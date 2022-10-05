<template>
  <div class="max-w-screen-sm mx-auto px-4 py-10">
    <!-- Error handling -->
    <div v-if="errorMessage" class="mb-10 p-4 rounded-md bg-light-grey">
      <p class="text-red-500">{{ errorMessage }}</p>
    </div>

    <!-- Register -->
    <form
      @submit.prevent="register"
      class="p-8 flex flex-col bg-light-grey rounded-md shadow-lg">
      <h1 class="text-3xl text-at-light-green mb-4">Register</h1>

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

      <div class="flex flex-col mb-2">
        <label for="confirmPassword" class="mb-1 text-sm text-at-light-green"
          >Confirm Password</label
        >
        <input
          id="confirmPassword"
          class="p-2 text-gray-500 focus:outline-none"
          required
          type="confirmPassword"
          v-model="confirmPassword" />
      </div>

      <button
        type="submit"
        class="mt-6 py-2 px-6 rounded-sm text-sm self-start border-solid border-2 hover:border-at-light-green text-white bg-at-light-green border-transparent duration-200 hover:text-at-light-green hover:bg-white">
        Register
      </button>

      <router-link class="text-sm text-center mt-6" :to="{ name: 'Login' }">
        Already have an account?
        <span class="text-at-light-green">Login</span>
      </router-link>
    </form>
  </div>
</template>

<script>
import { ref } from "vue";
import { supabase } from "../supabase/supabase.js";
import { useRouter } from "vue-router";

export default {
  name: "register",
  setup() {
    // Create data / vars
    const router = useRouter();
    const email = ref(null);
    const password = ref(null);
    const confirmPassword = ref(null);
    const errorMessage = ref(null);
    // Register function
    const register = async () => {
      if (password.value === confirmPassword.value) {
        try {
          let { user, error } = await supabase.auth.signUp({
            email: email.value,
            password: password.value,
          });

          if (error) throw error;
          router.push({ name: "Login" });
        } catch (error) {
          errorMessage.value = error.message;
        }
      }
      errorMessage.value = "Error: Passwords do not match";
      setTimeout(() => {
        errorMessage.value = null;
      }, 5000);
    };
    return { email, password, confirmPassword, errorMessage, register };
  },
};
</script>
