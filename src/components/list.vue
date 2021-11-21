<template>
  <div v-if="!reRendering" class="list-of-movies">
    <movie v-for="(movie, index) in movies" :key="index" :movie="movie" :movieIndex="index"/>
  </div>
</template>

<script setup>
import { defineProps, toRefs, watch, ref, nextTick } from "vue";
import movie from "./movie";

const props = defineProps({
  movies: Array,
})
const reRendering = ref(false)

const { movies } = toRefs(props);
watch(movies, () => {
  reRendering.value = true

  nextTick(() => reRendering.value = false)
})

</script>
