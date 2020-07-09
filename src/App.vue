<template>
  <div id="app">
    <Wrapper>
      <div class="main-content">
        <slottie :animation-data="animationData" :controllers="controllers" />
        <Grid style="width: fit-content" column>
          <Input-Scroll label="Hose Length" v-model="length.value" :min="1" />
          <Input-Scroll
            label="Bend Radius"
            v-model="radius.value"
            :min="-200"
            :max="200"
          />
          <Input-Scroll
            label="Bend Direction"
            v-model="direction.value"
            :min="-100"
            :max="100"
          />
        </Grid>
      </div>
    </Wrapper>
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";
import anim from "./assets/data.json";

export default {
  name: "App",
  data: () => ({
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
  }),
  components: {
    slottie: require("@/components/slottie.vue").default,
  },
  mounted() {
    require("starlette").default.initAs("AEFT", "gradient", "0");
    console.log(this.controllers);
  },
  computed: {
    animationData() {
      return anim;
    },
    controllers() {
      let temp = [];
      Object.keys(this._data).forEach((key) => {
        temp.push(this[key]);
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
