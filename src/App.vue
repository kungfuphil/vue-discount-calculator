<script setup lang="ts">
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://v3.vuejs.org/api/sfc-script-setup.html#sfc-script-setup
import DiscountCalculator from './components/DiscountCalculator.vue'
</script>

<template>
  <DiscountCalculator />
</template>

<style>

body {
    font-family: 'Open Sans', sans-serif;
    margin: 0;
    padding: 0;
    color: rgb(39, 39, 39);
    background-color: rgb(209, 231, 255);
}

h1, h2 {
  text-align: center;
}

main {
  width: 80%;
  margin: auto;
  background-color: white;
  padding: 1.5rem;
  border-radius: 8px;
  box-shadow: 2px 4px 8px rgba(0, 0, 0, 0.2);
}

.form-control,
.form-control-overflow {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.form-control label,
.form-control-overflow label {
  flex-grow: 1;
}

.form-control input,
.form-control-overflow input {
  flex-grow: 2;
  margin: 0.5rem;
}

button {
  background-color: royalblue;
  color: white;
  border-radius: 5px;
  border: 1px solid royalblue;
  margin: 0 .5rem;
  padding: 0.5rem;
  cursor: pointer;
}

button:hover {
  background-color: rgb(18, 70, 224);
}
</style>
