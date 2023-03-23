<script setup lang="ts">
import LowCodePlatform from "./LowCodePlatform.vue";
</script>

<template>
  <div class="home">
    <div class="home-title">低代码拖拽平台</div>
    <LowCodePlatform></LowCodePlatform>
  </div>
</template>

<style scoped>
.home {
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-top: 60px;
}
.home-title {
  text-align: center;
  font-size: 26px;
  font-weight: 700;
}
</style>
