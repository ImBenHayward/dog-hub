<template>
  <v-app>
    <v-main>
      <div class="loading-overlay" v-if="loading">
        <atom-spinner
          :animation-duration="1000"
          :size="60"
          :color="'#ff1d5e'"
        />
      </div>
      <v-container class="main-container">
        <SectionHeading text="Dog Breeds" :isFavourites="false" />
        <SearchBar @searchTerm="(payload) => searchDogs(payload)" />
        <DogImageList
          :dogsList="filteredDogList"
          :user="currentUser"
          @updatedItem="(payload) => upsertUser(payload)"
        />
        <v-divider></v-divider>
        <SectionHeading text="Favourites" :isFavourites="true" />
        <DogImageList
          :dogsList="filteredFavourites"
          :user="currentUser"
          @updatedItem="(payload) => upsertUser(payload)"
        />
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import { ref, computed, onMounted } from "vue";
import SectionHeading from "./components/SectionHeading.vue";
import SearchBar from "./components/SearchBar.vue";
import DogImageList from "./components/DogImageList.vue";
import { AtomSpinner } from "epic-spinners";
import { v4 as uuidv4 } from "uuid";

export default {
  name: "App",

  components: {
    SectionHeading,
    SearchBar,
    DogImageList,
    AtomSpinner,
  },

  setup() {
    onMounted(async () => {
      const response = await fetch(
        "https://nsby657zyb.execute-api.eu-west-2.amazonaws.com/DisplayDogsFunction"
      );
      dogsList.value = await response.json();
      loading.value = false;

      if (localStorage.getItem("userId") === null) {
        localStorage.setItem("userId", uuidv4());
      }

      const localUserId = localStorage.getItem("userId");

      // For the purposes of this challenge, this is mocking authentication
      getUser(localUserId)
        .then((data) => {
          // checks to see if the user currently exists
          Object.keys(data).length === 0
            ? (currentUser.value.id = localUserId)
            : (currentUser.value = data);
        })
        .catch((error) => {
          console.error(error);
        });
    });

    const loading = ref(true);
    const searchTerm = ref("");
    const dogsList = ref([]);
    const currentUser = ref({
      id: "",
      favourites: [],
    });

    function getUser(userId) {
      return new Promise((resolve, reject) => {
        fetch(
          `https://nsby657zyb.execute-api.eu-west-2.amazonaws.com/getUserFunction/${userId}`,
          {
            method: "get",
            headers: {
              "Content-Type": "application/json",
            },
          }
        )
          .then((response) => {
            return response.text();
          })
          .then((data) => {
            resolve(data ? JSON.parse(data) : {});
          })
          .catch((error) => {
            reject(error);
          });
      });
    }

    async function upsertUser(updatedUser) {
      if (updatedUser) {
        currentUser.value = updatedUser;
      }
      const response = await fetch(
        "https://nsby657zyb.execute-api.eu-west-2.amazonaws.com/addUserFunction",
        {
          method: "post",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify(currentUser.value),
        }
      );
      currentUser.value = await response.json();
    }

    // retrieves the users favourite dogs with additional search filter functionality
    const filteredFavourites = computed(() => {
      if (!searchTerm.value) {
        return dogsList.value.filter((item) =>
          currentUser.value.favourites.includes(item.id)
        );
      }

      let favourites = dogsList.value.filter((item) =>
        currentUser.value.favourites.includes(item.id)
      );

      return favourites.filter((item) => {
        return item.breed
          .toLowerCase()
          .includes(searchTerm.value.toLowerCase());
      });
    });

    // assigns emitted search term to data ref and calls filter computed function
    function searchDogs(emittedSearchTerm) {
      searchTerm.value = emittedSearchTerm;
      this.filter;
    }

    // filters main dog list
    const filteredDogList = computed(() => {
      if (!searchTerm.value) return dogsList.value;
      return dogsList.value.filter((item) => {
        return item.breed
          .toLowerCase()
          .includes(searchTerm.value.toLowerCase());
      });
    });

    return {
      loading,
      dogsList,
      currentUser,
      searchTerm,
      getUser,
      upsertUser,
      filteredFavourites,
      searchDogs,
      filteredDogList,
    };
  },
};
</script>

<style scoped>
.main-container {
  max-width: 800px;
}

.loading-overlay {
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 10;
  position: fixed;
  height: 100%;
  width: 100%;
  background: rgba(255, 255, 255, 0.5);
  backdrop-filter: blur(10px);
}
</style>
