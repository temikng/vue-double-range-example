<template>
  <div class="container" ref="container">
    <div class="slider" name="slider"
         ref="slider"
         :style="{ left: inner.left + 'px', width: inner.width + 'px' }"
         @mousedown="onMouseDown">
      <div class="zoom-char zoom-char--left" name="left"
           ref="left"
           @mousedown="onMouseDown"></div>
      <div class="zoom-char zoom-char--right" name="right"
           ref="right"
           @mousedown="onMouseDown"></div>
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
  },
  data () {
    return {
      isMouseDown: false,
      movedItem: null,
      tmpValue: {
        from: 0,
        halfFrom: 0,
        to: 0,
        halfTo: 0
      },
      width: {
        container: 0,
        step: 0,
        halfStep: 0
      },
      mouse: {
        startX: 0
      }
    }
  },
  mounted () {
    // check available slider bounded
    if (this.step <= 0) {
      this.step = 1;
    }
    let value = this.value;
    if (value.from < this.min) {
      this.$emit('input', { from: this.min, to: value.to });
    }
    if (value.to > this.max) {
      this.$emit('input', { from: value.from, to: this.max });
    }
    if (value.from > value.to) {
      this.$emit('input', { from: this.min, to: this.max });
    }
    // add events
    window.addEventListener('resize', this.onWindowResize);
    document.addEventListener('mouseup', this.onMouseUp);
    document.addEventListener('mousemove', this.onMouseMove);
    // init width data
    this.onWindowResize();
  },
  beforeDestroy () {
    // remove added events
    window.removeEventListener('resize', this.onWindowResize);
    document.removeEventListener('mouseup', this.onMouseUp);
    document.removeEventListener('mousemove', this.onMouseMove);
  },
  computed: {
    inner () {
      let { from, to } = this.getValue();
      let left = this.width.step * ((from - this.min) / this.step);
      let width = (this.width.step * ((to - this.min) / this.step)) - left;
      return {
        left,
        width
      };
    }
  },
  methods: {
    getValue () {
      return {
        from: (Number(this.value.from) + this.tmpValue.from + this.tmpValue.halfFrom) || this.min,
        to: (Number(this.value.to) + this.tmpValue.to + this.tmpValue.halfTo) || this.max
      }
    },
    onWindowResize () {
      this.width.container = this.$refs.container.offsetWidth;
      this.width.step = (this.width.container / (this.max - this.min)) * this.step;
      this.width.halfStep = this.width.step / 2;
    },
    onMouseDown (event) {
      // check is previously mouse not pressed down
      if (this.isMouseDown) return;
      // get info from new mouse down event
      this.movedItem = event.target.getAttribute('name');
      this.mouse.startX = event.clientX;
      switch (this.movedItem) {
        case 'slider':
          this.$refs.slider.classList.add('slider--grabbing');
          break;
        case 'left':
          this.$refs.right.style.zIndex = 1;
          this.$refs.left.style.zIndex = 2;
          break;
        case 'right':
          this.$refs.left.style.zIndex = 1;
          this.$refs.right.style.zIndex = 2;
          break;
      }
      this.isMouseDown = true;
    },
    onMouseMove (event) {
      // check is previously mouse pressed down
      if (this.isMouseDown) {
        let clientX = event.clientX;
        let delta = this.mouse.startX - clientX;
        // get new value parts from delta
        let parts, halfPart;
        if (delta > 0) {
          parts = Math.floor(delta / this.width.step);
          halfPart = Math.floor((delta - (parts * this.width.step)) / this.width.halfStep);
          parts = -(parts * this.step);
          halfPart = -(halfPart * this.step);
        } else {
          delta = -delta;
          parts = Math.floor(delta / this.width.step);
          halfPart = Math.floor((delta - (parts * this.width.step)) / this.width.halfStep);
          parts = parts * this.step;
          halfPart = halfPart * this.step;
        }
        let valueFrom = Number(this.value.from);
        let valueTo = Number(this.value.to);
        switch (this.movedItem) {
          case 'left': {
            // check bounds
            let tmpValueFrom = valueFrom + parts + halfPart;
            if (tmpValueFrom < this.min) {
              parts = this.min - valueFrom;
              halfPart = 0;
            } else if (tmpValueFrom > valueTo) {
              parts = valueTo - valueFrom;
              halfPart = 0;
            }
            // set new values
            this.tmpValue.from = parts;
            this.tmpValue.halfFrom = halfPart;
            break;
          }
          case 'right': {
            // check bounds
            let tmpValueTo = valueTo + parts + halfPart;
            if (tmpValueTo > this.max) {
              parts = this.max - valueTo;
              halfPart = 0;
            } else if (tmpValueTo < valueFrom) {
              parts = valueFrom - valueTo;
              halfPart = 0;
            }
            // set new values
            this.tmpValue.to = parts;
            this.tmpValue.halfTo = halfPart;
            break;
          }
          case 'slider': {
            // check bounds
            let tmpValueFrom = valueFrom + parts + halfPart;
            let tmpValueTo = valueTo + parts + halfPart;
            if (tmpValueFrom < this.min) {
              parts = this.min - valueFrom;
              halfPart = 0;
            } else if (tmpValueTo > this.max) {
              parts = this.max - valueTo;
              halfPart = 0;
            }
            // set new values
            this.tmpValue.from = parts;
            this.tmpValue.to = parts;
            this.tmpValue.halfFrom = halfPart;
            this.tmpValue.halfTo = halfPart;
          }
        }
        // emit event that moved value update
        this.$emit('update', this.getValue());
      }
    },
    onMouseUp () {
      // check is previously mouse pressed down
      if (!this.isMouseDown) return;
      // final handling data
      this.updateValue();
      if (this.movedItem === 'slider') {
        this.$refs.slider.classList.remove('slider--grabbing');
      }
      this.resetData();
    },
    updateValue () {
      // recalculate new value
      let prevValueFrom = Number(this.value.from);
      let prevValueTo = Number(this.value.to);
      this.value.from = prevValueFrom + this.tmpValue.from + this.tmpValue.halfFrom;
      this.value.to = prevValueTo + this.tmpValue.to + this.tmpValue.halfTo;
      if (prevValueFrom !== this.value.from || prevValueTo !== this.value.to) {
        // emit event to update v-model
        this.$emit('input', this.value);
      }
    },
    resetData () {
      this.tmpValue.from = 0;
      this.tmpValue.halfFrom = 0;
      this.tmpValue.to = 0;
      this.tmpValue.halfTo = 0;
      this.movedItem = null;
      this.isMouseDown = false;
    }
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
