<template>
  <div class="photo-uploader">
    <div class="photo-uploader__modal" v-if="showModal.show" @click="hideModal">
      <div class="photo-uploader__modal-content">
        Вы уже выбрали файл с таким именем, выберите другой файл
      </div>
    </div>
    <div
      class="photo-uploader__wrapper"
      :class="{ 'photo-uploader__wrapper--drag': isDragStarted }"
    >
      <input
        ref="input"
        class="photo-uploader__input"
        type="file"
        multiple
        title=""
        @change="uploadFile"
        @dragenter="isDragStarted = true"
        @dragleave="isDragStarted = false"
      />
      {{ isDragStarted ? "" : uploadText }}
      <img
        v-show="isDragStarted"
        src="@/assets/images/upload.png"
        class="photo-uploader__icon"
        alt="Загрузите фото"
      />
    </div>
    <div class="photo-uploader__button-container">
      <button @click="toBase64" class="photo-uploader__button">
        Отправить
      </button>
    </div>
    <div class="photo-uploader__photos">
      <div
        v-for="(photo, index) in modelValue"
        :key="index"
        class="photo-uploader__photo"
        :class="{ 'photo-uploader__photo--none': !isImg(photo) }"
      >
        <img :src="getSrc(photo)" :alt="`Фотография ${index + 1}`" />
      </div>
    </div>
  </div>
</template>

<script>
import { computed, ref, toRefs } from "vue";
export default {
  props: {
    modelValue: {
      type: Array,
      required: true
    }
  },
  emits: ["update:modelValue"],
  setup(props, { emit }) {
    const { modelValue } = toRefs(props);

    const showModal = ref({ show: false });
    const input = ref(null);

    const isDragStarted = ref(false);

    const uploadFile = ({ currentTarget }) => {
      if (currentTarget.files) {
        const newFileName = currentTarget.files[0].name;
        const fileArray = modelValue.value;
        const sameFileName = fileArray.find(el => el.name === newFileName);
        if (!sameFileName) {
          emit("update:modelValue", [
            ...modelValue.value,
            ...Array.from(currentTarget.files)
          ]);
        } else {
          showModal.value.show = true;
        }
      }
      if (input.value) {
        input.value.value = "";
      }
      isDragStarted.value = false;
    };

    const getSrc = photo => {
      return URL.createObjectURL(photo);
    };

    const toBase64 = async () => {
      const imageArray = modelValue.value.filter(
        el => el.type.split("/")[0] === "image"
      );

      imageArray.forEach(el => {
        const reader = new FileReader();
        reader.onload = function() {
          console.log(reader.result);
        };
        reader.readAsDataURL(el);
      });
    };

    const isImg = photo => {
      const fileType = photo.type.split("/")[0];
      return fileType === "image";
    };
    const uploadText = computed(() => {
      return `Загрузите  ${
        modelValue.value.length === 0 ? "вашу фотографию" : "ваши фотографии"
      }`;
    });
    const hideModal = () => {
      showModal.value.show = false;
    };
    return {
      isDragStarted,
      uploadText,
      input,
      uploadFile,
      getSrc,
      isImg,
      toBase64,
      showModal,
      hideModal
    };
  }
};
</script>

<style lang="scss">
.photo-uploader {
  &__wrapper {
    margin: 0 auto;
    margin-top: 100px;
    position: relative;
    max-width: 800px;
    text-align: center;
    height: 150px;
    display: flex;
    justify-content: center;
    align-items: center;
    border: 4px dotted rgb(223, 111, 111);
    border-radius: 10px;
    color: rgba(rgb(7, 7, 7), 0.5);
    &--drag {
      color: rgba(#fff, 0);
      border-color: #777;
    }
  }
  &__input {
    cursor: pointer;
    position: absolute;
    z-index: 2;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    width: 100%;
    opacity: 0;
  }
  &__button-container {
    margin: 0 auto;
    max-width: 800px;
    background: rgba(103, 100, 100, 0.217);
    height: 50px;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  &__button {
    height: 40px;
    width: 200px;
    font-size: 24px;
    color: rgb(255, 255, 255);
    border: 2px solid rgb(94, 130, 84);
    border-radius: 10px;
    background-color: rgb(46, 46, 46);
    &:hover {
      background-color: rgb(99, 142, 64);
    }
    &:active {
      padding: calc(0.1em + 1px) 0.1em calc(0.1em - 1px);
      box-shadow: inset 0 -1px 1px rgba(0, 0, 0, 0.1),
        inset 0 1px 2px rgba(0, 0, 0, 0.3),
        inset 0 0 0 60px rgba(255, 255, 0, 0.45);
    }
  }
  &__photos {
    display: flex;
    margin-top: 20px;
    justify-content: space-around;
    flex-wrap: wrap;
  }
  &__photo {
    margin-top: 20px;
    position: relative;
    display: flex;
    align-items: center;
    border-radius: 10px;
    height: 300px;
    width: 200px;
    overflow: hidden;
    box-shadow: 0px 2px 5px 0px #777;
    &--none {
      display: none;
      width: 0;
    }
    img {
      width: 100%;
    }
  }

  &__icon {
    opacity: 0.3;
    width: 50px;
  }
  &__modal {
    z-index: 99;
    top: 0;
    bottom: 0;
    right: 0;
    left: 0;
    background: rgba(35, 1, 1, 0.462);
    position: fixed;
    display: flex;
  }
  &__modal-content {
    font-size: 25x;
    padding: 20px;
    display: flex;
    align-items: center;
    margin: auto;
    background: rgb(211, 211, 211);
    border-radius: 12px;
    min-width: 200px;
    max-width: 300px;
    min-height: 100px;
  }
}
</style>
