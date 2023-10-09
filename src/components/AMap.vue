<template>
  <div id="container" class="w-full h-full"></div>
</template>

<script lang="ts" setup>
import * as AMapLoader from "@amap/amap-jsapi-loader"
import "@amap/amap-jsapi-types"

const amapKey = import.meta.env.VITE_AMAP_KEY

let map: AMap.Map | null = null

let selfMarker: AMap.Marker | null = null

// 当点击地图时，在点击的位置添加标记
const clickHandler = function (e: any) {
  if (!map) {
    return
  }

  if (selfMarker) {
    map.remove(selfMarker)
  }

  selfMarker = new AMap.Marker({
    position: e.lnglat,
  })

  map.add(selfMarker)
}

function initMap() {
  AMapLoader.load({
    key: amapKey, // 申请好的Web端开发者Key，首次调用 load 时必填
    version: "2.0", // 指定要加载的 JSAPI 的版本，缺省时默认为 1.4.15
    plugins: [""], // 需要使用的的插件列表，如比例尺'AMap.Scale'等
  })
    .then((AMap) => {
      map = new AMap.Map(
        "container", //设置地图容器id
        {
          viewMode: "3D", //是否为3D地图模式
          zoom: 5, //初始化地图级别
          center: [105.602725, 37.076636], //初始化地图中心点位置
        },
      )

      // 绑定事件
      map!.on("click", clickHandler)
    })
    .catch((e) => {
      console.log(e)
    })
}

onMounted(() => {
  initMap()
})

onUnmounted(() => {
  map?.destroy()
})
</script>
