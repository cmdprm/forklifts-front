<template>
  <home-view />
</template>

<script>
import HomeView from "@/views/HomeView.vue";

export default {
  name: "App",
  components: {
    HomeView,
  },
};
</script>

<style>
body {
  background: #f2f2f2;
}

#app {
  font-family: Helvetica, Arial, sans-serif;
  padding: 20px 30px;
}
</style>
