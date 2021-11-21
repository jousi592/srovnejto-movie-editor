<template>
  <Suspense>
    <movieList :movies="movies" />
  </Suspense>
</template>

<script setup>
import { ref, onBeforeMount } from "vue";
import movieList from "./components/list.vue";
import { storedMoviesLsKey } from "./constants";

const movies = ref([]);
const loadMovies = async () => {
  const storedData = JSON.parse(window.localStorage.getItem(storedMoviesLsKey))

  movies.value = storedData || await fetch(
    "https://run.mocky.io/v3/a147d572-eb76-4e33-ad48-374480d2d8ff"
  ).then(async (data) => {
    data = await data.json()
    window.localStorage.setItem(storedMoviesLsKey, JSON.stringify(data))

    return data
  });
};

onBeforeMount(async () => {
  window.addEventListener("refreshDataEvent", loadMovies);

  await loadMovies();
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
