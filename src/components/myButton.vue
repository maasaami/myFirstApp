<script setup>
import { ref } from 'vue'
const countOfClick = ref('')
</script>

<template>
<div id="myButton">
  <button @click="countOfClick++">clicked : {{countOfClick}} times</button>
  <p v-if="countOfClick">You've clicked {{countOfClick}} times!</p>
</div>
</template>

<style scoped>
#myButton {
		color: blue;
		text-align: center;
		padding: 10px;
} 
</style>