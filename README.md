Weather_App

XDU B测雾霾探测系统作品

> 本实验源代码：[bojianzhu/btest-weather (github.com)](https://github.com/bojianzhu/Btest-weather)



# 1、任务分析
本次任务要求设计一款手机端雾霾app探测系统，需要满足以下三个功能：
1.定位功能：获取当前定位城市信息，并显示在客户端。
 2.界面设计：包含显示天气详情和空气质量指数的动态显示。
3.天气详情和空气质量指数：通过和风天气、墨迹天气、我的天气等均可获取，百度地图、高德地图等可以辅助位置信息。

# 2、系统方案
## 2.1 app实现方案
app实现方案大致有三种：
1.	使用html进行网页开发，手机通过浏览器的方式进行访问。虽然这能够解决不同机型和操作系统的适配问题，但不是真正的app。
2.	使用uniapp将网页开发成果在手机端进行部署，和网页开发方式类似，跨平台能力强，开源社区活跃，组件丰富，用户体验佳，开发潜力大。
3.	使用Android原生开发，选择Android Studio作为编辑器，这样能够在Android操作系统上获得最佳的性能表现。不足之处在于无法适用于Apple的操作系统。
综合上述考虑，我们考虑到用户体验和跨平台使用，因此，我们选择了uniapp开发。
## 2.2 app定位方案
uniapp定位实现方案大致有两种：
1.	GPS定位。该方案需要用户打开GPS定位模块，该方案准确度较高。
2.	网络定位。该方案通过用户的网络ip进行定位，但准确率不高。

综合上述考虑，我们选择使用H5中的函数获取手机GPS定位，再通过获得的经纬度调用高德地图提供的地理逆编码API获取详细地理位置

## 2.3 天气查询实现方案
天气数据可以通过各种天气网站进行获取。我们选择了和风天气的数据接口。
# 3、系统实现
## 3.1 获取GPS经纬度信息
   为了获取用户的定位信息，我们需要用户在首次启动时，对GPS调用权限进行授权。具体实现如下。

```javascript
getLocation() { 
	let _this = this
	plus.geolocation.getCurrentPosition(onSucess,function(error){
		switch (error.code) {
			case error.PERMISSION_DENIED:
				x.innerHTML = "用户拒绝对获取地理位置的请求。"
				break;
			case error.POSITION_UNAVAILABLE:
				x.innerHTML = "位置信息是不可用的。"
				break;
			case error.TIMEOUT:
				x.innerHTML = "请求用户地理位置超时。"
				break;
			case error.UNKNOWN_ERROR:
				x.innerHTML = "未知错误。"
				break;
		}
	});
	function onSucess(position){
		_this.location = String(position.coords.longitude) + ',' + String(position.coords.latitude)		
	}
}
```

## 3.2 获取详细地理位置
我们采用高德地图的API来实现定位功能，主要步骤如下：
1.	在百度地图开放平台申请账号并新建应用。
2.	创建项目获取key
3.	通过调用高德地图地理逆转换API获得详细地址：*https://restapi.amap.com/v3/geocode/regeo*

具体实现如下

```javascript
getAddress(){
	let data = {}
	data.key = this.gd_key	
	data.location = this.location
	let _this = this
	uni.request({
		url: this.gd_xy_api_url,
		data,
		success: (e) => {
		    if (!this.check('obj', e.data)) {
		        this.showToast('请检查url接口是否正确')
		        return
		    }
		    if (this.check('state', e.data)) {
		        console.log(e)
			    let address = e.data.regeocode.addressComponent
			    _this.province = address.province
			    _this.city = address.city
			    _this.district = address.district
		    }
		  },
		fail: (e) => {
		    this.showToast('网络问题')
		    console.log(e)
		}
	})
}
```

## 3.3 天气查询功能实现
我们采用和风天气的接口来查询天气数据，空气质量数据以及一周内的温湿度数据，我们首先在和风天气App注册账号，然后获取网站提供的免费key值，最后就能调用接口。
接口地址如下：

- 实时天气数据接口：
  *https://devapi.qweather.com/v7/weather/now*

- 天气预报接口

  *https://devapi.qweather.com/v7/weather/7d*

- 空气质量数据接口：
*https://devapi.qweather.com/v7/air/now*

有了接口之后，我们需要在App内发起网络请求。
核心代码如下：

```javascript
update() {
	if (this.debug) console.log({
        key: this.he_key,
        location: this.location,
     	})
    if (!this.check('key')) return
	// 天气
    uni.request({
        url: this.he_weather_api_url,
        data: {
          	key: this.he_key,
          	location: this.location,
        },
        success: (e) => {
		  	console.log(e)
          	if (this.debug) console.log(e.data)
          	if (!this.check('obj', e.data)) {
            	this.showToast('请检查url接口是否正确')
            	return
          	}
          	if (!this.check('state', e.data)) return
         	this.weather.temp = e.data.now.temp
		 	this.weather.text = e.data.now.text
		  	this.weather.windDir = e.data.now.windDir
		  	this.weather.windScale = e.data.now.windScale
		  	this.weather.humidity = e.data.now.humidity
		  	this.weather.vis = e.data.now.vis
		  	this.updateTime = e.data.updateTime
        },
        fail: (e) => {
          	this.showToast('网络问题')
          	console.log(e)
        }
    })
    // 天气预测
	uni.request({
		url: this.he_weather7d_api_url,
		data: {
			key: this.he_key,
			location: this.location,
		},
		success: (e) => {
			// 1
			console.log(e)
			if (!this.check('obj', e.data)) {
				this.showToast('请检查url接口是否正确')
				return
			}
			if (!this.check('state', e.data)) return
			// 2
			let daily = e.data.daily;
			let res = {
			    categories：[daily[0].fxDate.substr(5),daily[1].fxDate.substr(5),daily[2].fxDate.substr(5),daily[3].fxDate.substr(5),daily[4].fxDate.substr(5),daily[5].fxDate.substr(5),daily[6].fxDate.substr(5)],
			    series: [{
			        name: "最高温",
			        data: [daily[0].tempMax,daily[1].tempMax,daily[2].tempMax,daily[3].tempMax,daily[4].tempMax,daily[5].tempMax,daily[6].tempMax]
			    },
				{
					name: "最低温",
					data: [daily[0].tempMin,daily[1].tempMin,daily[2].tempMin,daily[3].tempMin,daily[4].tempMin,daily[5].tempMin,daily[6].tempMin]
				}]
			};
			this.chartData = res;
		}
	})
	// 空气质量
	uni.request({
	    url: this.he_air_api_url,
	    data: {
	      key: this.he_key,
	      location: this.location,
	    },
	    success: (e) => {
	  		console.log(e)
	      	if (this.debug) console.log(e.data)
	      	if (!this.check('obj', e.data)) {
	        	this.showToast('请检查url接口是否正确')
	        	return
	      	}
	      	if (!this.check('state', e.data)) return
	      	this.air.category = e.data.now.category
	      	this.air.aqi = e.data.now.aqi
	      	this.air.pm10 = e.data.now.pm10
	      	this.air.pm2p5 = e.data.now.pm2p5
	      	this.air.no2 = e.data.now.no2
	      	this.air.so2 = e.data.now.so2
	      	this.air.co = e.data.now.co
	      	this.air.o3 = e.data.now.o3
	    },
	    fail: (e) => {
	      this.showToast('网络问题')
	      console.log(e)
	    }
	})
}
```
## 3.5 系统界面设计
系统主界面我们主要参考了手机自带的天气App的设计布局，使用了Vue框架和开源UI组件，大大提高了开发的效率，也提高了用户的体验效果。
# 4、结果分析
  在实机上，我们的界面展示如下：

<img src=".\README.assets\9ea857fe48a07fd11990665d0f776ba.jpg" alt="9ea857fe48a07fd11990665d0f776ba" style="zoom: 25%;" />

<img src="D:\MyWork\学业\大三下\B测\weatherAPP\README.assets\71d3ad2dd83281139655605f14fdbb0.jpg" alt="71d3ad2dd83281139655605f14fdbb0" style="zoom: 25%;" />

# 参考资料

[Android 天气APP（一）开发准备_android天气_初学者-Study的博客-CSDN博客](https://llw-study.blog.csdn.net/article/details/105204552)

[HTML5 简介 (w3school.com.cn)](https://www.w3school.com.cn/html/html5_intro.asp)

[uni-app官网 (dcloud.net.cn)](https://uniapp.dcloud.net.cn/)

[地理/逆地理编码-API文档-开发指南-Web服务 API|高德地图API (amap.com)](https://lbs.amap.com/api/webservice/guide/api/georegeo)

[实时天气 for API | 和风天气开发服务 (qweather.com)](https://dev.qweather.com/docs/api/weather/weather-now/)

[每日天气预报 for API | 和风天气开发服务 (qweather.com)](https://dev.qweather.com/docs/api/weather/weather-daily-forecast/)

[实时空气质量 for API | 和风天气开发服务 (qweather.com)](https://dev.qweather.com/docs/api/air/air-now/)

[演示 - uCharts跨平台图表库](https://www.ucharts.cn/v2/#/demo/index)
