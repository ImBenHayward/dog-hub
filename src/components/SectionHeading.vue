<template>
  <div
    class="heading-container"
    :class="{ 'favourites-heading': isFavourites }"
  >
    <h2>{{ text }}</h2>
    <v-icon icon="mdi-heart" color="red"></v-icon>
  </div>
</template>

<script>
export default {
  name: "SectionHeading",
  props: {
    text: String,
    isFavourites: Boolean,
  },

  data: () => ({}),
};
</script>

<style scoped>
.heading-container {
  display: flex;
  flex-direction: horizontal;
  align-items: center;
  justify-content: space-between;
  margin: 10px 0px 20px;
}
.favourites-heading {
  justify-content: start;
  flex-direction: row-reverse;
  gap: 20px;
}
</style>
