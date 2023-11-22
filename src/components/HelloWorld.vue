<script setup>
import { ref, } from 'vue';

defineProps({
  msg: String,
})

const toggle = ref(false);
</script>

<template>
  <div class="card">
    <button type="button" @click="toggle = !toggle">Click me</button>
    <p v-if="toggle">Halooo</p>
  </div>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
