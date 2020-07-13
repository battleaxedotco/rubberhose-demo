<template>
  <div class="main-content">
    <div class="home-content">
      <Rubberhose :controllers="controllerArray" :animation-data="animation" />
      <div class="controls">
        <div
          class="control-range slider"
          v-for="(controller, i) in realControls"
          :key="i"
        >
          <Input-Scroll
            style="font-size: 16px !important;"
            :label="controller.realname"
            v-model="controller.value"
            :min="controller.options.min"
            :max="controller.options.max"
            flat
          />
          <vue-slider v-model="controller.value" v-bind="controller.options" />
        </div>
        <div class="control-range toggles">
          <Toggle
            label="Auto Rotate Start"
            :state="controls.autoRotateStart.value"
            v-model="controls.autoRotateStart.value"
          />
          <Toggle
            label="Auto Rotate End"
            :state="controls.autoRotateEnd.value"
            v-model="controls.autoRotateEnd.value"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import "vue-slider-component/theme/default.css";
import slider from "vue-slider-component";
export default {
  data: () => ({
    rubberhoses: [],
    animation: require("@/assets/homeExample.json"),
    controls: {
      autoRotateStart: {
        layer: "control",
        name: "rotateStart",
        value: false,
      },
      autoRotateEnd: {
        layer: "control",
        name: "rotateEnd",
        value: true,
      },
      length: {
        layer: "control",
        name: "hoseLength",
        realname: "Hose Length",
        value: 700,
        options: {
          dotSize: 14,
          width: "100%",
          height: 4,
          contained: false,
          direction: "ltr",
          data: null,
          min: 1,
          max: 900,
          interval: 1,
          clickable: true,
          duration: 0.5,
        },
      },
      direction: {
        layer: "control",
        name: "bendDirection",
        realname: "Bend Direction",
        value: 100,
        options: {
          dotSize: 14,
          width: "100%",
          height: 4,
          contained: false,
          direction: "ltr",
          data: null,
          min: -100,
          max: 100,
          interval: 1,
          clickable: true,
          duration: 0.5,
        },
      },
      radius: {
        layer: "control",
        name: "bendRadius",
        realname: "Bend Radius",
        value: 100,
        options: {
          dotSize: 14,
          width: "100%",
          height: 4,
          contained: false,
          direction: "ltr",
          data: null,
          min: -100,
          max: 100,
          interval: 1,
          clickable: true,
          duration: 0.5,
        },
      },
      realism: {
        layer: "control",
        name: "realism",
        realname: "Realism",
        value: 0,
        options: {
          dotSize: 14,
          width: "100%",
          height: 4,
          contained: false,
          direction: "ltr",
          data: null,
          min: 0,
          max: 100,
          interval: 1,
          clickable: true,
          duration: 0.5,
        },
      },
    },
  }),
  components: {
    Rubberhose: require("rubberhose-lottie").default,
    "vue-slider": slider,
  },
  computed: {
    // The prop expects an Array, but I'd prefer to keep them as Objects
    // in data above. So we just convert the parent data objects to an Array:
    controllerArray() {
      let temp = [];
      Object.keys(this.controls).forEach((key) => {
        if (!/options/.test(key)) temp.push(this.controls[key]);
      });
      return temp;
    },
    realControls() {
      let temp = [];
      Object.keys(this.controls).forEach((key) => {
        if (!/autoRotate/.test(key)) temp.push(this.controls[key]);
      });
      return temp;
    },
  },
  methods: {
    assignRubberhoses(val) {
      console.log(val);
    },
  },
};
</script>

<style>
.main-content {
  display: flex;
  justify-content: center;
  width: 100%;
  align-items: flex-start;
}

.home-content {
  width: 800px;
}

.anim-main {
  fill: var(--color-default);
}

.controls {
  width: 100%;
  min-height: 40px;
}

.control-range {
  display: flex;
  justify-content: center;
  flex-wrap: nowrap;
  width: 100%;
  box-sizing: border-box;
  padding: 10px 20px;
  align-items: center;
  font-size: 20px !important;
}

.control-range.slider {
  display: grid;
  grid-template-columns: 1fr 1fr;
  width: calc(100% - 40px);
  justify-items: center;
}

.toggle-contents .label {
  user-select: none;
  cursor: default;
}

.input-scroll-value {
  font-size: 20px !important;
}

.control-range > * {
  margin: 0px 30px;
}

.rh-control {
  fill: #00aaff;
  stroke: #00aaff;
}
.rh-main {
  stroke: #ffaa00;
}
.rh-bg {
  fill: rgba(1, 1, 1, 0.02);
}

@media only screen and (max-width: 500px) {
  .control-range.toggles {
    flex-direction: column;
  }
  .control-range.toggles > * {
    margin: 4px 0px;
  }
}
</style>
