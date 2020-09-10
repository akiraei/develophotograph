<template>
  <section>
    <label>{{ title }}</label>
    <label v-for="item in tags" :key="item">{{ item }}</label>
    <label v-for="item in categories" :key="item">{{ item }}</label>
    <component v-if="component" :is="component" />
  </section>
</template>

<script>
export default {
  name: "markdown-wrapper",
  props: ["name"],
  data() {
    return { title: "", tags: [], categories: [], component: null };
  },
  mounted() {
    const md = require(`../posts/${this.name}.md`);
    this.title = md.attributes.title;
    this.tags = md.attributes.tags;
    this.categories = md.attributes.categories;
    this.component = md.vue.component;
  }
};
</script>
