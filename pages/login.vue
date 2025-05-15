<template>
  <div class="w-full h-screen flex items-center justify-center bg-gray-100">
    <div
      class="w-[350px] p-8 space-y-6 rounded-lg shadow-lg bg-card border border-border"
    >
      <div class="text-center">
        <h1 class="text-3xl font-bold">Вход</h1>
      </div>

      <form @submit.prevent="onSubmit" class="space-y-4">
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
                placeholder="m@example.com"
                v-bind="componentField"
                required
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
              />
            </FormControl>
            <FormMessage />
          </FormItem>
        </FormField>

        <Button type="submit" class="w-full">
          <span v-if="!isLoading">Войти</span>
          <div
            v-if="isLoading"
            class="animate-spin rounded-full h-5 w-5 border-b-2 border-secondary"
          ></div>
        </Button>
      </form>

      <div class="text-center text-sm">
        Нет аккаунта?
        <NuxtLink
          to="/signup"
          class="underline text-primary hover:text-primary/80"
        >
          Зарегистрироваться
        </NuxtLink>
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
