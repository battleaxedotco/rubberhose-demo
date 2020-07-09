<template>
  <div
    class="slottie-container"
    :style="{
      height: height,
      width: width,
    }"
  >
    <div class="slottie-animation"></div>
  </div>
</template>

<script>
import * as lottie from "lottie-web";
import loading from "../assets/data.json";

export default {
  name: "slottie",
  props: {
    animationData: {
      type: Object,
      default: () => {
        return null;
      },
    },
    controllers: {
      type: Array,
      default: () => {
        return [];
      },
    },
    height: {
      type: String,
      default: "",
    },
    width: {
      type: String,
      default: "",
    },
    layer: {
      type: String,
      default: "control",
    },
  },
  data: () => ({
    speed: 1,
    elt: null,
    doneLoading: false,
    animData: null,
    loop: true,
    autoplay: true,
    control: [],
  }),
  mounted() {
    require("./lottie_api.js");
    this.elt = this.$el.children[0];
    this.init();
  },
  computed: {},
  methods: {
    init() {
      this.animData = this.buildAnimation();
      this.animAPI = lottie_api.createAnimationApi(this.animData);

      this.buildControllerCallbacks();
      this.animData.play();
    },
    isDone() {
      this.$emit("loop");
    },
    buildControllerCallbacks() {
      this.controllers.forEach((controller) => {
        let tempSlider = this.animAPI.getKeyPath(
          `${this.layer},Effects,${controller.name},0`
        );
        this.animAPI.addValueCallback(tempSlider, (currentVal) => {
          return controller.value;
        });
      });
    },
    buildAnimation() {
      const self = this;
      const animData = {
        wrapper: self.elt,
        animType: "svg",
        loop: self.loop,
        prerender: true,
        autoplay: self.autoplay,
      };
      animData.animationData = this.animationData;
      return lottie.loadAnimation(animData);
    },
  },
};
</script>

<style>
svg {
  width: 100%;
}

.slottie-container {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
}

.slottie-animation {
  /* max-width: 200px; */
  width: 100%;
}

.anim-main {
  fill: var(--color-default);
}

.rh-control {
  fill: #00aaff;
  stroke: #00aaff;
}
.rh-main {
  stroke: #ffaa00;
}
.rh-bg {
  fill: transparent;
}
</style>
