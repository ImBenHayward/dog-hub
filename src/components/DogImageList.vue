<template>
  <div class="dog-list-container">
    <div v-for="dog in computedList" :key="dog.id">
      <DogImage
        :dogItem="dog"
        :user="user"
        @updatedItem="$emit('updatedItem', $event)"
      />
    </div>
  </div>
</template>

<script>
import DogImage from "./DogImage.vue";
import { computed } from 'vue';

export default {
  name: "DogImageList",

  components: {
    DogImage,
  },

  props: {
    dogsList: Array,
    user: Object,
  },

  setup(props) {
    const computedList = computed(() => {
      return props.dogsList.slice(0, 10);
    })

    return {
      computedList
    };
  },
};
</script>

<style scoped>
.dog-list-container {
  margin: 20px 0px 20px 0px;
  display: flex;
  flex-direction: row;
  gap: 10px;
  flex-wrap: wrap;
}
</style>
