<template>
  <div class="popup-controls">
    <button class="rounded-button" @click="(open = !open), $emit('toggle')">
      <icon-chevron-down v-if="open" />
      <icon-chevron-down v-else />
    </button>
    <button class="rounded-button" @click="$emit('close')">
      <icon-times />
    </button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      open: true,
    };
  },
  emits: ["close", "toogle"],
};
</script>

<style scoped>
.rounded-button svg {
  fill: #f7f7f7;
}
.rounded-button {
  background: #212529;
}
.rounded-button:hover {
  background: #31363b;
}
</style>