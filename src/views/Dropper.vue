<template>
  <div class="dropper-page">
    <div class="closeButton" v-if="currentAnimation">
      <Button @click="$refs.lottie.checkValue()">
        <Icon name="download" />
      </Button>
      <Button @click="$refs.lottie.resetPositions()">
        <Icon name="refresh" />
      </Button>
      <Button @click="currentAnimation = null">
        <Icon name="close" />
      </Button>
    </div>
    <div class="dropper-content">
      <File-Picker v-if="!currentAnimation" auto-read @read="testRead">
        <Dropzone :fullscreen="false" auto-read @read="testRead">
          <template v-slot:prompt>
            <div class="placeholder">
              Drop or click any Rubberhose rig
            </div>
          </template>
          <template v-slot:overlay>
            <div class="placeholder" />
          </template>
        </Dropzone>
      </File-Picker>
      <div class="rubberhose-drop-content" v-else>
        <Rubberhose
          ref="lottie"
          :animation-data="currentAnimation"
          :draggable="realDraggables"
          debug
        />
      </div>
    </div>
    <!-- <Grid style="max-width: 800px;">
      <Button @click="currentAnimation = test">test 1</Button>
      <Button @click="currentAnimation = virus">test 2</Button>
      <Button @click="currentAnimation = joystick">test 3</Button>
    </Grid> -->
  </div>
</template>

<script>
export default {
  components: {
    Rubberhose: require("rubberhose-lottie").default,
  },
  data: () => ({
    currentAnimation: null,
    virus: require("@/assets/virus2.json"),
    joystick: require("@/assets/joystick.json"),
    test: require("@/assets/test.json"),
  }),
  computed: {
    realDraggables() {
      if (this.currentAnimation.nm == "Comp 2") {
        return ["Relative", "Absolute", "Dynamic", ".RelativeAnchor"];
      }
      return [];
    },
  },
  methods: {
    testRead(value) {
      console.log(value);
      this.currentAnimation = value;
    },
  },
};
</script>

<style>
.closeButton {
  position: absolute;
  top: 20px;
  right: 20px;
  display: flex;
  justify-content: center;
}
.closeButton > * {
  margin: 0px 5px;
}
.placeholder {
  min-height: 30px;
  height: calc(100vh - 180px);
  border-radius: 5px;
  width: 100%;
  background: rgba(0, 0, 0, 0.05);
  padding: 30px;
  user-select: none;
  cursor: default;
  font-size: 40px;
  display: flex;
  justify-content: center;
  align-items: center;
  box-sizing: border-box;
}

.dropper-page {
  box-sizing: border-box;
  padding: 50px;
  width: 100%;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  position: relative;
}

.dropper-content,
.rubberhose-drop-content {
  box-sizing: border-box;
  max-width: 800px;
  overflow: hidden;
}

.dropper-content {
  width: 100%;
}

.rubberhose-drop-content {
  width: 100%;
  border: 2px solid rgba(0, 0, 0, 0.05);
}
</style>
