<template>
  <div
    v-if="status && status !== 'pending'"
    :class="content.bgColor + ' ' + content.borderColor"
    class="border rounded-lg p-6 shadow-md animate-fadeIn transition-all duration-500"
  >
    <div class="flex items-center mb-4">
      <component :is="content.icon" size="24" class="mr-3" />
      <h3 :class="content.color + ' text-xl font-semibold'">
        {{ content.title }}
      </h3>
    </div>

    <p class="text-gray-700 mb-6">{{ content.description }}</p>

    <div v-if="confidence > 0" class="mb-6">
      <div class="flex items-center justify-between mb-2">
        <span class="text-sm font-medium text-gray-700">Уверенность:</span>
        <span class="text-sm font-medium text-gray-700"
          >{{ Math.round(confidence) }}%</span
        >
      </div>
      <div class="w-full bg-gray-200 rounded-full h-2.5">
        <div
          :class="content.confidenceColor"
          class="h-2.5 rounded-full"
          :style="{ width: `${confidence}%` }"
        ></div>
      </div>
    </div>

    <div class="border-t border-gray-200 pt-4 mt-2">
      <h4 class="text-sm font-medium text-gray-700 mb-2">Текст новости:</h4>
      <div
        class="bg-white p-3 rounded border border-gray-200 text-gray-700 max-h-40 overflow-y-auto text-sm"
      >
        {{ newsText }}
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from "vue";

import { AlertTriangle, CheckCircle, Clock } from "lucide-vue-next";

const props = defineProps({
  status: String,
  newsText: String,
  confidence: {
    type: Number,
    default: 0,
  },
});

const content = computed(() => {
  switch (props.status) {
    case "fake":
      return {
        icon: AlertTriangle,
        title: "Фейковая новость",
        description:
          "Наш алгоритм определил, что эта новость, вероятно, содержит недостоверную информацию.",
        color: "text-red-600",
        bgColor: "bg-red-50",
        borderColor: "border-red-200",
        confidenceColor: "bg-red-500",
      };
    case "true":
      return {
        icon: CheckCircle,
        title: "Достоверная новость",
        description:
          "Наш алгоритм определил, что эта новость, вероятно, содержит достоверную информацию.",
        color: "text-green-600",
        bgColor: "bg-green-50",
        borderColor: "border-green-200",
        confidenceColor: "bg-green-500",
      };
    default:
      return {
        icon: Clock,
        title: "Проверка",
        description: "Новость находится в процессе проверки.",
        color: "text-blue-600",
        bgColor: "bg-blue-50",
        borderColor: "border-blue-200",
        confidenceColor: "bg-blue-500",
      };
  }
});
</script>
