<template>
  <div
    class="min-h-screen bg-gradient-to-b from-white to-blue-50 w-full flex flex-col justify-center items-center"
  >
    <div class="max-w-md mx-auto px-4 sm:px-6 lg:px-8 py-12">
      <div class="bg-white rounded-2xl shadow-lg p-8 border border-gray-100">
        <div class="text-center mb-6">
          <h1 class="text-3xl font-bold text-gray-900">Вход</h1>
          <p class="text-sm text-gray-600 mt-2">
            Введите email и пароль для входа
          </p>
        </div>

        <form @submit.prevent="onSubmit" class="space-y-6">
          <FormField
            name="email"
            v-slot="{ componentField }"
            :validate-on-blur="!isFieldDirty"
          >
            <FormItem v-auto-animate>
              <FormLabel>Email</FormLabel>
              <FormControl>
                <Input
                  id="email"
                  type="email"
                  placeholder="test@test.com"
                  v-bind="componentField"
                  required
                  class="w-full px-4 py-3 rounded-xl border border-gray-200 focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                />
              </FormControl>
              <FormMessage />
            </FormItem>
          </FormField>

          <FormField
            name="password"
            v-slot="{ componentField }"
            :validate-on-blur="!isFieldDirty"
          >
            <FormItem v-auto-animate>
              <FormLabel>Пароль</FormLabel>
              <FormControl>
                <Input
                  id="password"
                  type="password"
                  v-bind="componentField"
                  required
                  class="w-full px-4 py-3 rounded-xl border border-gray-200 focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                />
              </FormControl>
              <FormMessage />
            </FormItem>
          </FormField>

          <Button
            type="submit"
            class="w-full py-3 bg-blue-600 text-white rounded-xl hover:bg-blue-700 transition-colors duration-300 flex items-center justify-center"
            :loading="isLoading"
          >
            <span v-if="!isLoading">Войти</span>
            <div
              v-if="isLoading"
              class="animate-spin h-5 w-5 border-t-2 border-b-2 border-white rounded-full"
            ></div>
          </Button>
        </form>

        <div class="mt-6 text-center text-sm text-gray-600">
          Нет аккаунта?
          <NuxtLink
            to="/signup"
            class="ml-1 text-blue-600 hover:text-blue-700 underline"
          >
            Зарегистрироваться
          </NuxtLink>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { vAutoAnimate } from "@formkit/auto-animate/vue";

import { toTypedSchema } from "@vee-validate/zod";
import { useForm, useField } from "vee-validate";
import { h } from "vue";
import * as z from "zod";
import { toast } from "vue-sonner";

// Схема валидации формы
const formSchema = toTypedSchema(
  z.object({
    email: z.string().email({ message: "Введите корректный email" }),
    password: z
      .string()
      .min(6, { message: "Пароль должен быть не менее 6 символов" }),
  })
);

const isLoading = ref(false);

// Форма
const { isFieldDirty, handleSubmit, setFieldError, setErrors } = useForm({
  validationSchema: formSchema,
});

const { login } = useSanctumAuth();

async function successAuth() {
  toast("Вы успешно авторизовались", {
    description: "Сейчас мы вас переадресуем на страницу авторизации",
  });
}

// Обработчик отправки формы
const onSubmit = handleSubmit(async (values) => {
  isLoading.value = true;

  try {
    const response = await login(values);
    await successAuth();
  } catch (error) {
    setFieldError("password", error.data.message);
  } finally {
    isLoading.value = false; // Скрываем loader
  }
});
</script>

<style></style>
