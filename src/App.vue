<template>
  <div id="app">
    <div class="header">
      <p class="head">DEVeloPhotograp</p>
    </div>
    <div class="content">
      <markdown-wrapper
        v-for="item in list"
        :key="item.name"
        :name="item.name"
        :date="item.date"
      />
    </div>
  </div>
</template>

<script>
import MarkdownWrapper from "./components/markdown-wrapper";
import list from "./posts/reveal-list.js";
import "github-markdown-css";

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
            name: e,
            date: new Date(str)
          };
        } catch (e) {
          console.error(e);
          return false;
        }
      })
      .filter(e => e)
      .sort((a, b) => b.date - a.date);
    return {
      list: filtered
    };
  }
};
</script>
<style lang="scss">
.header {
  height: 4rem;
  width: 100vw;
  background-color: #dddddd;
  position: fixed;
  top: 0;
  left: 0;
  display: flex;
  padding: 0 0 0 1rem;
  align-items: center;
}
.content {
  margin-top: 4rem;
}

.head {
    font-size: 2rem;
    margin: 0;
    padding: 0;
}
</style>
