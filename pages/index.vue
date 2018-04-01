<template>
  <div>
    <div class="controls">
      <div class="lightness">
        <div class="p1">
          Point 1: <br>
          x: <input v-model="lPoints.p1.x" type="range" min="0" max="1" step="0.01"><br>
          y: <input v-model="lPoints.p1.y" type="range" min="0" max="1" step="0.01"><br>
          <div>{{ lPoints.p1 }}</div>
        </div>
        <div class="p1">
          Point 1: <br>
          x: <input v-model="lPoints.p2.x" type="range" min="0" max="1" step="0.01"><br>
          y: <input v-model="lPoints.p2.y" type="range" min="0" max="1" step="0.01"><br>
          <div>{{ lPoints.p2 }}</div>
        </div>
      </div>
      <div class="chroma">
        <div class="p1">
          Point 1: <br>
          x: <input v-model="cPoints.p1.x" type="range" min="0" max="1" step="0.01"><br>
          y: <input v-model="cPoints.p1.y" type="range" min="0" max="1" step="0.01"><br>
          <div>{{ cPoints.p1 }}</div>
        </div>
        <div class="p1">
          Point 1: <br>
          x: <input v-model="cPoints.p2.x" type="range" min="0" max="1" step="0.01"><br>
          y: <input v-model="cPoints.p2.y" type="range" min="0" max="1" step="0.01"><br>
          <div>{{ cPoints.p2 }}</div>
        </div>
      </div>
    </div>
    <template v-for="color in palette">
      <div :key="color" :style="{ backgroundColor: '#'+labToHex(color), padding: '8px' }" class="color">
        {{ color }}
      </div>
    </template>
    <easing-edit/>
  </div>
</template>

<script>
import convert from "color-convert"
import bezier from "~/plugins/bezier-easing.js"
import easingEdit from "~/components/easing-edit"

export default {
  components: {
    easingEdit
  },
  data() {
    return {
      lPoints: {
        p1: { x: 0.25, y: 0 },
        p2: { x: 0.75, y: 1 }
      },
      cPoints: {
        p1: { x: 0.25, y: 0 },
        p2: { x: 0.75, y: 1 }
      },
      // color: chromaJS.lch(80, 40, 130).hex(),
      lightnessRange: [0, 1],
      chromaRange: [0, 1],
      hueRange: [0, 360]
    }
  },
  computed: {
    palette() {
      return {
        c0: convert.lch.lab.raw([this.getL(50), this.getC(0), 0]),
        c50: convert.lch.lab.raw([this.getL(50), this.getC(5), 0]),
        c100: convert.lch.lab.raw([this.getL(50), this.getC(10), 0]),
        c200: convert.lch.lab.raw([this.getL(50), this.getC(20), 0]),
        c300: convert.lch.lab.raw([this.getL(50), this.getC(30), 0]),
        c400: convert.lch.lab.raw([this.getL(50), this.getC(40), 0]),
        c500: convert.lch.lab.raw([this.getL(50), this.getC(50), 0]),
        c600: convert.lch.lab.raw([this.getL(50), this.getC(60), 0]),
        c700: convert.lch.lab.raw([this.getL(50), this.getC(70), 0]),
        c800: convert.lch.lab.raw([this.getL(50), this.getC(80), 0]),
        c900: convert.lch.lab.raw([this.getL(50), this.getC(90), 0]),
        c1000: convert.lch.lab.raw([this.getL(50), this.getC(100), 0])
      }
    }
  },
  methods: {
    getL(x) {
      x /= 100
      return (
        bezier(
          this.lPoints.p1.x,
          this.lPoints.p1.y,
          this.lPoints.p2.x,
          this.lPoints.p2.y
        )(x) * 131
      )
    },
    getC(x) {
      x /= 100
      return (
        bezier(
          this.cPoints.p1.x,
          this.cPoints.p1.y,
          this.cPoints.p2.x,
          this.cPoints.p2.y
        )(x) * 100
      )
    },
    labToHex(color) {
      /*eslint-disable*/
      return (
        convert.rgb.hex.raw(
          convert.xyz.rgb.raw(
            convert.lab.xyz.raw(color)
          )
        )
      )
      /*eslint-enable*/
    }
  }
}
</script>

<style lang="scss">
input[type="range"] {
  box-sizing: border-box;
  width: 100%;
}
.controls {
  display: flex;
}
.lightness {
  padding: 8px;
  width: 50%;
}
.chroma {
  padding: 8px;
  width: 50%;
}
</style>
