<template>
  <div
    class="min-h-screen bg-gradient-to-b from-white to-blue-50 w-full flex flex-col justify-center items-center"
  >
    <div class="max-w-md mx-auto px-4 sm:px-6 lg:px-8 py-12">
      <div class="bg-white rounded-2xl shadow-lg p-8 border border-gray-100">
        <div class="text-center mb-6">
          <h1 class="text-3xl font-bold text-gray-900">Регистрация</h1>
          <p class="text-sm text-gray-600 mt-2">
            Введите свои данные для создания аккаунта
          </p>
        </div>

        <form @submit.prevent="onSubmit" class="space-y-6">
          <FormField
            name="firstName"
            v-slot="{ componentField }"
            :validate-on-blur="!isFieldDirty"
          >
            <FormItem v-auto-animate>
              <FormLabel>Имя</FormLabel>
              <FormControl>
                <Input
                  id="first-name"
                  placeholder="Данил"
                  v-bind="componentField"
                  class="w-full px-4 py-3 rounded-xl border border-gray-200 focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                />
              </FormControl>
              <FormMessage />
            </FormItem>
          </FormField>

          <FormField
            name="lastName"
            v-slot="{ componentField }"
            :validate-on-blur="!isFieldDirty"
          >
            <FormItem v-auto-animate>
              <FormLabel>Фамилия</FormLabel>
              <FormControl>
                <Input
                  id="last-name"
                  placeholder="Фалалеев"
                  v-bind="componentField"
                  class="w-full px-4 py-3 rounded-xl border border-gray-200 focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                />
              </FormControl>
              <FormMessage />
            </FormItem>
          </FormField>

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
            <span v-if="!isLoading">Создать аккаунт</span>
            <div
              v-if="isLoading"
              class="animate-spin h-5 w-5 border-t-2 border-b-2 border-white rounded-full"
            ></div>
          </Button>
        </form>

        <div class="mt-6 text-center text-sm text-gray-600">
          Уже есть аккаунт?
          <NuxtLink
            to="/login"
            class="ml-1 text-blue-600 hover:text-blue-700 underline"
          >
            Войти
          </NuxtLink>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { vAutoAnimate } from "@formkit/auto-animate/vue";

import { toTypedSchema } from "@vee-validate/zod";
import { useForm, useSetFieldError } from "vee-validate";
import * as z from "zod";
import { toast } from "vue-sonner";

definePageMeta({
  sanctum: {
    guestOnly: true,
  },
});

// Схема валидации формы
const formSchema = toTypedSchema(
  z.object({
    firstName: z
      .string()
      .min(2, { message: "Имя должно быть не короче 2 символов" }),
    lastName: z
      .string()
      .min(2, { message: "Фамилия должна быть не короче 2 символов" }),
    email: z.string().email({ message: "Введите корректный email" }),
    password: z
      .string()
      .min(6, { message: "Пароль должен быть не менее 6 символов" }),
  })
);

// Форма
const { isFieldDirty, handleSubmit, setFieldError, setErrors } = useForm({
  validationSchema: formSchema,
});

// Обработчик отправки формы
const onSubmit = handleSubmit((values) => {
  registerUser(values);
});

const { $api } = useNuxtApp();

const isLoading = ref(false);

async function successRegistration() {
  toast("Вы успешно зарегистрировались", {
    description: "Сейчас мы вас переадресуем на страницу авторизации",
  });

  await navigateTo("/login");
}

async function registerUser(data) {
  isLoading.value = true;
  try {
    const response = await $api("/register", {
      method: "POST",
      body: JSON.stringify({
        name: `${data.firstName} ${data.lastName}`,
        email: data.email,
        password: data.password,
      }),
      onResponse: ({ response }) => {
        if ([200, 201].includes(response.status)) {
          successRegistration();
        }
      },
    });
  } catch (error) {
    handleApiErrors(error.data?.errors);
  } finally {
    isLoading.value = false; // Скрываем loader
  }
}

function handleApiErrors(errors) {
  setErrors(errors);
}
</script>
