<template>
  <div class="min-h-screen bg-gradient-to-b from-white to-blue-50">
    <Header />

    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 pt-32 pb-16">
      <!-- Hero Section -->
      <div class="text-center max-w-3xl mx-auto mb-10">
        <div
          class="inline-flex items-center justify-center p-3 bg-blue-100 rounded-2xl mb-6"
        >
          <ShieldIcon class="h-12 w-12 text-blue-600" />
        </div>
        <h1 class="text-4xl md:text-5xl font-bold mb-6 text-gray-900">
          Проверка новостей на достоверность
        </h1>
        <p class="text-base md:text-xl text-gray-600 mb-8">
          Простой и быстрый способ определить правдивость информации
        </p>
        <router-link
          to="/login"
          class="px-6 py-3 md:px-8 md:py-4 bg-blue-600 text-white rounded-xl font-medium hover:bg-blue-700 transition-all duration-300 inline-flex items-center justify-center"
        >
          Проверить новость
          <ArrowRightIcon class="ml-2 h-5 w-5" />
        </router-link>
      </div>

      <!-- How it works -->
      <div class="mb-10">
        <h2
          class="text-2xl md:text-3xl font-bold text-center text-gray-900 mb-6"
        >
          Как это работает?
        </h2>
        <div class="grid md:grid-cols-3 gap-6">
          <div
            class="bg-white p-4 md:p-8 rounded-xl shadow-sm border border-gray-100 text-center"
          >
            <div
              class="inline-flex items-center justify-center p-2 md:p-3 bg-blue-100 rounded-xl mb-4"
            >
              <SearchIcon class="h-5 md:h-6 w-5 md:w-6 text-blue-600" />
            </div>
            <h3 class="text-lg md:text-xl font-semibold mb-2 text-gray-900">
              Шаг 1
            </h3>
            <p class="text-sm md:text-base text-gray-600">
              Введите текст новости в форму для проверки
            </p>
          </div>
          <div
            class="bg-white p-4 md:p-8 rounded-xl shadow-sm border border-gray-100 text-center"
          >
            <div
              class="inline-flex items-center justify-center p-2 md:p-3 bg-blue-100 rounded-xl mb-4"
            >
              <ClockIcon class="h-5 md:h-6 w-5 md:w-6 text-blue-600" />
            </div>
            <h3 class="text-lg md:text-xl font-semibold mb-2 text-gray-900">
              Шаг 2
            </h3>
            <p class="text-sm md:text-base text-gray-600">
              Подождите несколько секунд, пока система анализирует информацию
            </p>
          </div>
          <div
            class="bg-white p-4 md:p-8 rounded-xl shadow-sm border border-gray-100 text-center"
          >
            <div
              class="inline-flex items-center justify-center p-2 md:p-3 bg-blue-100 rounded-xl mb-4"
            >
              <CheckCircleIcon class="h-5 md:h-6 w-5 md:w-6 text-blue-600" />
            </div>
            <h3 class="text-lg md:text-xl font-semibold mb-2 text-gray-900">
              Шаг 3
            </h3>
            <p class="text-sm md:text-base text-gray-600">
              Получите результат: правда или фейк
            </p>
          </div>
        </div>
      </div>

      <!-- Features -->
      <div
        class="bg-white rounded-2xl shadow-sm border border-gray-100 p-4 md:p-8 mb-10"
      >
        <div class="grid md:grid-cols-2 gap-4 md:gap-8">
          <div>
            <h2 class="text-xl md:text-2xl font-bold text-gray-900 mb-6">
              Преимущества сервиса
            </h2>
            <ul class="space-y-4">
              <li class="flex items-center text-gray-600">
                <CheckCircleIcon
                  class="h-4 md:h-5 w-4 md:w-5 text-green-500 mr-2"
                />
                Быстрая проверка за несколько секунд
              </li>
              <li class="flex items-center text-gray-600">
                <CheckCircleIcon
                  class="h-4 md:h-5 w-4 md:w-5 text-green-500 mr-2"
                />
                Простой и понятный интерфейс
              </li>
              <li class="flex items-center text-gray-600">
                <CheckCircleIcon
                  class="h-4 md:h-5 w-4 md:w-5 text-green-500 mr-2"
                />
                История всех проверок
              </li>
            </ul>
          </div>
          <div class="bg-blue-50 rounded-xl p-4 md:p-6">
            <h3 class="text-lg md:text-xl font-semibold text-gray-900 mb-4">
              Важно знать
            </h3>
            <p class="text-sm md:text-base text-gray-600">
              Система анализирует текст новости и определяет вероятность того,
              что информация является достоверной или фейковой. Результат
              основан на анализе множества факторов и исторических данных.
            </p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
definePageMeta({
  layout: "default",
  sanctum: {
    guestOnly: true,
  },
});
import Header from "../components/Header.vue";
import {
  ShieldIcon,
  ArrowRightIcon,
  SearchIcon,
  ClockIcon,
  CheckCircleIcon,
} from "lucide-vue-next";
</script>

<style scoped>
/* Адаптивные шрифты */
@media (max-width: 768px) {
  .text-4xl {
    font-size: 1.5rem;
  }
  .text-2xl {
    font-size: 1.25rem;
  }
  .text-xl {
    font-size: 1.125rem;
  }
  .text-lg {
    font-size: 1rem;
  }
}
</style>
