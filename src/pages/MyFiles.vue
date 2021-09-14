<template>
  <div class="container py-3">
    <ActionBar
      :selected-count="selectedItems.length"
      @remove="handleRemove"
      @rename="showModal = true"
    />

    <div class="d-flex justify-content-between align-items-center py-2">
      <h6 class="text-muted mb-0">Files</h6>
      <SortToggle @sort-change="handleSortChange($event)" />
    </div>

    <teleport to="#search-form">
      <SearchForm v-model="q" />
    </teleport>
    <FilesList :files="files" @select-change="handleSelectChange($event)" />
    <AppToast
      :show="toast.show"
      :message="toast.message"
      type="success"
      position="bottom-left"
      @hide="toast.show = false"
    />
    <AppModal
      title="Rename"
      :show="showModal && selectedItems.length === 1"
      @hide="showModal = false"
    >
      <FileRenameForm
        :file="selectedItems[0]"
        @close="showModal = false"
        @file-updated="handleFileUpdated($event)"
      />
    </AppModal>
  </div>
</template>

<script>
import filesApi from "../api/files";
import SearchForm from "../components/SearchForm.vue";
import ActionBar from "../components/ActionBar.vue";
import SortToggle from "../components/SortToggle.vue";
import FilesList from "../components/files/FilesList.vue";
import FileRenameForm from "../components/files/FileRenameForm.vue";
import { ref, reactive, watchEffect, toRef } from "vue";
const fetchFiles = async (query) => {
  try {
    const { data } = await filesApi.index(query);
    return data;
  } catch (error) {
    console.log(error);
  }
};

const removeItem = async (item, files) => {
  try {
    const response = await filesApi.delete(item.id);
    if (response.status === 200 || response.status === 204) {
      const index = files.value.findIndex((file) => file.id === item.id);
      files.value.splice(index, 1);
    }
  } catch (error) {
    console.log(error);
  }
};
export default {
  components: { ActionBar, FilesList, SortToggle, SearchForm, FileRenameForm },
  setup() {
    const files = ref([]);
    const query = reactive({
      _sort: "name",
      _order: "asc",
      q: "",
    });

    const selectedItems = ref([]);
    const toast = reactive({
      show: false,
      message: "",
    });

    const showModal = ref(false);

    const handleSelectChange = (items) => {
      selectedItems.value = Array.from(items);
    };
    const handleSortChange = (payload) => {
      query._sort = payload.column;
      query._order = payload.order;
    };

    const handleRemove = () => {
      if (confirm("Are you sure?")) {
        selectedItems.value.forEach((item) => removeItem(item, files));
        selectedItems.value.splice(0);
        toast.show = true;
        toast.message = "Selected item(s) successfully removed";
      }
    };

    const handleFileUpdated = (file) => {
      const oldFile = selectedItems.value[0];
      const index = files.value.findIndex((item) => item.id === file.id);
      files.value.splice(index, 1, file);
      toast.show = true;
      toast.message = `File '${oldFile.name}' renamed to '${file.name}'`;
    };

    watchEffect(async () => (files.value = await fetchFiles(query)));

    return {
      files,
      handleSortChange,
      q: toRef(query, "q"),
      selectedItems,
      handleSelectChange,
      handleRemove,
      toast,
      showModal,
      handleFileUpdated,
    };
  },
};
</script>
