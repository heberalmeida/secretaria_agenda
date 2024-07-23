<template>
  <div id="app">
    <h1>ADMCG</h1>
    <CalendarComponent />
  </div>
</template>

<script>
import CalendarComponent from './components/CalendarComponent.vue';

export default {
  name: 'App',
  components: {
    CalendarComponent
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  color: #2c3e50;
  height: 100vh;
  width: 100vw;
  margin: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
</style>
