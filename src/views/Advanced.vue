<template>
  <div class="center-page">
    <div class="about-content">
      <Rubberhose
        v-if="!isViruses"
        :animation-data="realAnimation"
        :draggable="draggables"
        :hidden="realHiddens"
        :locked="locked"
        ref="rubberhose"
        debug
      />
      <Grid column v-else>
        <Rubberhose :animation-data="virus1" />
        <Rubberhose :animation-data="virus2" />
        <Rubberhose :animation-data="virus3" />
      </Grid>
    </div>
    <Button v-if="!isViruses" label="reset" @click="resetContent" />
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
    hand: require("@/assets/hand.json"),
    handBlank: require("@/assets/handBlank.json"),
    mouth: require("@/assets/mouth.json"),
    mouthBlank: require("@/assets/mouth.json"),
    bernie: require("@/assets/bernie.json"),
    virus1: require("@/assets/virus1.json"),
    virus2: require("@/assets/virus2.json"),
    virus3: require("@/assets/virus3.json"),
    bbox: require("@/assets/bbox.json"),
    rubberhoseNest: require("@/assets/testRubberhose.json"),
    rubberhoseBroken: require("@/assets/testRubberhoseBroken.json"),
  }),
  computed: {
    isViruses() {
      return /virus/.test(this.$route.params.id);
    },
    realAnimation() {
      let animation = this.$route.params.id;
      if (this[animation]) return this[animation];
      else return null;
    },
    draggables() {
      if (/mime/.test(this.$route.params.id)) return ["MasterControl"];
      else if (/bernie/.test(this.$route.params.id)) return ["MASTER"];
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
      else if (/bernie/.test(this.$route.params.id))
        return [
          "RThumb::Start",
          "RThumb::End",
          "RFinger1::Start",
          "RFinger1::End",
          "RFinger2::Start",
          "RFinger2::End",
          "RFinger3::Start",
          "RFinger3::End",
          "LThumb::Start",
          "LThumb::End",
          "LFinger1::Start",
          "LFinger1::End",
          "LFinger2::Start",
          "LFinger2::End",
          "LFinger3::Start",
          "LFinger3::End",
          "LArm::Shoulder",
          "RArm::Shoulder",
          "LLeg::Hip",
          "RLeg::Hip",
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
