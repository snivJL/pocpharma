<template>
  <header>
    <div class="container">
      <h1>{{ title }}</h1>
    </div>
  </header>
</template>

<script lang="ts">
import { defineComponent } from "vue";
export default defineComponent({
  props: ["title"],
});
</script>

<style scoped>
header {
  display: flex;
  align-items: center;
  height: 60px;
  background: #009578;
  color: white;
}
</style>
