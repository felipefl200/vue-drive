<template>
  <div class="card shadow uploader-popup" v-if="items.length">
    <div class="card-header bg-dark py-3">
      <div class="d-flex justify-content-between align-items-center">
        <span class="text-light">{{ uploadingStatus }}</span>
        <PopupControls
          @toggle="showPopupBody = !showPopupBody"
          @close="handleClose"
        />
      </div>
    </div>
    <div class="upload-items" v-if="showPopupBody">
      <ul class="list-group">
        <UploadItem        
          v-for="item in items"
          :key="`item-${item.id}`"
          :item="item"
        />   
      </ul>
    </div>
  </div>
</template>

<script>
import { computed, ref, watch } from "@vue/runtime-core";
import states from "./states";
import PopupControls from "./PopupControls.vue";
import UploadItem from '../item/UploadItem.vue'

const uploadingItemsCount = (items) => {
  return computed(() => {
    return items.value.filter(
      (item) => item.state === states.WAITING || item.state === states.UPLOADING
    ).length;
  }).value;
};

const randomId = () => {
  return Math.random().toString(36).substr(2, 9);
};

const getUploadItems = (files) => {
  return Array.from(files).map((file) => ({
    id: randomId,
    file,
    progress: 0,
    state: states.WAITING,
    response: null,
  }));
};

export default {
  setup(props, { emit }) {
    const items = ref([]);
    const showPopupBody = ref(true);

    const handleClose = () => {
      if (confirm("Cancel all uploads ?")) items.value.splice(0);
    };

    const uploadingStatus = computed(() => {
      return `Uploading ${uploadingItemsCount(items)} items`;
    });

    watch(
      () => props.files,
      (newFiles) => {
        items.value.unshift(...getUploadItems(newFiles));
      }
    );

    return { items, uploadingStatus, showPopupBody, handleClose };
  },
  props: {
    files: {
      type: Object,
      required: true,
    },
  },
  components: {
    PopupControls,
    UploadItem
  },
};
</script>
<style scoped>
.uploader-popup {
  width: 400px;
  position: fixed;
  bottom: 20px;
  right: 20px;
  z-index: 999;
}
</style>