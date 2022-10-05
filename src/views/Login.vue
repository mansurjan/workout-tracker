<template>
  <div class="max-w-screen-sm mx-auto px-4 py-10">
    <!-- Error handling -->
    <div v-if="errorMessage" class="mb-10 p-4 rounded-md bg-light-grey">
      <p class="text-red-500">{{ errorMessage }}</p>
    </div>

    <!-- Register -->
    <form
      @submit.prevent="login"
      class="p-8 flex flex-col bg-light-grey rounded-md shadow-lg">
      <h1 class="text-3xl text-at-light-green mb-4">Login</h1>

      <div class="flex flex-col mb-2">
        <label for="email" class="mb-1 text-sm text-at-light-green"
          >Email</label
        >
        <input
          id="email"
          class="p-2 text-gray-500 focus:outline-none"
          required
          type="text"
          v-model="email" />
      </div>

      <div class="flex flex-col mb-2">
        <label for="password" class="mb-1 text-sm text-at-light-green"
          >Password</label
        >
        <input
          id="password"
          class="p-2 text-gray-500 focus:outline-none"
          required
          type="password"
          v-model="password" />
      </div>

      <button
        type="submit"
        class="mt-6 py-2 px-6 rounded-sm text-sm self-start border-solid border-2 hover:border-at-light-green text-white bg-at-light-green border-transparent duration-200 hover:text-at-light-green hover:bg-white">
        Login
      </button>

      <router-link class="text-sm text-center mt-6" :to="{ name: 'Register' }">
        Don't have an account?
        <span class="text-at-light-green">Register</span>
      </router-link>
    </form>
  </div>
</template>

<script>
import { ref } from "vue";
import { supabase } from "../supabase/supabase.js";
import { useRouter } from "vue-router";

export default {
  name: "login",
  setup() {
    // Create data / vars
    const router = useRouter();
    const email = ref(null);
    const password = ref(null);
    const errorMessage = ref(null);
    // Login function
    const login = async () => {
      try {
        let { user, error } = await supabase.auth.signIn({
          email: email.value,
          password: password.value,
        });

        if (error) throw error;
        router.push({ name: "Home" });
      } catch (error) {
        errorMessage.value = `Error: ${error.message}`;

        setTimeout(() => {
          errorMessage.value = null;
        }, 50000);
      }
    };
    return { email, password, errorMessage, login };
  },
};
</script>
