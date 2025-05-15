<template>
  <header
    class="fixed top-0 left-0 right-0 bg-white border-b border-gray-100 z-50"
  >
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="flex items-center justify-between h-16">
        <router-link to="/" class="flex items-center">
          <ShieldIcon class="h-8 w-8 text-blue-600 mr-2" />
          <span class="text-xl font-bold text-gray-900">ФейкНет</span>
        </router-link>

        <div class="flex items-center space-x-4">
          <router-link
            to="/login"
            class="px-4 py-2 text-gray-600 hover:text-gray-900 font-medium"
          >
            Войти
          </router-link>
          <router-link
            to="/signup"
            class="px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-all duration-300 font-medium"
          >
            Регистрация
          </router-link>
        </div>
      </div>
    </div>
  </header>
</template>

<script setup lang="ts">
import { ShieldIcon } from "lucide-vue-next";
</script>
