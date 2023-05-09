<template>
  <view>
    <view class="weather" :class="card?'weather-card weather-card_radius':''">
      <view class="weather__c" :class="card?'weather-card_radius':''">

        <view class="weather__c_head">
          <view class="weather__c_head_update">
            <view class="weather__c_head_update_icon xy_center" @click="updateBtn()" style="text-align: right">
              <oea-icon type="icofont" :name="update_icon"></oea-icon>
            </view>
          </view>
          <view class="weather__c_head_title" style="text-align: center;font-size: 45rpx;">
            <!-- {{province}} - {{city}} -->
			{{province}}
            {{city}}
			{{district}}
          </view>
        </view>
        
        <view class="xy_center" style="font-size: 120rpx;">
          {{weather.temp}}℃  
		  
        </view>
		<view class="xy_center" style="font-size: 40rpx;">
		  {{weather.text}} | {{air.category}}
		</view>
         
        <oea-separate top="25px"></oea-separate>
        
        <oea-grid style="color: #000; font-size: 30rpx" :column="4" :square="true" :highlight="false">
		  
		  <oea-grid-item>
            <view>
              <view class="xy_center" style="font-weight: bold;">
                相对湿度
              </view>
              <view class="xy_center" style="font-size: 28rpx">
				{{weather.humidity}}%
              </view>
            </view>
          </oea-grid-item>
		  <oea-grid-item>
		    <view>
		      <view class="xy_center" style="font-weight: bold;">
		        风向
		      </view>
		      <view class="xy_center" style="font-size: 28rpx">
		  		{{weather.windDir}}
		      </view>
		    </view>
		  </oea-grid-item>
		  <oea-grid-item>
		    <view>
		      <view class="xy_center" style="font-weight: bold;">
		        风力
		      </view>
		      <view class="xy_center" style="font-size: 28rpx">
		  		{{weather.windScale}}级
		      </view>
		    </view>
		  </oea-grid-item>
		  <oea-grid-item>
		    <view>
		      <view class="xy_center" style="font-weight: bold;">
		        能见度
		      </view>
		      <view class="xy_center" style="font-size: 28rpx">
		  		{{weather.vis}}km
		      </view>
		    </view>
		  </oea-grid-item>
        </oea-grid>
		
        <oea-separate top="20px"></oea-separate>
		<view style="color: aliceblue;font-weight: bold;font-size: 30rpx;">
			空气质量指标
		</view>
        <oea-grid style="background-color: lightskyblue;color: #ff5500;padding-top: 20px;padding-bottom: 0px;" :highlight="false" :column="6" :square="false">
			<oea-separate left="300rpx"></oea-separate>
			<oea-grid-item style="align-self: center;">
			  <view style="display: flex;justify-content: center;">
			    <view>						  
			        <view style="text-align: center;font-weight: bold;font-size: 30rpx;">AQI</view>
			        <view style="text-align: center;font-size: 28rpx;">{{air.aqi}}</view>
			    </view>
			  </view>
			</oea-grid-item>
		</oea-grid>
        <oea-grid style="background-color: lightskyblue;color: #000;padding-top: 10px;padding-bottom: 20px;" :highlight="false" :column="6" :square="false">
          
		  <oea-grid-item >
		    <view style="display: flex;justify-content: center;">
		      <view>
		          <view style="text-align: center;font-weight: bold;font-size: 28rpx;">PM10</view>
		          <view style="text-align: center;font-size: 25rpx;">{{air.pm10}}</view>
		      </view>
		    </view>
		  </oea-grid-item>
		  <oea-grid-item >
		    <view style="display: flex;justify-content: center;">
		      <view>
		          <view style="text-align: center;font-weight:bold;font-size: 28rpx;">PM2.5</view>
		          <view style="text-align: center;font-size: 25rpx;">{{air.pm2p5}}</view>
		      </view>
		    </view>
		  </oea-grid-item>
		  <oea-grid-item >
		    <view style="display: flex;justify-content: center;">
		      <view>
		          <view style="text-align: center;font-weight:bold;font-size: 28rpx;">NO2</view>
		          <view style="text-align: center;font-size: 25rpx;">{{air.no2}}</view>
		      </view>
		    </view>
		  </oea-grid-item>
		  <oea-grid-item >
		    <view style="display: flex;justify-content: center;">
		      <view>
		          <view style="text-align: center;font-weight:bold;font-size: 28rpx;">SO2</view>
		          <view style="text-align: center;font-size: 25rpx;">{{air.so2}}</view>
		      </view>
		    </view>
		  </oea-grid-item>
		  <oea-grid-item >
		    <view style="display: flex;justify-content: center;">
		      <view>
		          <view style="text-align: center;font-weight:bold;font-size: 28rpx;">CO</view>
		          <view style="text-align: center;font-size: 25rpx;">{{air.co}}</view>
		      </view>
		    </view>
		  </oea-grid-item>
		  <oea-grid-item >
		    <view style="display: flex;justify-content: center;">
		      <view>
		          <view style="text-align: center;font-weight:bold;font-size: 28rpx;">O3</view>
		          <view style="text-align: center;font-size: 25rpx;">{{air.o3}}</view>
		      </view>
		    </view>
		  </oea-grid-item>
        </oea-grid>
        
        <oea-separate top="5px"></oea-separate>
		
		<view style="color: aliceblue;font-weight: bold;font-size: 30rpx;">
			未来七天天气预报
		</view>	
		<view class="charts-box">
		    <qiun-data-charts 
		      type="line"
		      :opts="opts"
		      :chartData="chartData"
		    />
		</view>
		<oea-separate top="20px"></oea-separate>
        <view v-if="updateTime" style="font-size: 25rpx;display: flex;justify-content: center;margin-top: 20px;position: absolute;bottom: 0;right: 0;left: 0;color: black;">更新于{{updateTime}}</view>
      </view>
    </view>
  </view>
  
</template>

<script>

/**
 * @name weather
 * @description 利用 高德geo逆转换API接口实现的地理定位, 并通过和风天气API查询当前的天气, 以及空气质量
 * 
 * @property {Boolean} debug 打开 输出 内容到控制台
 * @property {Boolean} card 卡片模式 默认为 true
 * @property {String} gd_xy_api_url 高德逆转换API
 * @property {String} gd_key 获取地址：https://console.amap.com
 * @property {String} he_weather_api_url 和风天气weatherAPI
 * @property {String} he_weather7d_api_url 和风天气七天预测API
 * @property {String} he_air_api_url 和风天气airAPI
 * @property {String} he_key 获取地址：https://console.qweather.com
 * @property {String} update_icon 更新天气 的按钮的 形状
 * 
 */

export default {
  name: 'weather',
  data() {
    return {
		chartData: {},
		      //您可以通过修改 config-ucharts.js 文件中下标为 ['line'] 的节点来配置全局默认参数，如都是默认参数，此处可以不传 opts 。实际应用过程中 opts 只需传入与全局默认参数中不一致的【某一个属性】即可实现同类型的图表显示不同的样式，达到页面简洁的需求。
		      opts: {
		        color: ["#1890FF","#91CB74","#FAC858","#EE6666","#73C0DE","#3CA272","#FC8452","#9A60B4","#ea7ccc"],
		        padding: [15,10,0,15],
		        enableScroll: false,
		        legend: {},
		        xAxis: {
		          disableGrid: true
		        },
		        yAxis: {
		          gridType: "dash",
		          dashLength: 2
		        },
		        extra: {
		          line: {
		            type: "straight",
		            width: 2,
		            activeType: "hollow"
		          }
		        }
		      },
      province: '',
      city: '',
	  district: '',
      updateTime: '', // 更新时间
	  location: '', //经纬度
      weather: {
        temp:'',
		text:'',
		windDir:'',
		windScale:'',
		humidity:'',
		vis:''
      },
      air: {
		category:'',
	    aqi:'',
		pm10:'',
		pm2p5:'',
		no2:'',
		so2:'',
		co:'',
		o3:'',
	  }
    }
  },
  props: {
    // 用于测试 输出调试 信息
    debug: {
      type: Boolean,
      default: false
    },
    card: {
      type: Boolean,
      default: true
    },
    default_adcode: {
      type: String, 
      default: ''
    },
	// 高德经纬度逆转换API
	gd_xy_api_url: {
	  type: String,
	  default: 'https://restapi.amap.com/v3/geocode/regeo'
	},
	// 和风天气weatherAPI
    he_weather_api_url: {
      type: String,
      default: 'https://devapi.qweather.com/v7/weather/now'
    },
	// 和风天气预测API
	he_weather7d_api_url:{
	  type: String,
	  default: 'https://devapi.qweather.com/v7/weather/7d'
	},
	// 和风天气airAPI
	he_air_api_url: { 
	  type: String,
	  default: 'https://devapi.qweather.com/v7/air/now'
	},
    gd_key: {
      type: String,
      default: ''
    },
	he_key: {
	  type: String,
	  default: ''
	},
    update_icon: {
      type: String,
      default: 'ui-rotation'
    }
  },
  watch: {
	location() {
	  this.getAddress()
	  this.update()
	},
  },
  emits: ['forecasts', 'todayWeather', 'citySwitch'],
  mounted() {
	this.getLocation();
  },

  methods: {
	/**获取地图定位*/ 
    getLocation() { 
		let _this = this
		//手机用plus,电脑用navigator
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
    },
	getAddress(){
		let data = {}
		data.key = this.gd_key	
		data.location = this.location
		let _this = this
		if (this.debug) console.log({
		  name: 'uni.request',
		  data: data
		})
		uni.request({
		  url: this.gd_xy_api_url,
		  data,
		  success: (e) => {
		    // 1
		    if (this.debug) console.log(e.data)
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
			  _this.city_adcode = address.adcode
		    }
		  },
		  fail: (e) => {
		    this.showToast('网络问题')
		    console.log(e)
		  }
		})
	},
    log(e) {
      console.log(e)
    },
    citySwitchBtn() {
      this.$emit('citySwitch')
    },
    updateBtn() {
      this.update()
	  this.getLocation()
      this.showToast('刷新成功')
    },
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
          // 1
		  console.log(e)
          if (this.debug) console.log(e.data)
          if (!this.check('obj', e.data)) {
            this.showToast('请检查url接口是否正确')
            return
          }
          if (!this.check('state', e.data)) return
          // 2
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
	  // 天气预报
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
			    categories: [daily[0].fxDate.substr(5),daily[1].fxDate.substr(5),daily[2].fxDate.substr(5),daily[3].fxDate.substr(5),daily[4].fxDate.substr(5),daily[5].fxDate.substr(5),daily[6].fxDate.substr(5)],
			    series: [{
					name: "最低温",
					data: [daily[0].tempMin,daily[1].tempMin,daily[2].tempMin,daily[3].tempMin,daily[4].tempMin,daily[5].tempMin,daily[6].tempMin]
				},
				{
			        name: "最高温",
			        data: [daily[0].tempMax,daily[1].tempMax,daily[2].tempMax,daily[3].tempMax,daily[4].tempMax,daily[5].tempMax,daily[6].tempMax]
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
	      // 1
	  		  console.log(e)
	      if (this.debug) console.log(e.data)
	      if (!this.check('obj', e.data)) {
	        this.showToast('请检查url接口是否正确')
	        return
	      }
	      if (!this.check('state', e.data)) return
	      // 2
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
    },
    //  做基本的检查
    check(type = 'all', data=null) {
      switch(type) {
        case 'key': {
          if (this.gd_key == '') {this.showToast('请输入 key');return false}
        };break;
        case 'gd_state': {
          if (data.status != '1') {if(this.debug)console.log(data); this.showToast('错误的服务器返回值:'+data.info);return false}
        };break;
		case 'he_state': {
		  if (data.code != '200') {if(this.debug)console.log(data); this.showToast('错误的服务器返回值:'+data.info);return false}
		}
      }
      return true
    },
    showToast(title) {
      uni.showToast({
        icon:"none",
        title:title
      })
    }
  }
}
</script>

<style  lang="scss">
  .charts-box {
    width: 100%;
    height: 300px;
	background-color: #0AC4EC;
  }
  .weather {
    min-height: 100px;
    background-color: white;
  
    &__c {
      position: relative;
      min-height: 180px;
      color: #FFFFFF;
      font-size: 14px;
      background: linear-gradient(15deg, #0F65DB, #0A8FEC, #0AC4EC);
      
      &_head {
        height: 50px;
        font-size: 20px;
        font-weight: bold;
        display: flex;
        align-items: center;
        &_title {
          width: 80%;
          display: flex;
          justify-content: center;
        }
        &_update {
          width: 10%;
          display: flex;
          justify-content: flex-end;
          padding-right: 12px;
          &_icon {
            width: 22px;
            height: 22px;
            border-radius: 8px;
          }
          &_icon:active {
            background: darkgray !important;
          }
        }
      }      
      &_temperature {
        display: flex;
        justify-content: center;
        align-items: center;
      }  
      
    }
  }
  
  // 卡片模式
  .weather-card {
    // border-radius: 8px;
    margin: 8px;
    box-shadow: 5px 5px 5px #b3b3b3;
  }
  // 圆角
  .weather-card_radius {
    border-radius: 8px;
  }
  
  // 居中
  .xy_center {
    display: flex;
    justify-content: center;
    align-items: center;
  }
</style>
