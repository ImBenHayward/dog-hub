<template>
  <div class="dog-image-container" @click="toggleFavourite">
    <img :src="dogItem.imageSrc" class="dog-image" />
    <div class="favourite-icon-container">
      <v-icon
        end
        icon="mdi-heart"
        :color="[isUserFavourite ? 'red' : 'grey']"
      ></v-icon>
    </div>
  </div>
</template>

<script>
import { computed } from "vue";

export default {
  name: "DogImage",
  emits: ["updatedItem"],
  props: {
    dogItem: Object,
    user: Object,
  },

  setup(props, { emit }) {
    const updatedFavourites = computed({
      get() {
        return props.user.favourites;
      },
      set(newValue) {
        emit("updatedItem", { ...props.user, favourites: newValue });
      },
    });

    const isUserFavourite = computed(() => {
      return props.user.favourites.includes(props.dogItem.id);
    });

    function toggleFavourite() {
      if (isUserFavourite.value) {
        const index = props.user.favourites.indexOf(props.dogItem.id);
        updatedFavourites.value = [
          ...updatedFavourites.value.slice(0, index),
          ...updatedFavourites.value.slice(index + 1),
        ];
      } else {
        updatedFavourites.value = [
          ...updatedFavourites.value,
          props.dogItem.id,
        ];
      }
    }

    return {
      updatedFavourites,
      toggleFavourite,
      isUserFavourite,
    };
  },
};
</script>

<style scoped>
.dog-image-container {
  position: relative;
  width: 100px;
  height: 100px;
  cursor: pointer;
  border-radius: 5px;
  overflow: hidden;
}

.dog-image {
  width: 100px;
  height: 100px;
  object-fit: cover;
}

.favourite-icon-container {
  position: absolute;
  bottom: 5px;
  right: 5px;
}
</style>
