<template>
  <div style="height: 100%">
    <div style="border-bottom: 1px solid black"><b>СЛОВА</b></div>
    <div style="overflow-y: scroll; height: calc(100% - 19px)">
      <div style="text-align: left">
        <div style="padding-left: 4px; cursor: pointer" v-for="(cat, id) in cats" :key="id">
          <div class="catname" @click="cat.opened = !cat.opened"><b>{{cat.name}}</b> <small style="font-size: x-small;float: right;">{{cat.count}}</small></div>
          <div v-show="cat.opened">
            <div class="catcontent" v-for="(word, id) in filteredWords(cat)" :key="id" @click="select(word)" @dblclick="regSelect(word)" :style="(selected == word)?'color: red':''">{{word.Name}}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ['words', 'cats', 'geojson'],
  data() {
    return {
      selected: null
    }
  },
  computed: {
    selectedRegions() {
      let regions = []
      this.geojson.features.forEach((obj) => {
        if (obj.properties['selected'])
          regions.push(obj.properties.name)
      })
      return regions
    },
  },
  methods:
  {
    filteredWords(cat) {
      let out = []
      if(this.selectedRegions.length == 0)
        out = this.words.filter(word => this.isInCat(word, cat))
      else
        out = this.words.filter(word => (this.isInWord(word, this.selectedRegions) && this.isInCat(word, cat)))
      cat.count = out.length
      return out
    },
    select(word) {
      this.selected = word
      this.$emit('select', word)
    },
    regSelect(word) {
      this.select(word)
      setTimeout(() => {
        this.geojson.features.forEach((obj) => {
          obj.properties.selected = (word[obj.properties.name] == 1)
        })
      }, 200);
    },
    isInWord(word, regions) {
      let out = false
      regions.forEach((reg)=>{
        if (word[reg]) out = true
      })
      return out
    },
    isInCat(word, cat) {
      return word.Category.indexOf(cat.name) !== -1
    }
  }
}
</script>

<style scoped>
  .catname { 
    background: rgb(106,106,106);
    background: linear-gradient(0deg, rgba(192, 192, 192, 0.685) 0%, rgba(255,255,255,1) 45%);
    padding: 5px;
  }
  .catcontent {
    background: linear-gradient(90deg, rgba(53, 53, 53, 0.468) 0%, rgba(255,255,255,1) 2%);
    padding-left: 7px; 
    cursor: pointer;
  }
</style>