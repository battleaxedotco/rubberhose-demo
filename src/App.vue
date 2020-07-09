<template>
  <div id="app">
    <Wrapper>
      <router-view />
    </Wrapper>
  </div>
</template>

<script>
import anim from "./assets/plain.json";

export default {
  name: "App",
  data: () => ({}),
  mounted() {
    require("starlette").default.initAs("ILST", "lightest");
  },
};
</script>

<style>
html,
body,
#app {
  width: 100vw;
  height: 100vh;
  overflow: auto;
  /* background-color: #262626; */
}

.main-content {
  width: 100%;
  text-align: center;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}
</style>
