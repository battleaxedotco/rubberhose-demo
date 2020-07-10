<template>
  <div class="center-page">
    <div class="about-content">
      <Rubberhose
        :animation-data="realAnimation"
        :draggable="draggables"
        :hidden="realHiddens"
        :locked="locked"
        ref="rubberhose"
      />
    </div>
    <Button label="reset" @click="resetContent" />
  </div>
</template>

<script>
export default {
  components: {
    Rubberhose: require("rubberhose-lottie").default,
  },
  data: () => ({
    mime: require("@/assets/mime.json"),
    mimeBlank: require("@/assets/mimeBlank.json"),
    joystick: require("@/assets/joystick.json"),
  }),
  computed: {
    realAnimation() {
      let animation = this.$route.params.id;
      if (this[animation]) return this[animation];
      else return null;
    },
    draggables() {
      if (/mime/.test(this.$route.params.id)) return ["MasterControl"];
      else return [];
    },
    realHiddens() {
      if (/mime/.test(this.$route.params.id))
        return [
          "Larm 2::Shoulder",
          "Rarm 2::Shoulder",
          "RLeg 2::Hip",
          "LLeg 2::Hip",
          "RotationControl",
        ];
      else return [];
    },
    locked() {
      if (/mime/.test(this.$route.params.id))
        return [
          "scarfJOY",
          "browCurveJOY",
          "browCurveJOY",
          "browLiftJOY",
          "HeadTiltJOY",
        ];
      else return [];
    },
  },
  methods: {
    doSomething() {
      console.log("HELLO");
    },
    resetContent() {
      this.$refs.rubberhose.resetPositions();
    },
  },
};
</script>

<style>
.about-content {
  /* max-width: 1200px; */
  height: fit-content;
  width: 800px;
}
.center-page {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

.about-content > * {
  margin-bottom: 30px;
}

.brutalism-grid-wrapper,
.brutalism-grid-content {
  height: "";
}

.language-html {
  margin: 0 !important;
}
</style>
