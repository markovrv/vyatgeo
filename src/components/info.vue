<template>
  <div style="height: 100%">
    <!-- <div style="border-bottom: 1px solid black"><b>ОПИСАНИЕ</b></div> -->
    <div v-if="word" class="wordRaw">
      <i>{{ word.RAW }}</i><br>
      <b>Категория</b>: {{ word.Category }}
    </div>
  </div>
</template>
<script>
export default {
  props: ['word'],
}
</script>

<style scoped>
 .wordRaw{
    width: calc(100% - 20px);
    height: calc(100% - 20px);
    padding: 5px;
    margin: 5px;
    display: inline-block;
    vertical-align: center;
    border: 1px solid #f00;
    text-align: center;
  }
</style>

