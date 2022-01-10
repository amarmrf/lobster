<template>
  <div v-if="dataLoaded" class="container mt-10 px-4">
    <!-- No Data -->
    <div v-if="data.length === 0" class="w-full flex flex-col items-center">
      <h1 class="text-2xl">Looks empty here...</h1>
      <router-link
        class="mt-6 py-2 px-6 rounded-sm  text-sm
      text-white bg-at-light-green duration-200 border-solid
      border-2 border-transparent hover:border-at-light-green hover:bg-white
      hover:text-at-light-green"
        :to="{ name: 'Create' }"
        >Create Workout</router-link
      >
    </div>

    <!-- Data -->
    <div
      v-else
      class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-6"
    >
      <router-link
        class="flex flex-col items-center bg-light-grey p-8 shadow-md cursor-pointer"
        :to="{ name: 'View-Workout', params: { workoutId: epoch.id } }"
        v-for="(epoch, index) in data"
        :key="index"
      >
        <!-- Cardio Img -->
        <img
          v-if="epoch.sequence_number % 2"
          src="@/assets/images/running-light-green.png"
          class="h-24 w-auto"
          alt=""
        />

        <!-- Strength Training -->
        <img
          v-else
          src="@/assets/images/dumbbell-light-green.png"
          class="h-24 w-auto"
          alt=""
        />

        <p
          class="mt-6 py-1 px-3 text-xs text-white bg-at-light-green shadow-md rounded-lg"
        >
          {{ `${epoch.sequence_number || 0 }${epoch.sequence_number === 1 ? 'st' : epoch.sequence_number === 2 ? 'nd' : epoch.sequence_number === 3 ? 'rd' : 'th'} Epoch` }}
        </p>

        <h1 class="mt-8 mb-2 text-center text-xl text-at-light-green">
          {{ epoch.title }}
        </h1>
      </router-link>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import { supabase } from "../supabase/init";

export default {
  name: "home-view",
  components: {},
  setup() {
    // Create data / vars
    const data = ref([]);
    const dataLoaded = ref(null);
   
    // Get data
    const getData = async () => {
      try {
        const { data: epochs, error } = await supabase.from("past_epochs").select("*");
        if (error) throw error;
        data.value = epochs;
        console.log(epochs)
        dataLoaded.value = true;
      } catch (error) {
        console.warn(error.message);
      }
    };

    // Run data function
    getData();

    return { data, dataLoaded };
  },
};
</script>
