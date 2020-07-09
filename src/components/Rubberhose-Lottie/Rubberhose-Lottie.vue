<template>
  <div
    class="slottie-container"
    :style="{
      height: height,
      width: width,
    }"
    @mousemove="adjustMousePos"
  >
    <div class="slottie-animation"></div>
    <div class="mousecoords">
      {{ mousePos }}
    </div>
  </div>
</template>

<script>
import * as lottie from "lottie-web";

export default {
  name: "Rubberhose-Lottie",
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
    draggables: {
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
    mousePos: {
      x: 0,
      y: 0,
    },
    compSize: {
      width: 0,
      height: 0,
    },
    lottieSize: {
      width: 0,
      height: 0,
    },
    lottieElt: null,
    dragElements: [],
    activeItem: null,
  }),
  mounted() {
    require("./lottie_api.js");
    this.elt = this.$el.children[0];
    this.init();
    window.addEventListener("resize", this.adjustScreenSize);
  },
  watch: {
    stringMousePos(value) {
      if (this.activeItem) {
        this.activeItem.position.x = this.mousePos.x;
        this.activeItem.position.y = this.mousePos.y;
      }
    },
  },
  computed: {
    stringMousePos() {
      return JSON.stringify(this.mousePos);
    },
  },
  methods: {
    init() {
      this.animData = this.buildAnimation();
      this.animAPI = lottie_api.createAnimationApi(this.animData);
      this.buildControllerCallbacks();
      this.buildDraggableCallbacks();
      this.adjustScreenSize();
      this.adjustLottieSize();
      this.animData.play();
    },
    adjustMousePos(evt) {
      let bbox = this.lottieElt.getBoundingClientRect();
      let insidePos = {
        x: Math.round(evt.clientX - bbox.x),
        y: Math.round(evt.clientY - bbox.y),
      };
      if (
        insidePos.x >= 0 &&
        insidePos.y >= 0 &&
        insidePos.x <= this.compSize.width &&
        insidePos.y <= this.compSize.height
      ) {
        this.mousePos.x = Math.round(
          insidePos.x * (this.lottieSize.width / this.compSize.width)
        );
        this.mousePos.y = Math.round(
          insidePos.y * (this.lottieSize.height / this.compSize.height)
        );
      }
    },
    adjustScreenSize() {
      if (!this.lottieElt) this.lottieElt = this.elt.children[0];
      let boundingBox = this.lottieElt.getBoundingClientRect();
      this.compSize.width = boundingBox.width;
      this.compSize.height = boundingBox.height;
    },
    buildDraggableCallbacks() {
      const self = this;
      this.$nextTick(() => {
        self.draggables.forEach((item) => {
          let list = [];
          for (
            let i = 0;
            i < document.querySelectorAll(item.selector).length;
            i++
          )
            list.push(document.querySelectorAll(item.selector)[i]);
          let realElt = list.find((elt) => {
            let bbox = elt.getBoundingClientRect();
            return bbox.width > 0 && bbox.height > 0;
          });
          let temp = {
            name: item.name,
            elt: realElt,
          };
          let layer = this.animationData.layers.find((target) => {
            return target.nm == item.name;
          });
          temp["position"] = {
            x: layer.ks.p.k[0],
            y: layer.ks.p.k[1],
          };
          this.animAPI.addValueCallback(
            this.animAPI.getKeyPath(`${item.name},Transform,Position`),
            (currentVal) => {
              return [temp.position.x, temp.position.y];
            }
          );
          temp.elt.addEventListener("mousedown", (evt) => {
            self.activeItem = temp;
          });
          window.addEventListener("mouseup", (evt) => {
            self.activeItem = null;
          });
          self.dragElements.push(temp);
        });
      });
    },
    buildControllerCallbacks() {
      this.controllers.forEach((controller) => {
        this.animAPI.addValueCallback(
          this.animAPI.getKeyPath(`${this.layer},Effects,${controller.name},0`),
          (currentVal) => {
            return controller.value;
          }
        );
      });
    },
    adjustLottieSize() {
      this.lottieSize.width = this.animationData.w;
      this.lottieSize.height = this.animationData.h;
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
  max-width: 1000px;
}

.slottie-container {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  flex-direction: column;
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
  fill: rgba(1, 1, 1, 0.1);
}

#TestShoulder,
#TestWrist {
  cursor: move;
}
</style>
