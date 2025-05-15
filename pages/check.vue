<template>
  <div class="p-6">
    <h1 class="text-3xl font-bold text-gray-800 mb-6">Проверка новостей</h1>
    <p class="text-gray-600 mb-6">
      Введите текст новости для проверки на достоверность. Наши алгоритмы
      проанализируют её и определят, содержит ли она фейковую информацию.
    </p>
    <NewsForm @submit-news="handleSubmit" :is-submitting="isSubmitting" />

    <div v-if="status" class="mt-8 animate-fadeIn">
      <NewsResult
        :status="status"
        :news-text="newsText"
        :confidence="confidence"
      />
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import NewsForm from "../components/News/NewsForm.vue";
import NewsResult from "../components/News/NewsResult.vue";

definePageMeta({
  layout: "admin",
});

const newsText = ref("");
const status = ref(null);
const isSubmitting = ref(false);
const confidence = ref(0);

const { $api } = useNuxtApp();

async function verifyNews(text) {
  try {
    const response = await $api("/check-news", {
      method: "POST",
      body: JSON.stringify({ news_text: text }),
    });
    return {
      status: response.verdict,
      confidence: response.confidence_percentage,
    };

    // if (response.status === 201) {
    //   const data = response._data;
    //   console.log("result", data);

    //   return {
    //     status: data.verdict,
    //     confidence: data.confidence_percentage,
    //   };
    // } else {
    //   throw new Error(`Server returned status ${response.status}`);
    // }
  } catch (error) {
    console.error("Ошибка при проверке новости:", error);
    throw error;
  }
}

const handleSubmit = async (text) => {
  newsText.value = text;
  isSubmitting.value = true;
  status.value = null;

  try {
    const result = await verifyNews(text);
    console.log("result", result);
    status.value = result.status;
    confidence.value = result.confidence;
  } catch (error) {
    console.error("Error verifying news:", error);
    status.value = "error"; // Можно отображать сообщение об ошибке
  } finally {
    isSubmitting.value = false;
  }
};
</script>
