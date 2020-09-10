<template>
  <div id="app">
    <markdown-wrapper v-for="name in list" :key="name" :name="name" />
  </div>
</template>

<script>
import MarkdownWrapper from "./components/markdown-wrapper";
import list from "./posts/reveal-list.js";

export default {
  name: "App",
  components: {
    MarkdownWrapper
  },
  data() {
    const filtered = list
      .map(e => {
        try {
          const arr = e.split("-").slice(0, 3);
          const str = arr.join("-");
          return {
            value: e,
            date: new Date(str)
          };
        } catch (e) {
          console.error(e);
          return false;
        }
      })
      .filter(e => e)
      .sort((a, b) => b.date - a.date)
      .map(e => e.value);
    return {
      list: filtered
    };
  }
};
</script>
