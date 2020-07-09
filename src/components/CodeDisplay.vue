<template>
  <pre :class="[`language-${lang}`]">
    <code>{{content}}</code>
  </pre>
</template>

<script>
// const sanitizeHtml = require('sanitize-html');

export default {
  props: {
    lang: {
      type: String,
      default: "html",
    },
    content: "",
  },
  data: () => ({}),
  methods: {},
  mounted() {
    Prism.highlightElement(this.$el, false, () => {
      // Done
    });
  },
  watch: {
    content(val) {
      console.log(val);
    },
  },
};
</script>

<style></style>
