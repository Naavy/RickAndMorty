<template>
  <RMDashboard />
</template>

<script lang="ts">
import RMDashboard from './components/RMDashboard.vue';

export default ({
  name: 'App',
  components: {
    RMDashboard,
  },
});
</script>

<style lang="scss">
@import "@/styles/variables.scss";

body {
  margin: 0;
  font-family: 'Poppins', sans-serif;
  color: $primary-color;
  font-size: 16px;
}
</style>