<template>
  <div class="container py-3">
    <ActionBar />

    <div class="d-flex justify-content-between align-items-center py-2">
      <h6 class="text-muted mb-0">Files</h6>
      <button class="rounded-button">
        <icon-arrow-up />
      </button>
    </div>
    <FilesList :files="files" />
  </div>
</template>

<script>
import filesApi from "../api/files";
import ActionBar from "../components/ActionBar.vue";
import FilesList from "../components/files/FilesList.vue";
import { ref, onMounted } from "vue";
const fetchFiles = async () => {
  try {
    const { data } = await filesApi.index();
    return data;
  } catch (error) {
    console.log(error);
  }
};
export default {
  components: { ActionBar, FilesList },
  setup() {
    const files = ref([]);

    onMounted(async () => (files.value = await fetchFiles()));

    return { files };
  },
};
</script>
