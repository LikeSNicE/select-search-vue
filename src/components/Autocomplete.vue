<script setup lang="ts">
import { computed, ref, watch } from "vue";

interface Country {
  name: string;
}

interface props {
  source: Country[];
  modelValue: string;
}

const props = withDefaults(defineProps<props>(), {
  source: () => [],
});

const emit = defineEmits<{
  (e: "update:modelValue", value: string): void;
}>();

const search = ref("");
const isOpen = ref(false);

// Синхронизируем search с modelValue
watch(
  () => props.modelValue,
  () => {
    search.value = props.modelValue;
  },
);

const searchResults = computed(() => {
  if (search.value === "" && isOpen.value) {
    return props.source;
  }

  if (search.value === "") {
    return [];
  }

  return props.source.filter((item) =>
    item.name.toLowerCase().includes(search.value.toLowerCase()),
  );
});

const setSelected = (item: string) => {
  isOpen.value = false;
  search.value = item;
  emit("update:modelValue", search.value);
};

const handleInput = (event: Event) => {
  const target = event.target;

  if (target instanceof HTMLInputElement) {
    isOpen.value = true;
    search.value = target.value;
    emit("update:modelValue", search.value);
  }
};

const clearSearch = () => {
  search.value = "";
  emit("update:modelValue", search.value);
};

const toggleDropdown = () => {
  isOpen.value = !isOpen.value;
};
</script>

<template>
  <div class="w-full relative">
    <input
      type="text"
      :value="modelValue"
      @input="handleInput"
      class="w-full bg-[#3c3c3c] rounded-md px-3 py-4 focus:outline-none focus:ring-2 focus:ring-[#1d8954] transition duration-300 ease-in-out"
      placeholder="Select a country"
      style="padding: 8px"
      @click="toggleDropdown"
    />

    <svg
      @click="clearSearch"
      class="w-5 h-5 absolute bottom-1/2 translate-y-1/2 right-3 cursor-pointer"
      viewBox="0 -0.5 25 25"
      fill="none"
      xmlns="http://www.w3.org/2000/svg"
    >
      <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
      <g
        id="SVGRepo_tracerCarrier"
        stroke-linecap="round"
        stroke-linejoin="round"
      ></g>
      <g id="SVGRepo_iconCarrier">
        <path
          d="M6.96967 16.4697C6.67678 16.7626 6.67678 17.2374 6.96967 17.5303C7.26256 17.8232 7.73744 17.8232 8.03033 17.5303L6.96967 16.4697ZM13.0303 12.5303C13.3232 12.2374 13.3232 11.7626 13.0303 11.4697C12.7374 11.1768 12.2626 11.1768 11.9697 11.4697L13.0303 12.5303ZM11.9697 11.4697C11.6768 11.7626 11.6768 12.2374 11.9697 12.5303C12.2626 12.8232 12.7374 12.8232 13.0303 12.5303L11.9697 11.4697ZM18.0303 7.53033C18.3232 7.23744 18.3232 6.76256 18.0303 6.46967C17.7374 6.17678 17.2626 6.17678 16.9697 6.46967L18.0303 7.53033ZM13.0303 11.4697C12.7374 11.1768 12.2626 11.1768 11.9697 11.4697C11.6768 11.7626 11.6768 12.2374 11.9697 12.5303L13.0303 11.4697ZM16.9697 17.5303C17.2626 17.8232 17.7374 17.8232 18.0303 17.5303C18.3232 17.2374 18.3232 16.7626 18.0303 16.4697L16.9697 17.5303ZM11.9697 12.5303C12.2626 12.8232 12.7374 12.8232 13.0303 12.5303C13.3232 12.2374 13.3232 11.7626 13.0303 11.4697L11.9697 12.5303ZM8.03033 6.46967C7.73744 6.17678 7.26256 6.17678 6.96967 6.46967C6.67678 6.76256 6.67678 7.23744 6.96967 7.53033L8.03033 6.46967ZM8.03033 17.5303L13.0303 12.5303L11.9697 11.4697L6.96967 16.4697L8.03033 17.5303ZM13.0303 12.5303L18.0303 7.53033L16.9697 6.46967L11.9697 11.4697L13.0303 12.5303ZM11.9697 12.5303L16.9697 17.5303L18.0303 16.4697L13.0303 11.4697L11.9697 12.5303ZM13.0303 11.4697L8.03033 6.46967L6.96967 7.53033L11.9697 12.5303L13.0303 11.4697Z"
          fill="currentColor"
        ></path>
      </g>
    </svg>

    <Transition name="fade">
      <ul
        class="w-full max-h-60 rounded-md absolute overflow-y-auto scrollbar-custom"
        v-show="searchResults.length && isOpen"
        style="margin-top: 8px"
      >
        <li
          class="cursor-pointer transition-colors bg-[#3c3c3c] text-white hover:bg-[#646464]"
          style="padding: 8px"
          v-for="result in searchResults"
          :key="result.name"
          @click="setSelected(result.name)"
        >
          {{ result.name }}
        </li>
      </ul>
    </Transition>
  </div>
</template>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 1s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.scrollbar-custom {
  scrollbar-width: auto;
  scrollbar-color: #646464 #3c3c3c;
}
</style>
