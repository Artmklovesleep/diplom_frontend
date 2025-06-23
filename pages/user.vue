<template>
  <div class="container mx-auto px-4 py-8">
    <h1 class="text-3xl font-bold text-gray-800 mb-6">Личные данные</h1>

    <div class="bg-white rounded-lg shadow-md overflow-hidden">
      <!-- Информация о пользователе -->
      <div class="p-6 border-b border-gray-200">
        <div class="flex items-center">
          <div
            class="w-20 h-20 rounded-full bg-blue-100 flex items-center justify-center text-blue-600"
          >
            <User size="40" />
          </div>
          <div class="ml-6">
            <h2 class="text-2xl font-semibold text-gray-800">
              {{ user?.name }}
            </h2>
            <p class="text-gray-600 flex items-center mt-1">
              <Mail size="16" class="mr-2" />
              {{ user?.email }}
            </p>
          </div>
        </div>
      </div>

      <!-- Статистика использования -->
      <div class="p-6" v-if="statistics && Object.keys(statistics).length > 0">
        <h3 class="text-lg font-medium text-gray-800 mb-4">
          Статистика использования
        </h3>

        <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
          <div class="bg-gray-50 p-4 rounded-lg border border-gray-200">
            <div class="flex items-center text-blue-600 mb-2">
              <Shield size="20" class="mr-2" />
              <span class="font-medium">Всего проверок</span>
            </div>
            <p class="text-2xl font-bold text-gray-800">
              {{ statistics.total_checks }}
            </p>
          </div>

          <div class="bg-gray-50 p-4 rounded-lg border border-gray-200">
            <div class="flex items-center text-red-500 mb-2">
              <AlertTriangle size="20" class="mr-2" />
              <span class="font-medium">Обнаружено фейков</span>
            </div>
            <p class="text-2xl font-bold text-gray-800">
              {{ statistics.fake_news }}
            </p>
          </div>

          <div class="bg-gray-50 p-4 rounded-lg border border-gray-200">
            <div class="flex items-center text-green-500 mb-2">
              <CheckCircle size="20" class="mr-2" />
              <span class="font-medium">Подтверждено новостей</span>
            </div>
            <p class="text-2xl font-bold text-gray-800">
              {{ statistics.confirmed_news }}
            </p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import {
  User,
  Mail,
  Shield,
  AlertTriangle,
  CheckCircle,
} from "lucide-vue-next";
definePageMeta({
  layout: "admin",
});

import { useSanctumUser } from "#imports";
const { $api } = useNuxtApp();

const user = useSanctumUser();
const statistics = ref({
  total_checks: 0,
  fake_news: 0,
  confirmed_news: 0,
});

async function getUserStatistics() {
  try {
    // const response = await $api("/history/statistics");
    const response = await $api("/statistics");
    if (response && typeof response === "object") {
      return {
        total_checks: response.total_checks || 0,
        fake_news: response.fake_news || 0,
        confirmed_news: response.confirmed_news || 0,
      };
    }
    return {
      total_checks: 0,
      fake_news: 0,
      confirmed_news: 0,
    };
  } catch (error) {
    console.error("Ошибка получения статистики:", error);
    return {
      total_checks: 0,
      fake_news: 0,
      confirmed_news: 0,
    };
  }
}

onMounted(async () => {
  const data = await getUserStatistics();
  statistics.value = data;
});
</script>
