<template>
  <div class="mx-auto max-w-screen-md px-4 py-10">
    <!-- Status Message -->
    <div
      v-if="statusMessage || errorMessage"
      class="p-4 mb-10 bg-at-light-grey rounded-md shadow-lg">
      <p class="text-at-light-green">{{ statusMessage }}</p>
      <p class="text-red-500">{{ errorMessage }}</p>
    </div>

    <!-- Create Workout -->
    <div class="flex items-start p-8 bg-light-grey rounded-md shadow-lg">
      <form
        @submit.prevent="createWorkout"
        class="flex flex-col gap-y-5 w-full">
        <h1 class="text-2xl text-at-light-green">Record Workout</h1>
        <div class="flex flex-col">
          <label for="workout-name" class="mb-1 text-sm text-at-light-green"
            >Workout Name</label
          >
          <input
            class="p-2 text-grey-500 focus:outline-none"
            type="text"
            required
            id="workout-name"
            v-model="workoutName" />
        </div>

        <div class="flex flex-col">
          <label for="workout-type" class="mb-1 text-sm text-at-light-green"
            >Workout Type</label
          >
          <select
            class="p-2 text-grey-500 focus:outline-none"
            required
            id="workout-type"
            v-model="workoutType">
            <option value="select-workout">Select Workout</option>
            <option value="strength">Strength Training</option>
            <option value="cardio">Cardio Training</option>
          </select>
        </div>

        <div v-if="workoutType === 'strength'" class="flex flex-col gap-y-4">
          <div
            class="flex flex-col gap-x-6 gap-y-2 relative md:flex-row"
            v-for="(item, index) in exercises"
            :key="index">
            <div class="flex flex-col md:w-1/3">
              <label
                for="exercise-name"
                class="mb-1 text-sm text-at-light-green"
                >Exercise
              </label>
              <input
                id="exercise-name"
                type="text"
                class="p-2 text-grey-500 focus:outline-none"
                v-model="item.exercise"
                required />
            </div>
            <div class="flex flex-col flex-1">
              <label for="sets" class="mb-1 text-sm text-at-light-green"
                >Sets</label
              >
              <input
                type="text"
                class="p-2 w-full text-grey-500 focus:outline-none"
                v-model="item.sets"
                required />
            </div>

            <div class="flex flex-col flex-1">
              <label for="reps" class="mb-1 text-sm text-at-light-green"
                >Reps
              </label>
              <input
                required
                type="text"
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.reps" />
            </div>

            <div class="flex flex-col flex-1">
              <label for="weight" class="mb-1 text-sm text-at-light-green"
                >Weight (LB's)
              </label>
              <input
                required
                type="text"
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.weight" />
            </div>
            <img
              @click="deleteExercise(item.id)"
              src="@/assets/images/trash-light-green.png"
              class="h-4 w-auto absolute -left-5 cursor-pointer" />
          </div>
          <button
            @click="addExercise"
            type="button"
            class="mt-6 py-2 px-6 rounded-sm text-sm self-start border-solid border-2 hover:border-at-light-green text-white bg-at-light-green border-transparent duration-200 hover:text-at-light-green hover:bg-white">
            Add New exercise
          </button>
        </div>

        <div v-if="workoutType === 'cardio'" class="flex flex-col gap-y-4">
          <div
            class="flex flex-col gap-x-6 gap-y-2 relative md:flex-row"
            v-for="(item, index) in exercises"
            :key="index">
            <div class="flex flex-col md:w-1/3">
              <label for="cardio-type" class="mb-1 text-sm text-at-light-green"
                >Type
              </label>
              <select
                id="cardio-type"
                class="p-2 w-full text-grey-500 focus:outline-none"
                v-model="item.cardioType">
                <option value="running">Running</option>
                <option value="walking">Walking</option>
              </select>
            </div>
            <div class="flex flex-col flex-1">
              <label for="distance" class="mb-1 text-sm text-at-light-green"
                >Distance (kms)</label
              >
              <input
                id="distance"
                type="text"
                class="p-2 w-full text-grey-500 focus:outline-none"
                v-model="item.distance"
                required />
            </div>

            <div class="flex flex-col flex-1">
              <label for="duration" class="mb-1 text-sm text-at-light-green"
                >Duration (mins)
              </label>
              <input
                id="duration"
                required
                type="text"
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.duration" />
            </div>

            <div class="flex flex-col flex-1">
              <label for="pace" class="mb-1 text-sm text-at-light-green"
                >Pace (km/hour)
              </label>
              <input
                required
                type="text"
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.pace" />
            </div>
            <img
              @click="deleteExercise(item.id)"
              src="@/assets/images/trash-light-green.png"
              class="h-4 w-auto absolute -left-5 cursor-pointer" />
          </div>
          <button
            @click="addExercise"
            type="button"
            class="mt-6 py-2 px-6 rounded-sm text-sm self-start border-solid border-2 hover:border-at-light-green text-white bg-at-light-green border-transparent duration-200 hover:text-at-light-green hover:bg-white">
            Add New Exercise
          </button>
        </div>

        <button
          type="submit"
          class="mt-6 py-2 px-6 rounded-sm text-sm self-start border-solid border-2 hover:border-at-light-green text-white bg-at-light-green border-transparent duration-200 hover:text-at-light-green hover:bg-white">
          Record Workout
        </button>
      </form>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import { uid } from "uid";
import { supabase } from "../supabase/supabase";

export default {
  name: "create",
  setup() {
    // Create data
    const workoutName = ref("");
    const workoutType = ref("select-workout");
    const exercises = ref([]);
    const statusMessage = ref(null);
    const errorMessage = ref(null);
    // Add exercise
    const addExercise = () => {
      if (workoutType.value === "strength") {
        exercises.value.push({
          id: uid(),
          exercise: "",
          sets: "",
          reps: "",
          weight: "",
        });
        return;
      }
      exercises.value.push({
        id: uid(),
        cardioType: "",
        distance: "",
        duration: "",
        pace: "",
      });
    };
    // Delete exercise

    const deleteExercise = (id) => {
      console.log(id);
      if (exercises.value.length > 1) {
        exercises.value = exercises.value.filter(
          (exercise) => exercise.id !== id
        );
        return;
      }

      errorMessage.value =
        "Error: Can not be removed, need to have at least one exercise";

      setTimeout(() => {
        errorMessage.value = false;
      }, 3000);
    };

    // Listens for chaging of workout type input

    // Create workout

    const createWorkout = async () => {
      try {
        const { error } = await supabase.from("workouts").insert([
          {
            workoutName: workoutName.value,
            workoutType: workoutType.value,
            exercises: exercises.value,
          },
        ]);
        if (error) throw error;
        statusMessage.value = "Succes: Workout Created!";
        workoutName.value = null;
        workoutType.value = "select-workout";
        exercises.value = [];
        setTimeout(() => {
          statusMessage.value = false;
        }, 5000);
      } catch (error) {
        errorMessage.value = `Error: ${error.message}`;
        setTimeout(() => {
          errorMessage.value = false;
        }, 5000);
      }
    };

    return {
      workoutName,
      workoutType,
      errorMessage,
      exercises,
      statusMessage,
      addExercise,
      deleteExercise,
      createWorkout,
    };
  },
};
</script>
