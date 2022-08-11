<!-- page2 -->
<script setup>
import { useRoute } from 'vue-router'
const route = useRoute()
const id = route.query.id

</script>

<template>
  <div class="page2">
    hello world page2-id:{{id}}
  </div>
</template>

<style lang='scss' scoped>
.page2{
  font-size: 26px;
  text-align: center;
}
</style>