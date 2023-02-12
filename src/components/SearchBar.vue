<template>
  <v-text-field
    :loading="loading"
    density="compact"
    variant="solo"
    label="Search dog breeds"
    append-inner-icon="mdi-magnify"
    single-line
    hide-details
    v-model="searchTerm"
    @click:append-inner="emitSearchTerm()"
    @keyup.enter="emitSearchTerm()"
  ></v-text-field>
</template>

<script>
import { ref } from "vue";

export default {
  name: "SearchBar",
  emits: ["searchTerm"],
  setup(props, { emit }) {
    const searchTerm = ref("");

    function emitSearchTerm() {
      emit("searchTerm", searchTerm.value);
    }

    return {
      searchTerm,
      emitSearchTerm,
    };
  },
};
</script>

<style scoped>
.search-bar {
  display: flex;
  flex-direction: horizontal;
}
</style>
