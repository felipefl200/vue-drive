<template>
  <nav
    class="
      navbar navbar-light
      bg-white
      navbar-expand-lg
      border-bottom
      fixed-top
    "
  >
    <div class="container">
      <a class="navbar-brand" href="/"><strong>Vue</strong>Drive</a>

      <div id="search-form"></div>
      
    </div>
  </nav>
</template>
<script>
import { ref, watchEffect } from "vue";
import SearchForm from "./SearchForm.vue";
export default {
  setup() {
    const q = ref("");
    watchEffect(() => console.log("q", q.value));
    return { q };
  },
  components: {
    SearchForm,
  },
};
</script>
