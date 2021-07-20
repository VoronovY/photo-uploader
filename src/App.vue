<template>
  <PhotoUploader v-model="files" />
</template>

<script>
import { ref } from "vue";
import PhotoUploader from "@/components/PhotoUploader.vue";
export default {
  components: {
    PhotoUploader
  },
  setup() {
    const files = ref([]);
    return { files };
  }
};
</script>

<style lang="scss"></style>
