<template>
  <div id="container" class="absolute w-full h-full"></div>
  <div class="absolute top-8 left-8 p-2 bg-white border rounded">
    <label for="search-input" class="mx-2">搜索</label>
    <input id="search-input" class="border px-2" placeholder="请输入关键字" />
  </div>
</template>

<script lang="ts" setup>
import * as AMapLoader from "@amap/amap-jsapi-loader"
import "@amap/amap-jsapi-types"

const amapKey = import.meta.env.VITE_AMAP_KEY
const amapSecret = import.meta.env.VITE_AMAP_SECRET

let map: AMap.Map | null = null

let selfMarker: AMap.Marker | null = null

window._AMapSecurityConfig = {
  securityJsCode: amapSecret
}

function initMap() {
  AMapLoader.load({
    key: amapKey, // 申请好的Web端开发者Key，首次调用 load 时必填
    version: "2.0", // 指定要加载的 JSAPI 的版本，缺省时默认为 1.4.15
    plugins: ["AMap.ToolBar", "AMap.Scale", "AMap.AutoComplete", "AMap.PlaceSearch"] // 需要使用的的插件列表，如比例尺'AMap.Scale'等
  })
    .then((AMap) => {
      map = new AMap.Map(
        "container", //设置地图容器id
        { resizeEnable: true }
      )

      // 添加控件
      map!.addControl(new AMap.ToolBar())
      map!.addControl(new AMap.Scale())

      const auto = new AMap.AutoComplete({ input: "search-input" })
      const placeSearch = new AMap.PlaceSearch({
        map: map
      })  //构造地点查询类
      auto.on("select", select)//注册监听，当选中某条记录时会触发
      function select(e: any) {
        placeSearch.setCity(e.poi.adcode)
        placeSearch.search(e.poi.name)  //关键字查询查询
      }

      // 绑定事件
      map!.on("click", onClickMap)
    })
    .catch((e) => {
      console.log(e)
    })
}

// 当点击地图时，在点击的位置添加标记
function onClickMap(e: any) {
  if (!map) {
    return
  }

  if (selfMarker) {
    map.remove(selfMarker)
  }

  selfMarker = new AMap.Marker({
    position: e.lnglat
  })

  map.add(selfMarker)
}

onMounted(() => {
  initMap()
})

onUnmounted(() => {
  map?.destroy()
})
</script>
