<template>
  <div id="app">
    <div style="width: 100vw;">
      <div style="float:left; width: 80vw">
        <osm :geojson="geojson" />
      </div>
      <div style="float:right; height: 80vh; width: 20vw">
        <panel v-if="words && cats && geojson" :words="words" :cats="cats" :geojson="geojson" @select="selectWord" />
      </div>
    </div>
    <div style="width: 100vw; height: 20vh">
      <div style="float:left; width: 80vw; height: 20vh">
        <info v-if="words" :word="selectedWord"/>
      </div>
      <div style="float:right; width: 20vw; height: 20vh;">
        <pic v-if="words" :word="selectedWord" />
      </div>
    </div>
  </div>
</template>

<script>
import osm from './components/osm.vue'
import info from './components/info.vue'
import pic from './components/pic.vue'
import panel from './components/panel.vue'

export default {
  name: 'App',
  components: {
    osm, info, pic, panel
  },
  data() {
    return {
      geojson: null,
      words: null,
      cats: null,
      selectedWord: null,
    }
  },
  methods: {
    selectWord(word) {
      this.selectedWord = word
    }
  },
  async created () {
    console.log("загрузка регионов")
    const resp2 = await fetch('/data/regions.geojson?15122021');
    let geojson = await resp2.json();
    geojson.features.forEach((obj) => {
      obj.properties['selected'] = false
      obj.properties['count'] = 0
    })
    this.geojson = geojson;
    console.log("загрузка слов")
    const resp1 = await fetch('/data/text.json?15122021_1');
    let words = await resp1.json();
    this.words = words;
    console.log("загрузка категорий")
    const resp3 = await fetch('/data/cats.json?15122021');
    let cats = await resp3.json();
    cats.forEach((obj) => {
      obj['opened'] = false
      obj['count'] = 0
    })
    this.cats = cats
    console.log("подготовка")
    this.words.forEach((word) => {
      this.geojson.features.forEach((reg) => {
        if(word[reg.properties.name])
        reg.properties.count ++
      })
    })
    console.log("ГОТОВО")
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
