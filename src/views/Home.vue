<template>
  <div class="main-content">
    <Rubberhose
      :animation-data="animationData"
      :controllers="controllerArray"
    />
    <Grid style="width: fit-content" column>
      <Input-Scroll
        label="Hose Length"
        v-model="controllers.length.value"
        :step="20"
        :min="1"
      />
      <Input-Scroll
        label="Bend Radius"
        v-model="controllers.radius.value"
        :min="-200"
        :max="200"
      />
      <Input-Scroll
        label="Bend Direction"
        v-model="controllers.direction.value"
        :min="-100"
        :max="100"
      />
    </Grid>
  </div>
</template>

<script>
export default {
  data: () => ({
    controllers: {
      length: {
        layer: "control",
        name: "hoseLength",
        value: 700,
      },
      direction: {
        layer: "control",
        name: "bendDirection",
        value: 100,
      },
      radius: {
        layer: "control",
        name: "bendRadius",
        value: 100,
      },
    },
    animationData: require("@/assets/plain.json"),
  }),
  components: {
    Rubberhose: require("rubberhose-lottie").default,
  },
  computed: {
    controllerArray() {
      let temp = [];
      Object.keys(this.controllers).forEach((key) => {
        temp.push(this.controllers[key]);
      });
      return temp;
    },
  },
  methods: {
    doSomething(val) {
      console.log("Clicked!");
    },
  },
};
</script>

<style>
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
  fill: rgba(1, 1, 1, 0.02);
}
</style>
