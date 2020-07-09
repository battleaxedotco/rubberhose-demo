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
    draggableLayers: [],
    controlPoints: [],
    realHoses: [],
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
    activeItem(val) {
      console.log("ACTIVE ITEM IS CURRENTLY:", val);
    },
  },
  computed: {
    stringMousePos() {
      return JSON.stringify(this.mousePos);
    },
    totalDraggableLayers() {
      return [].concat(this.draggableLayers, this.controlPoints);
    },
  },
  methods: {
    prepAnimationFile(file) {
      let hosesFound = [],
        total = [],
        temp = [];
      this.draggableLayers = [];
      this.controlPoints = [];
      this.realHoses = [];
      if (this.draggables.length) {
        this.draggables.forEach((layerName) => {
          let thisDraggable = {};
          let layer = file.layers.find((item) => {
            return item.nm == layerName;
          });
          if (layer) {
            layer["cl"] = "rubberhose-draggable";
            thisDraggable = {
              name: layerName,
              class: "rubberhose-draggable",
              position: {
                x: layer.ks.p.k[0],
                y: layer.ks.p.k[1],
              },
            };
            this.draggableLayers.push(thisDraggable);
          }
        });
      }
      file.layers.forEach((layer) => {
        if (/\:\:/.test(layer.nm)) {
          let hoseName = layer.nm.replace(/\:\:.*/, "");
          layer["cl"] = "rubberhose-controller";
          layer["hd"] = false;
          let shape = layer.shapes.find((item) => {
            return item.nm == "Control Point";
          });
          temp.push({
            name: layer.nm,
            matches: new RegExp(`^${hoseName}`),
            position: {
              x: layer.ks.p.k[0],
              y: layer.ks.p.k[1],
            },
            parent: hoseName,
            class: hoseName.toLowerCase().replace(/\s/gm, "-"),
            sibling: "",
          });

          if (shape)
            shape.it[1]["cl"] = hoseName.toLowerCase().replace(/\s/gm, "-");
          if (!hosesFound.includes(hoseName)) hosesFound.push(hoseName);
        }
      });
      temp.forEach((hose) => {
        let sibling = file.layers.find((layer) => {
          return hose.matches.test(layer.nm) && layer.nm !== hose.name;
        });
        if (sibling) hose.sibling = sibling.nm;
        this.controlPoints.push(hose);
      });

      // this.controlPoints = temp;
      hosesFound.forEach((hose) => {
        let targetLayer = file.layers.find((layer) => {
          return new RegExp(`^${hose}$`).test(layer.nm);
        });
        let rubberhose = {
          name: targetLayer.nm,
          class: targetLayer.nm.toLowerCase().replace(/\s/gm, "-"),
        };
        let pointA = this.controlPoints.find((item) => {
          return new RegExp(rubberhose.name).test(item.name);
        });
        let pointB = this.controlPoints.find((item) => {
          return item.name == pointA.sibling;
        });
        rubberhose["A"] = pointA;
        rubberhose["B"] = pointB;
        rubberhose = this.updateHose(rubberhose);
        this.realHoses.push(rubberhose);
      });
      return file;
    },
    findDistance(a, b) {
      return Math.hypot(
        a.position.x - b.position.x,
        a.position.y - b.position.y
      );
    },
    updateHose(rubberhose) {
      let hasDistance = this.animationData.layers.find((item) => {
        return (
          (item.nm == rubberhose.A.name || item.nm == rubberhose.B.name) &&
          item.ef &&
          item.ef.length
        );
      });
      rubberhose["distance"] = this.findDistance(rubberhose.A, rubberhose.B);
      rubberhose["length"] = hasDistance.ef[0].ef.find((prop) => {
        return prop.nm == "Hose Length";
      }).v.k;
      rubberhose["isExtended"] = rubberhose.distance >= rubberhose.length;
      return rubberhose;
    },
    init() {
      let animData = this.prepAnimationFile(this.animationData);
      this.animData = this.buildAnimation(animData);
      this.animAPI = lottie_api.createAnimationApi(this.animData);

      this.buildDynamicDraggableCallbacks();
      // this.buildControllerCallbacks();
      // this.buildDraggableCallbacks();
      this.adjustScreenSize();
      this.adjustLottieSize();
      this.animData.play();
    },

    buildDynamicDraggableCallbacks() {
      const self = this;

      this.$nextTick(() => {
        self.totalDraggableLayers.forEach((layer) => {
          layer["elt"] = this.identifyLayerElement(layer);
          if (layer.elt) {
            layer.elt.addEventListener("mousedown", (evt) => {
              self.activeItem = layer;
            });
            this.animAPI.addValueCallback(
              this.animAPI.getKeyPath(`${layer.name},Transform,Position`),
              (currentVal) => {
                return [layer.position.x, layer.position.y];
              }
            );
          }
        });
        window.addEventListener("mouseup", (evt) => {
          self.activeItem = null;
        });
      });
    },

    /**
     * Currently only works if anchor is set to middle.
     * Doesn't take into account anchor offset from Layer.Transform.Position, probably should
     */
    identifyLayerElement(layer) {
      let possibleElts = document.querySelectorAll(`.${layer.class}`);
      let nodeList = [];
      let match = null;
      for (let i = 0; i < possibleElts.length; i++)
        nodeList.push(possibleElts[i]);
      nodeList.forEach((path) => {
        let position = path.getBoundingClientRect();
        let x = Math.round(position.width / 2 + position.x);
        let y = Math.round(position.height / 2 + position.y);
        let realPos = this.getCoordinatesRelativeToLottie(x, y);
        if (layer.position.x == realPos.x && layer.position.y == realPos.y)
          match = path;

        if (
          layer.position.x + position.width / 2 >= realPos.x &&
          layer.position.x - position.width / 2 <= realPos.x &&
          layer.position.y + position.height / 2 >= realPos.y &&
          layer.position.y - position.height / 2 <= realPos.y
        ) {
          match = path;
        }
      });
      return match;
    },

    // OLD
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
    getCoordinatesRelativeToLottie(x, y) {
      if (arguments.length < 2 && !/number/i.test(typeof arguments[0]))
        (x = x[0]), (y = x[1]);
      let bbox = this.lottieElt.getBoundingClientRect();
      let insidePos = {
        x: Math.round(x - bbox.x),
        y: Math.round(y - bbox.y),
      };
      return {
        x: Math.round(
          insidePos.x * (this.lottieSize.width / this.compSize.width)
        ),
        y: Math.round(
          insidePos.y * (this.lottieSize.height / this.compSize.height)
        ),
      };
    },

    // OLD
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
    adjustLottieSize() {
      this.lottieSize.width = this.animationData.w;
      this.lottieSize.height = this.animationData.h;
    },
    buildAnimation(anim = null) {
      if (!anim) anim = this.animationData;
      return lottie.loadAnimation({
        wrapper: this.elt,
        animType: "svg",
        loop: this.loop,
        prerender: true,
        autoplay: this.autoplay,
        animationData: anim,
      });
    },
  },
};
</script>

<style>
svg {
  width: 100%;
  max-width: 1000px;
}

.rubberhose-controller {
  border: 2px solid red;
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
