<template>
  <div
    class="bg-white p-6 mb-6 transition-all duration-300 border border-gray-200 rounded-xl"
  >
    <form @submit.prevent="onSubmit">
      <div class="mb-4">
        <label
          for="news-text"
          class="block text-sm font-medium text-gray-700 mb-2"
          >Текст новости</label
        >
        <textarea
          id="news-text"
          class="w-full max-w-full px-4 py-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 transition-colors min-h-[200px] resize-y overflow-x-auto"
          placeholder="Вставьте или введите текст новости здесь..."
          v-model="newsText"
          :disabled="isSubmitting"
          maxlength="10000"
        ></textarea>

        <p v-if="error" class="mt-2 text-sm text-red-600">{{ error }}</p>
      </div>

      <div class="flex justify-end">
        <button
          type="submit"
          :disabled="isSubmitting || isOverLimit"
          class="px-6 py-3 rounded-lg font-medium flex items-center transition-all duration-300 bg-blue-600 text-white hover:bg-blue-700 shadow-md hover:shadow-lg"
        >
          <svg
            v-if="isSubmitting"
            class="animate-spin -ml-1 mr-2 h-4 w-4 text-white"
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
          >
            <circle
              class="opacity-25"
              cx="12"
              cy="12"
              r="10"
              stroke="currentColor"
              stroke-width="4"
            ></circle>
            <path
              class="opacity-75"
              d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
              fill="currentColor"
            ></path>
          </svg>
          <span v-else>Проверить</span>
        </button>
      </div>
    </form>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";

const props = defineProps({
  isSubmitting: Boolean,
});

const emit = defineEmits(["submit-news"]);

const MAX_CHARS = 10000;
const newsText = ref("");
const error = ref("");

const isOverLimit = computed(() => {
  return newsText.value.length > MAX_CHARS;
});

const onSubmit = () => {
  if (!newsText.value.trim()) {
    error.value = "Пожалуйста, введите текст новости";
    return;
  }

  if (newsText.value.length > MAX_CHARS) {
    error.value = `Превышено максимальное количество символов (${MAX_CHARS})`;
    return;
  }

  error.value = "";
  emit("submit-news", newsText.value);
};
</script>
