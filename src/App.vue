<template>
  <div id="app">
    <h1>Example vue-double-range</h1>

    <div>
      <label>From:
        <input type="number"
               v-model="slider.from"
               :min="initData.slider.min" :max="slider.to"
               :step="initData.slider.step"
               @keydown="onKeyDownInputSlider"/>
      </label>
      <label>To:
        <input type="number"
               v-model="slider.to"
               :min="slider.from" :max="initData.slider.max"
               :step="initData.slider.step"
               @keydown="onKeyDownInputSlider"/>
      </label>
    </div>

    <vue-double-range
      :min="initData.slider.min"
      :max="initData.slider.max"
      :step="initData.slider.step"
      v-model="slider"
      @input="onSliderChange" @update="onSliderUpdate"></vue-double-range>

    <div class="info">
      On move data:
      <pre class="info-code">{{dynamicSlider}}</pre>
      On the end of move data:
      <pre class="info-code">{{slider}}</pre>
    </div>
  </div>
</template>

<script>
import VueDoubleRange from './components/VueDoubleRange.vue';

const AVAILABLE_KEY_CODES = [9, 35, 36, 37, 38, 39, 40];
const DEFAULT_SLIDER_DATA = {
  from: 150000,
  to: 500000
};

export default {
  name: 'App',
  components: {
    VueDoubleRange
  },
  data () {
    return {
      initData: {
        slider: {
          min: 1000,
          max: 1000000,
          step: 1000
        }
      },
      slider: DEFAULT_SLIDER_DATA,
      dynamicSlider: DEFAULT_SLIDER_DATA
    }
  },
  methods: {
    onKeyDownInputSlider (event = window.event) {
      const keyCode = (event.which) ? event.which : event.keyCode;
      if (AVAILABLE_KEY_CODES.indexOf(keyCode) >= 0) return true;
      event.preventDefault();
      return false;
    },
    onSliderChange (event) {
      console.log('onSliderChange:event', event);
    },
    onSliderUpdate (event) {
      this.dynamicSlider = event;
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
h1 {
  text-align: center;
}
.info {
  margin-top: 25px;
}
.info-code {
  background-color: #cecece;
  border: 1px solid #9a9a9a;
  padding: 8px;
}
</style>
