<template>
  <div id="app">
    <h1>Example vue-double-range</h1>

    <vue-double-range
      :min="initData.slider.min"
      :max="initData.slider.max"
      :step="initData.slider.step"
      v-model="slider"></vue-double-range>
  </div>
</template>

<script>
import VueDoubleRange from './components/VueDoubleRange.vue';

export default {
  name: 'App',
  components: {
    VueDoubleRange
  },
  data () {
    const defaultSliderData = {
      from: 150000,
      to: 500000
    };
    return {
      initData: {
        slider: {
          min: 1000,
          max: 1000000,
          step: 1000
        }
      },
      slider: defaultSliderData,
      dynamicSlider: defaultSliderData
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin: 60px 260px 0;
}
</style>
