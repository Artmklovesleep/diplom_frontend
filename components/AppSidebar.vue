<script setup>
import { useRoute } from "vue-router";
import { User, ChevronUp } from "lucide-vue-next";
import { toast } from "vue-sonner";

const props = defineProps(["items"]);

const route = useRoute();

const user = useSanctumUser();

const isActive = (url) => {
  return route.path === url;
};

const { $api } = useNuxtApp();

const isDropdownOpen = ref(false);

function toggleDropdown() {
  isDropdownOpen.value = !isDropdownOpen.value;
}

async function successLogout() {
  const tokenCookie = useCookie("sanctum.token.cookie");
  tokenCookie.value = null;

  // Очищаем пользователя (если нужно)
  user.value = null;

  toast("Вы успешно вышли из аккаунта", {
    description: "Сейчас мы вас переадресуем на страницу авторизации",
  });

  await navigateTo("/login");
}

async function logout(data) {
  try {
    const response = await $api("/logout", {
      method: "POST",
      onResponse: ({ response }) => {
        if ([200, 201].includes(response.status)) {
          successLogout();
        }
      },
    });
  } catch (error) {}
}
</script>

<template>
  <div class="w-60 border-r border-gray-200 h-screen flex flex-col">
    <!-- Logo -->
    <div class="p-4 border-b border-gray-200">
      <NuxtLink
        to="/"
        class="text-xl font-bold text-blue-600 flex items-center"
      >
        <Shield size="24" class="mr-2" />
        ФейкНет
      </NuxtLink>
    </div>

    <!-- Navigation -->
    <nav class="flex-1 p-2">
      <div v-for="item in items" :key="item.title" class="mb-1">
        <NuxtLink
          :to="item.url"
          :class="[
            'flex items-center px-4 py-3 hover:bg-gray-100 transition-colors duration-200 rounded',
            { 'bg-gray-100': isActive(item.url) },
          ]"
        >
          <div
            :class="{
              'text-blue-600': isActive(item.url),
              'text-gray-500': !isActive(item.url),
            }"
          >
            <component :is="item.icon" size="20" />
          </div>
          <span
            :class="{
              'font-medium text-blue-600': isActive(item.url),
              'text-gray-700': !isActive(item.url),
            }"
          >
            {{ item.title }}
          </span>
        </NuxtLink>
      </div>
    </nav>

    <!-- Footer -->
    <div class="border-t border-gray-200 p-4 relative">
      <div
        class="flex items-center justify-between cursor-pointer"
        @click="toggleDropdown"
      >
        <div class="flex items-center">
          <User size="20" class="text-gray-500 mr-2" />
          <span class="text-gray-700">{{ user?.name }}</span>
        </div>
        <ChevronUp
          :class="{ 'transform rotate-180': isDropdownOpen }"
          size="20"
          class="text-gray-500"
        />
      </div>

      <!-- Dropdown menu (открывается ВВЕРХ) -->
      <div
        v-if="isDropdownOpen"
        class="mt-[-85px] bg-white shadow rounded-md py-1 absolute w-full left-0 z-10"
      >
        <button
          @click.prevent="logout"
          class="block w-full text-left px-4 py-2 text-sm text-red-600 hover:bg-gray-100"
        >
          Выйти
        </button>
      </div>
    </div>
  </div>
</template>
