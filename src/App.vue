<template>
  <div id="app">
    <!-- <ThreeComponent v-if="!isSafari" />
    <ARQuickLookComponent v-if="isSafari" /> -->
    <ZapWorksComponent />
  </div>
</template>

<script lang="ts" setup>
import ARComponent from './components/ARComponent.vue'
import ThreeComponent from './components/ThreeComponent.vue'
import ARQuickLookComponent from './components/ARQuickLookComponent.vue'
import ZapWorksComponent from './components/ZapWorksComponent.vue';
// const isSafari = /^((?!chrome|android).)*safari/i.test(navigator.userAgent);
</script>

<style >
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html,
body,
#app {
  height: 100%;
}

img {
  max-width: 100%;
  object-fit: cover;
}
</style>
