<template>
  <DigitalHuman></DigitalHuman>
  <WebkitSpeech></WebkitSpeech>
</template>

<script>
import DigitalHuman from './components/DigitalHuman.vue';
import WebkitSpeech from './components/WebkitSpeech.vue';

export default {
  name: 'App',
  components: {
    DigitalHuman,
    WebkitSpeech
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
