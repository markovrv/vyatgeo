<template>
  <l-map v-if="geojson" style="height: 80vh" :zoom="zoom" :center="center" ref="myMap" @ready="mapReady" :crs="crs()">
    <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
    <l-polygon v-for="(poly, id) in geojson.features" :key="id" :lat-lngs="poly.geometry.coordinates" @mouseenter="showName(poly.properties.name)" @mouseleave="showName('')" :weight="getWeight(poly)" :color="getColor(poly)" :fillColor="getColor(poly)" @click="polyClick(poly)"></l-polygon>
    <l-control position="topright" >
      <button @click="clearFilter">Очистить выбор</button>
    </l-control>
      <div v-if="panel.text" :style="'left: ' + panel.x + 'px; top: ' + panel.y + 'px'" style="opacity: 0.8; position: absolute; z-index: 999999;border: 1px #aaa solid; background-color: white; padding: 3px; border-radius: 5px; box-shadow: 0.2em 0.2em 4px rgba(122,122,122,0.5);">{{panel.text}}</div>
  </l-map>
</template>

<script>
import {
    LMap, 
    LTileLayer, 
    LPolygon, 
    LControl
  } from 'vue2-leaflet';

import leaflet from "leaflet"

export default {
  components: {
    LMap,
    LTileLayer,
    LPolygon,
    LControl
  },
  props: ['geojson'],
  data () {
    return {
      // url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      url: 'https://core-renderer-tiles.maps.yandex.net/tiles?l=map&v=21.12.05-0-b211103130830&z={z}&x={x}&y={y}&scale=1&lang=ru_RU',
      attribution:
        '&copy; <a href="http://yandex.ru" target="_blank">Яндекс</a>',
      zoom: 6,
      center: [59, 49],
      panel: {
        text: '',
        x: -600,
        y: 0,
      }
    };
  },
  methods: {
    showName(name) {
      if( window.timer ) clearTimeout(window.timer);
      if (name == ''){
        this.panel.text = '';
      }
      else {
        window.timer = setTimeout(() => {
            this.panel.text = name;
        }, 300);
      }
    },
    clearFilter() {
      this.geojson.features.forEach((obj) => {
        obj.properties.selected = false
        this.$emit('clear-filter')
      })
    },
    polyClick(polygon) {
      if(polygon.properties.count){
        polygon.properties.selected = !polygon.properties.selected
        this.$emit('select', polygon)
      }
    },
    getColor(poly) {
      if(poly.properties.count){
        if (poly.properties.selected) return "red"
        return "green"
      }
      return '#000'
    },
    getWeight(poly) {
      if (poly.properties.selected) return 2
      return 1
    },
    mapReady() {
      // let map = this.$refs.myMap.mapObject;
    },
    crs() {
      return leaflet.CRS.EPSG3395;
    },
    mouseIsMoving(e) {
      this.panel.x = e.pageX + 10;
      this.panel.y = e.pageY + 20;
    },
  },
  mounted(){
    window.addEventListener('mousemove',this.mouseIsMoving);
  },
}
</script>
