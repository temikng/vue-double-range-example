<template>
  <div class="container" ref="container">
    <div class="slider" name="slider" ref="slider">
      <div class="zoom-char zoom-char--left" name="left"></div>
      <div class="zoom-char zoom-char--right" name="right"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'VueDoubleRange',
  props: {
    value: Object,
    min: Number,
    max: Number,
    step: Number
  }
}
</script>

<style scoped>
  .container {
    position: relative;
    height: 20px;
    background-color: #cecece;
    border: 1px solid #9a9a9a;
    margin: 10px 18px;
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
  }
  .slider {
    cursor: -webkit-grab;
    position: absolute;
    height: 100%;
    background-color: #00000080;
  }
  .slider--grabbing {
    cursor: -webkit-grabbing;
  }
  .zoom-char {
    background-image: url('../assets/images/vue-double-range/dragIconRoundBig.svg');
    cursor: ew-resize;
    position: absolute;
    width: 35px;
    height: 35px;
    top: -8px;
  }
  .zoom-char--left {
    left: -18px;
  }
  .zoom-char--right {
    right: -18px;
  }
</style>
