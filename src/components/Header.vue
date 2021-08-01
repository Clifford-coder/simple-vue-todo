<template>
  <header>
    <h1>{{ title }}</h1>
    <Button
      :text="closeOrAddTaskBtn ? 'Close' : 'Add Task'"
      @btn-click="$emit('toggle-add-task-form')"
      :color="closeOrAddTaskBtn ? '#ff2633' : '#0033ff'"
      v-show="homePage"
    />
  </header>
</template>

<script>
import Button from "./Button";

export default {
  name: "Header",
  props: {
    title: String, //it can as well be an object with type and default as the key - value pairs.
    closeOrAddTaskBtn: Boolean,
  },
  components: {
    Button,
  },
  computed: {
    homePage() {
      if (this.$route.path === "/") return true;
      else return false;
    },
  },
};
</script>

<style scoped>
header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}
</style>