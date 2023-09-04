<template>
  <header>
    <h1>{{ title }}</h1>
    <TheButton @btn-click="$emit('toggle-add-task')" :text="showAddTask ? 'Close' : 'Add Task'" :color="showAddTask ? 'red' : 'green'" />
  </header>
</template>

<script>
import TheButton from "./TheButton.vue";

export default {
  name: "TheHeader",
  props: { title: String, showAddTask: Boolean },
  components: { TheButton },
};
</script>

<style scoped>
header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>
