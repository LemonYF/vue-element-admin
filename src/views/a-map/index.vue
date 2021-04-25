<template>
  <div class="app-container">
    <el-input v-model="address" placeholder="请输入查询地点" @keydown.enter.native="searchPlace()" />
    <div id="container" class="map-show" />
    <el-button type="primary" @click="searchMap()">清空地址栏</el-button>
  </div>
</template>

<script>
import AMap from 'AMap'

export default {
  name: 'Guide',
  data() {
    return {
      address: '', // 获取的位置
      zoom: 4, // 地图缩放
      center: [111.59996, 37.197646], // 初始中心
      lng: 0, // 经纬度
      lat: 0,
      loaded: false,
      // 点击显示的标记默认的定位
      markers: [],
      map: null,
      contextMenuPositon: null,
      userList: [
        {
          id: 1,
          name: 'hahah',
          position: [112.140713, 31.961778]
        }, {
          id: 2,
          name: 'hahah2',
          position: [112.140913, 31.961778]
        }, {
          id: 3,
          name: 'hahah3',
          position: [112.141713, 31.967778]
        }, {
          id: 4,
          name: 'hahah4',
          position: [112.140213, 31.9615778]
        }
      ]
    }
  },
  mounted() {
    this.bindEventListening()
    this.getPosition()
  },
  methods: {
    bindEventListening() { // init时创建对象及绑定监听事件
      const _this = this // 将this绑定至 _this,不绑定的话，事件监听没办法使用
      this.map = new AMap.Map('container', {
        resizeEnable: true,
        viewMode: '2D', // 设置地图模式
        lang: 'zh_cn' // 设置地图语言类型
      })

      const contextMenu = new AMap.ContextMenu()
      // 地图绑定鼠标右击事件——弹出右键菜单
      this.map.on('rightclick', function(e) {
        contextMenu.open(_this.map, e.lnglat)
        _this.contextMenuPositon = e.lnglat
      })
      // 右键显示全国范围
      contextMenu.addItem('缩放至全国范围', function(e) {
        _this.map.setZoomAndCenter(4, [108.946609, 34.262324])
      }, 2)
      //
      // 右键添加Marker标记
      contextMenu.addItem('添加标记', function(e) {
        // const num = _this.markers.length
        const marker = new AMap.Marker({
          map: _this.map,
          icon: 'https://webapi.amap.com/theme/v1.3/markers/n/mark_b.png',
          // content: '<div class="mark">' + _this.markers.length + '</div>',
          position: _this.contextMenuPositon // 基点位置
        })
        marker.on('click', e => {
          console.log(e)
          _this.getClickUserInfo(e)
        })
        _this.markers.push(marker)
      }, 3)

      this.changeCenter(this.map) // init时直接调用方法绑定事件
    },
    searchMap() {
      this.address = ''
      this.tishi = ''
    },
    searchPlace() {
      console.log(222)
    },
    getClickUserInfo(e) { // 点击mark标记，获取对应详细信息
      console.log('you have clicked user', e.lnglat)
    },
    // getLocation() {
    //
    // },
    changeCenter(map, location) {
      const _this = this
      map.on('click', function(e) {
        console.log(111)
        _this.map.setCenter(e.lnglat)
      })
    },
    getPosition() {
      this.map.plugin('AMap.Geolocation', function() {
        const geolocation = new AMap.Geolocation({
          // 是否使用高精度定位，默认：true
          enableHighAccuracy: true,
          // 设置定位超时时间，默认：无穷大
          timeout: 10000
        })
        // console.log(map.addControl(geolocation))
        geolocation.getCurrentPosition()
        AMap.event.addListener(geolocation, 'compconste', onCompconste)
        AMap.event.addListener(geolocation, 'error', onError)

        function onCompconste(data) {
          console.log(data)
          // data是具体的定位信息
        }

        function onError() {
          // 定位出错
        }
      })
      // 实时路况图层
      const trafficLayer = new AMap.TileLayer.Traffic({
        zIndex: 10
      })
      this.map.add(trafficLayer) // 添加图层到地图
    }
  }
}
</script>

<style scoped lang="scss">
  .app-container {
    .map-show{
      width: 100%;
      height: 700px;
    }
    .mark{
      width:19px;
      height: 31px;
      color:white;
      text-align: center;
      line-height: 21px;
      background: url('https://webapi.amap.com/theme/v1.3/markers/n/mark_b.png');
    }
  }

</style>
