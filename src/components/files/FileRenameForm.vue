<template>
  <form @submit.prevent="handleSubmit">
    <input type="text" class="form-control" v-model="name" v-highlight />
    <div class="d-flex flex-row-reverse mt-3">
      <button class="btn btn-primary" type="submit">Ok</button>
      <button
        class="btn btn-outline-secondary mx-2"
        @click.prevent="$emit('close')"
      >
        Cancel
      </button>
    </div>
  </form>
</template>

<script>
import { nextTick } from "@vue/runtime-core";
import filesApi from "../../api/files";
export default {
  props: {
    file: {
      type: Object,
      required: true,
    },
  },
  directives: {
    highlight: {
      mounted(el) {
        nextTick(() => {
          let countName = el.value.split(".").slice(0, -1).join(".").length;
          el.setSelectionRange(0, countName);
          el.focus();
        });
      },
    },
  },
  data() {
    return {
      name: this.file.name,
    };
  },
  methods: {
    async handleSubmit() {
      try {
        const { data } = await filesApi.update(
          { ...this.file, name: this.name },
          this.file.id
        );
        this.$emit("file-updated", data);
        this.$emit("close");
      } catch (error) {
        console.log(error);
      }
    },
  },
  emits: ["file-updated", "close"],
};
</script>