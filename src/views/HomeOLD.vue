<template>
  <div class="main-content">
    <Rubberhose @rubberhoses="assignRubberhoses" />
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
    rubberhoses: [],
    // controllers: {
    //   length: {
    //     layer: "control",
    //     name: "hoseLength",
    //     value: 700,
    //   },
    //   direction: {
    //     layer: "control",
    //     name: "bendDirection",
    //     value: 100,
    //   },
    //   radius: {
    //     layer: "control",
    //     name: "bendRadius",
    //     value: 100,
    //   },
    // },
    // animationData: require("@/assets/plain.json"),
  }),
  components: {
    Rubberhose: require("rubberhose-lottie").default,
  },
  computed: {
    // The prop expects an Array, but I'd prefer to keep them as Objects
    // in data above. So we just convert the parent data objects to an Array:
    // controllerArray() {
    //   let temp = [];
    //   Object.keys(this.controllers).forEach((key) => {
    //     temp.push(this.controllers[key]);
    //   });
    //   return temp;
    // },
  },
  methods: {
    doSomething(val) {
      console.log("Clicked!");
    },
    assignRubberhoses(val) {
      console.log(val);
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
