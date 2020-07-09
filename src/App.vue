<template>
  <div id="app">
    <Wrapper>
      <div class="main-content">
        <slottie
          :animation-data="animationData"
          :controllers="controllerArray"
          :draggables="draggables"
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
    </Wrapper>
  </div>
</template>

<script>
import anim from "./assets/data.json";

export default {
  name: "App",
  data: () => ({
    controllers: {
      length: {
        name: "hoseLength",
        value: 700,
      },
      direction: {
        name: "bendDirection",
        value: 100,
      },
      radius: {
        name: "bendRadius",
        value: 100,
      },
    },
    draggables: [
      {
        name: "#TestWrist",
        selector: "#TestWrist",
      },
      {
        name: "#TestShoulder",
        selector: "#TestShoulder",
      },
    ],
  }),
  components: {
    slottie: require("@/components/Rubberhose-Lottie").default,
  },
  mounted() {
    require("starlette").default.initAs("AEFT", "gradient", "0");
    console.log(this.controllers);
  },
  computed: {
    animationData() {
      return anim;
    },
    controllerArray() {
      let temp = [];
      Object.keys(this.controllers).forEach((key) => {
        temp.push(this.controllers[key]);
      });
      return temp;
    },
  },
};
</script>

<style>
html,
body,
#app {
  width: 100vw;
  height: 100vh;
  background-color: #262626;
}

.main-content {
  width: 100%;
  text-align: center;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}
</style>
