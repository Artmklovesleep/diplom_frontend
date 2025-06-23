<template>
  <div class="p-6">
    <h1
      class="text-2xl md:text-3xl font-bold text-gray-800 mb-6 text-center md:text-left"
    >
      История проверок
    </h1>

    <div
      v-if="history.length === 0"
      class="bg-white rounded-lg shadow-md p-6 text-center"
    >
      <p class="text-gray-600 mb-4">У вас пока нет истории проверок</p>
      <NuxtLink
        to="/"
        class="flex items-center justify-center px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-colors w-full"
      >
        Проверить новость
        <ArrowRightIcon size="16" class="ml-2" />
      </NuxtLink>
    </div>

    <div v-else class="bg-white rounded-lg shadow-md">
      <ul class="divide-y divide-gray-200">
        <li
          v-for="item in history"
          :key="item.id"
          class="p-4 sm:p-5 hover:bg-gray-50 transition-colors cursor-pointer"
          @click="openModal(item)"
        >
          <div class="flex flex-col sm:flex-row sm:items-start">
            <div class="flex-shrink-0 mb-3 sm:mb-0 sm:mt-1">
              <component
                :is="
                  item.status === 'fake' ? AlertTriangleIcon : CheckCircleIcon
                "
                size="20"
                :class="
                  item.status === 'fake' ? 'text-red-500' : 'text-green-500'
                "
              />
            </div>
            <div class="ml-0 sm:ml-3 flex-1">
              <div class="flex flex-col sm:flex-row sm:justify-between">
                <p
                  class="text-sm sm:text-base font-medium text-gray-900 truncate max-w-full sm:max-w-md"
                >
                  {{
                    item.text.length > 100
                      ? `${item.text.substring(0, 100)}...`
                      : item.text
                  }}
                </p>
                <p class="text-xs sm:text-sm text-gray-500 mt-1 sm:mt-0">
                  {{ formatDate(item.date) }}
                </p>
              </div>
              <div class="mt-2 flex flex-wrap items-center">
                <span
                  class="px-2 py-1 text-xs sm:text-sm rounded-full mr-2"
                  :class="{
                    'bg-red-100 text-red-800': item.status === 'fake',
                    'bg-green-100 text-green-800': item.status !== 'fake',
                  }"
                >
                  {{ item.status === "fake" ? "Фейк" : "Достоверно" }}
                </span>
                <span class="text-xs sm:text-sm text-gray-500">
                  {{ item.confidence }}% уверенность
                </span>
              </div>
            </div>
          </div>
        </li>
      </ul>
    </div>

    <!-- Modal -->
    <div
      v-if="selectedItem"
      class="fixed inset-0 z-50 overflow-y-auto"
      style="background-color: rgba(0, 0, 0, 0.5)"
    >
      <div class="flex min-h-screen items-center justify-center p-4">
        <div class="relative w-full max-w-2xl bg-white rounded-lg shadow-xl">
          <div class="p-6">
            <button
              @click="closeModal"
              class="absolute top-4 right-4 text-gray-400 hover:text-gray-600"
            >
              &times;
            </button>

            <h2 class="text-xl font-semibold text-gray-800 mb-4">
              Полная информация о проверке
            </h2>

            <div class="mb-4">
              <label class="block text-sm font-medium text-gray-700"
                >Текст новости:</label
              >
              <div
                class="mt-1 text-gray-900 text-sm whitespace-pre-wrap break-all"
              >
                {{ selectedItem.text }}
              </div>
            </div>

            <div class="mb-4">
              <label class="block text-sm font-medium text-gray-700"
                >Статус:</label
              >
              <div class="mt-1 flex items-center">
                <component
                  :is="
                    selectedItem.status === 'fake'
                      ? AlertTriangleIcon
                      : CheckCircleIcon
                  "
                  size="16"
                  :class="{
                    'text-red-500': selectedItem.status === 'fake',
                    'text-green-500': selectedItem.status !== 'fake',
                  }"
                  class="mr-2"
                />
                <span
                  class="px-2 py-1 text-xs rounded-full"
                  :class="{
                    'bg-red-100 text-red-800': selectedItem.status === 'fake',
                    'bg-green-100 text-green-800':
                      selectedItem.status !== 'fake',
                  }"
                >
                  {{ selectedItem.status === "fake" ? "Фейк" : "Достоверно" }}
                </span>
              </div>
            </div>

            <div class="mb-4">
              <label class="block text-sm font-medium text-gray-700"
                >Уверенность:</label
              >
              <p class="mt-1 text-gray-900">{{ selectedItem.confidence }}%</p>
            </div>

            <div class="mb-4">
              <label class="block text-sm font-medium text-gray-700"
                >Дата проверки:</label
              >
              <p class="mt-1 text-gray-900">
                {{ formatDate(selectedItem.date) }}
              </p>
            </div>

            <div class="flex justify-end">
              <NuxtLink
                to="/"
                class="px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-colors"
              >
                Проверить новую новость
              </NuxtLink>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "#imports";
import { NuxtLink } from "#components";
import {
  CheckCircleIcon as CheckCircle,
  AlertTriangleIcon as AlertTriangle,
  ArrowRightIcon,
} from "lucide-vue-next";

definePageMeta({
  layout: "admin",
});

const history = ref([]);
const selectedItem = ref(null);
const { $api } = useNuxtApp();

// Форматирование даты
function formatDate(dateString) {
  const date = new Date(dateString);
  return date.toLocaleDateString("ru-RU", {
    year: "numeric",
    month: "long",
    day: "numeric",
  });
}

// Получение истории проверок
async function getNewsHistory() {
  try {
    const response = await $api("/history");
    if (Array.isArray(response)) {
      return response.map((item) => ({
        id: item.id,
        text: item.news_text,
        status: item.verdict,
        confidence: parseFloat(item.confidence_percentage),
        date: item.created_at,
      }));
    }
    return [];
  } catch (error) {
    console.error("Ошибка получения истории:", error);
    return [];
  }
}

onMounted(async () => {
  const data = await getNewsHistory();
  history.value = data;
});

function openModal(item) {
  selectedItem.value = item;
}

function closeModal() {
  selectedItem.value = null;
}
</script>
