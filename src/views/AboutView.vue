<template>
  <div class="about">
    <h1>This is an about page</h1>
    <button @click="handleClick">Click me</button>
  </div>
</template>

<script setup lang="ts">
const handleClick = () => {
  alert("Button clicked");
};
</script>

<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
}
</style>
