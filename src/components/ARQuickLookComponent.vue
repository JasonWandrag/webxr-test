<template>
    <a class="ar-btn" rel="ar" :href="modelURL">
        <img :src="modelImg" :alt="modelType + ' model'">
        <!-- view AR -->
        View the {{modelType}}
    </a>
    
</template>
    
<script setup lang='ts'>

const modelType = "Dinomei"
const modelURL = `https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/${modelType}.usdz`
const modelImg = `https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/${modelType}.jpg`
</script>
    
<style scoped>
    .ar-btn{
        position: fixed;
        bottom: 20px;
        left: 50%;
        transform: translateX(-50%);
    }
</style>