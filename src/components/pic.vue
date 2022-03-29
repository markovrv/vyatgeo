<template>
  <div class="wordRaw">
    <b v-if="word">{{word.Name}}</b>
    <b v-else>Выберите слово</b>
  </div>
</template>
<script>
export default {
  props: ['word'],
}
</script>

<style scoped>
 .wordRaw{
    width: calc(100% - 24px);
    height: calc(100% - 74px);
    padding: 5px;
    padding-top: 56px;
    margin: 5px;
    display: inline-block;
    vertical-align: center;
    border: 2px solid black;
    text-align: center;
  }
</style>

