<template>
  <section class="post">
    <div class="post--header">
      <label class="title">{{ title }}</label>
      <label class="category" v-for="item in categories" :key="item">{{
        item
      }}</label>
      <label class="tag" v-for="(item, i) in tags" :key="`${item}${i}`">{{
        item
      }}</label>
    </div>
    <p class="date">{{ dateStr }}</p>
    <component v-if="component" :is="component" />
  </section>
</template>

<script>
export default {
  name: "markdown-wrapper",
  props: ["name", "date"],
  data() {
    return { title: "", tags: [], categories: [], component: null };
  },
  mounted() {
    const md = require(`../posts/${this.name}.md`);
    this.title = md.attributes.title;
    this.tags = md.attributes.tags;
    this.categories = md.attributes.categories;
    this.component = md.vue.component;
  },
  computed: {
    dateStr() {
      const day = this.date.getDate();
      const month = this.date.getMonth() + 1;
      const year = this.date.getFullYear();
      return [year, month, day].map(e => String(e)).join(". ");
    }
  }
};
</script>
<style lang="scss" scoped>
.post {
  padding: 1rem 1rem 5rem 1rem;
  &--header {
    margin-bottom: 1rem;
  }
}

.title {
  background-color: #111111;
}

.category {
  background-color: #119911;
}

.tag {
  background-color: #111188;
}
.date {
  margin: 0rem;
}

label {
  display: inline-block;
  padding: 0.5rem;
  margin: 0 0.5rem 0.5rem 0;
  color: #fff;
}
</style>
