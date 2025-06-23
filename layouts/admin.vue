<template>
  <div class="flex h-screen overflow-hidden relative">
    <!-- Sidebar -->
    <Transition name="slide-x">
      <SidebarProvider
        v-if="isSidebarOpen"
        :class="[
          'w-60 bg-white shadow-lg transition-all duration-300 ease-in-out',
          windowWidth >= 768
            ? 'block z-auto'
            : 'absolute top-0 left-0 h-full z-50',
        ]"
      >
        <AppSidebar :items="items" @toggle-sidebar="toggleSidebar" />
      </SidebarProvider>
    </Transition>

    <!-- Main content -->
    <div
      class="flex flex-col w-full bg-gray-50 transition-all duration-300 ease-in-out relative z-0"
    >
      <!-- Trigger button for sidebar -->
      <button
        @click="toggleSidebar"
        class="p-4 text-blue-600 focus:outline-none md:hidden self-start"
      >
        ☰
      </button>

      <!-- Main area -->
      <main class="p-2 w-full text-sm flex-1 overflow-y-auto">
        <slot />
        <Toaster />
      </main>
    </div>
  </div>
</template>

<script setup>
import { Toaster } from "@/components/ui/sonner";
import AppSidebar from "@/components/AppSidebar.vue";
import { ref, computed, onMounted, onUnmounted } from "vue";
import { useRoute } from "vue-router";
import { Bell, History, User } from "lucide-vue-next";

const route = useRoute();

// Используем `ref(null)` вместо `window.innerWidth`
const windowWidth = ref(null);

// Проверяем, доступен ли window
if (import.meta.client) {
  windowWidth.value = window.innerWidth;

  const handleResize = () => {
    windowWidth.value = window.innerWidth;
    if (windowWidth.value >= 768) {
      isSidebarOpen.value = true;
    }
  };

  onMounted(() => {
    window.addEventListener("resize", handleResize);
    if (windowWidth.value < 768) {
      isSidebarOpen.value = false;
    }
  });

  onUnmounted(() => {
    window.removeEventListener("resize", handleResize);
  });
}

// Меню
const items = ref([
  {
    title: "Проверка новостей",
    url: "/check",
    icon: Bell,
  },
  {
    title: "История",
    url: "/history",
    icon: History,
  },
  {
    title: "Личные данные",
    url: "/user",
    icon: User,
  },
]);

// Состояние сайдбара
const isSidebarOpen = ref(windowWidth.value >= 768);

function toggleSidebar() {
  if (!isSidebarOpen.value || windowWidth.value <= 768) {
    isSidebarOpen.value = !isSidebarOpen.value;
  }
}

// Активное название страницы для хедера (если нужно)
const activeTitle = computed(() => {
  const activeItem = items.value.find((item) => route.path === item.url);
  return activeItem ? activeItem.title : "Главная";
});
</script>

<style scoped>
.slide-x-enter-active,
.slide-x-leave-active {
  transition: transform 0.3s ease;
}

.slide-x-enter-from,
.slide-x-leave-to {
  transform: translateX(-100%);
}
</style>
