<script setup>
import HomeView from './components/HomeView.vue';
</script>

<template>
  <HomeView />
</template>


<script>
import HomeView from "./components/HomeView.vue";

export default {
  name: "App",
  components: {
    HomeView,
  },
};
</script>

<style>
/* Global styles can go here */
</style>
