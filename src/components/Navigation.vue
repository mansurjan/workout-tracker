<template>
  <header class="bg-at-light-green text-white">
    <nav
      class="container py-5 px-4 flex flex-col gap-4 items-center justify-between sm:flex-row">
      <div class="flex space-x-4 items-center">
        <img class="w-14" src="../assets/images/dumbbell-light.png" alt="" />
        <h1 class="text-lg">Workout Tracker</h1>
      </div>
      <ul class="flex space-x-10">
        <router-link class="cursor-pointer" :to="{ name: 'Home' }"
          >Home</router-link
        >
        <router-link v-if="user" class="cursor-pointer" :to="{ name: 'Create' }"
          >Create</router-link
        >
        <router-link v-if="!user" class="cursor-pointer" :to="{ name: 'Login' }"
          >Login</router-link
        >
        <li v-if="user" @click="logout" class="cursor-pointer">Logout</li>
      </ul>
    </nav>
  </header>
</template>

<script>
import store from "../store/index";
import { computed } from "vue";
import { useRouter } from "vue-router";
import { supabase } from "../supabase/supabase";
export default {
  setup() {
    // Get user from store
    const user = computed(() => store.state.user);
    // Setup ref to router
    const router = useRouter();

    // Logout function
    const logout = async () => {
      supabase.auth.signOut();
      router.push({ name: "Login" });
    };
    return { logout, user };
  },
};
</script>
