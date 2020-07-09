<template>
  <div class="center-page">
    <div class="about-content">
      <pre
        style="font-size: 32px; height: 50px; overflow: hidden;"
        :class="[`language-bash`]"
      >
        <code>npm i rubberhose-lottie</code>
      </pre>
      <Rubberhose />
      <!-- <Anno size="25px" bold>Auto-parsing, auto-content</Anno> -->
      <Grid height="fit-content">
        <Rubberhose />
        <Code-Display :content="text1" />
      </Grid>
      <!-- <Anno size="25px" bold>Auto-parsing, auto-content</Anno> -->
      <Grid>
        <Code-Display :content="text2" />
        <Rubberhose :animation-data="testAnimation" />
      </Grid>
      <Grid>
        <Rubberhose
          :animation-data="lockAnimation"
          :locked="['Hose 1::Hips']"
        />
        <Code-Display :content="text3" />
      </Grid>
      <Grid>
        <Code-Display :content="text4" />
        <Rubberhose :animation-data="dragAnimation" :draggable="['square']" />
      </Grid>
      <Grid>
        <Rubberhose
          :animation-data="drag2Animation"
          :draggable="[
            {
              layer: 'square',
            },
            {
              layer: 'square 2',
            },
            {
              layer: 'square 3',
            },
          ]"
        />
        <Code-Display :content="text5" />
      </Grid>
      <Grid>
        <Code-Display :content="text6" />
        <Rubberhose
          :animation-data="clickAnimation"
          :clickable="[
            {
              layer: 'square',
              callback: doSomething,
            },
          ]"
        />
      </Grid>
      <Rubberhose
        :animation-data="dynamic"
        :clickable="[
          {
            layer: 'Clickable',
            callback: doSomething,
          },
        ]"
        :draggable="['square', 'square 2']"
        :locked="['Hose 1::Hips']"
      />
    </div>
  </div>
</template>

<script>
export default {
  components: {
    Rubberhose: require("rubberhose-lottie").default,
    "Code-Display": require("@/components/CodeDisplay.vue").default,
  },
  data: () => ({
    testAnimation: require("@/assets/plain.json"),
    dragAnimation: require("@/assets/draggable.json"),
    lockAnimation: require("@/assets/locked.json"),
    clickAnimation: require("@/assets/clickable.json"),
    drag2Animation: require("@/assets/draggable2.json"),
    dynamic: require("@/assets/dynamic.json"),

    text1: `<Rubberhose />`,
    text2: `<Rubberhose 
    :animation-data="testAnimation" 
  />`,
    text3: `<Rubberhose
    :animation-data="dragAnimation" 
    :locked="['Hose 1::Hips']" 
  />
`,
    text4: `<Rubberhose
    :animation-data="dragAnimation" 
    :draggable="['square']" 
  />
`,
    text5: `<Rubberhose
    :animation-data="dragAnimation" 
    :draggable="[
      { layer: 'square' },
      { layer: 'square 2' },
      { layer: 'square 3' }
    ]" 
  />
`,
    text6: `<Rubberhose
    :animation-data="clickAnimation" 
    :clickable="[
      {
        layer: 'square',
        callback: doSomething
      }
    ]" 
  />
`,
  }),
  methods: {
    doSomething() {
      console.log("HELLO");
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
