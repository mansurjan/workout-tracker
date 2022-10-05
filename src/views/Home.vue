<template>
  <div v-if="dataLoaded" class="container mt-10 px-4">
    <!-- No Data -->
    <div v-if="data.length === 0" class="w-full flex flex-col items-center">
      <h1 class="text-2xl">Looks empty here...</h1>
      <router-link
        class="mt-6 py-2 px-6 rounded-sm text-sm border-solid border-2 hover:border-at-light-green text-white bg-at-light-green border-transparent duration-200 hover:text-at-light-green hover:bg-white"
        :to="{ name: 'Create' }"
        >Create Workout</router-link
      >
    </div>
    <div
      v-else
      class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-6">
      <!-- Card -->
      <router-link
        class="flex flex-col items-center p-8 shadow-md bg-light-grey rounded-md cursor-pointer"
        :to="{ name: 'ViewWorkout', params: { workoutId: workout.id } }"
        v-for="(workout, index) in data"
        :key="index">
        <!-- Cardio Image -->
        <img
          v-if="workout.workoutType === 'cardio'"
          class="h-24 w-auto"
          src="../assets/images/running-light-green.png"
          alt="" />

        <!-- Strength training -->
        <img
          v-else
          class="h-24 w-auto"
          src="../assets/images/dumbbell-light-green.png"
          alt="" />

        <p
          class="mt-6 py-1 px-3 text-xs text-white bg-at-light-green shadow-md rounded-lg">
          {{ workout.workoutType }}
        </p>

        <h1 class="mt-8 mb-2 text-center text-xl text-at-light-green">
          {{ workout.workoutName }}
        </h1>
      </router-link>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import { supabase } from "../supabase/supabase";
export default {
  name: "Home",
  components: {},
  setup() {
    // Create data / vars
    const data = ref([]);
    const dataLoaded = ref(null);

    const getData = async () => {
      try {
        const { data: workouts, error } = await supabase
          .from("workouts")
          .select("*");
        if (error) throw error;
        data.value = workouts;
        dataLoaded.value = true;
        console.log(data.value);
      } catch (error) {
        console.log(error.message);
      }
    };
    // Get data

    // Run data function
    getData();

    return { data, dataLoaded };
  },
};
</script>
