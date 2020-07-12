<template>
  <div class="main-content">
    <Rubberhose @rubberhoses="assignRubberhoses" />
  </div>
</template>

<script>
export default {
  data: () => ({
    rubberhoses: [],
  }),
  components: {
    Rubberhose: require("rubberhose-lottie").default,
  },
  computed: {},
  methods: {
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
