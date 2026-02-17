<script setup lang="ts">
import AutoComplete from "./components/Autocomplete.vue";
import countries from "./data/countries.json";
import { ref } from "vue";

const country = ref("");
</script>

<template>
  <div class=" bg-[#202020] flex justify-center items-center flex-col text-white">
    <div class="">
      <h1 class="text-sm" style="margin-bottom: 4px;">Selected: {{ country }}</h1>
      <AutoComplete :source="countries" v-model="country" />
    </div>
  </div>
</template>

<style scoped></style>
