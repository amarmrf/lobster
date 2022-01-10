<template>
  <div class="max-w-screen-md mx-auto px-4 py-10">
    <!-- Status Message -->
    <div
      v-if="statusMsg || errorMsg"
      class="mb-10 p-4 bg-light-grey rounded-md shadow-lg"
    >
      <p class="text-at-light-green">
        {{ statusMsg }}
      </p>
      <p class="text-red-500">{{ errorMsg }}</p>
    </div>

    <!-- Create -->
    <div class="p-8 flex items-start bg-light-grey rounded-md shadow-lg">
      <!-- Form -->
      <form @submit.prevent="createExercise" class="flex flex-col gap-y-5 w-full">
        <h1 class="text-2xl text-at-light-green"> Epochs Creation </h1>

        <div class="flex flex-col gap-y-4">
          <div
            class="flex flex-col gap-x-6 gap-y-2 relative"
            v-for="(item, index) in epochs"
            :key="index"
          >
            <div class="flex flex-col ">
              <label for="epoch-name" class="mb-1 text-sm text-at-light-green"
                >Epoch {{ index + 1 }} Title
              </label>
              <input
                required
                type="text"
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.title"
              />
            </div>
            <img
              @click="deleteEpoch(item.id)"
              src="@/assets/images/trash-light-green.png"
              class="h-4 w-auto absolute -left-5 cursor-pointer"
              alt=""
            />
          </div>
          <button
            @click="addEpoch"
            type="button"
            class="mt-6 py-2 px-6 rounded-sm self-start text-sm
            text-white bg-at-light-green duration-200 border-solid
            border-2 border-transparent hover:border-at-light-green hover:bg-white
            hover:text-at-light-green"
          >
            Add Epoch
          </button>
        </div>

     
        <button
          type="submit"
          class="mt-6 py-2 px-6 rounded-sm self-start text-sm
      text-white bg-at-light-green duration-200 border-solid
      border-2 border-transparent hover:border-at-light-green hover:bg-white
      hover:text-at-light-green"
        >
         {{'Save & Continue'}}
        </button>
      </form>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import { uid } from "uid";
import { supabase } from "../supabase/init";

export default {
  name: "create-past",
  setup() {
    // Create data
    const exerciseName = ref("");
    const exerciseType = ref("select-exercise");
    const epochs = ref([]);
    const statusMsg = ref(null);
    const errorMsg = ref(null);
    const dataLoaded = ref(null);
  
    // Get data
    const getData = async () => {
      try {
        const { data: items, error } = await supabase.from("past_epochs").select("*");
        if (error) throw error;
        console.log(items)
        epochs.value = items;
        dataLoaded.value = true;
      } catch (error) {
        console.warn(error.message);
      }
    };

    // Run data function
    getData(); 

    // add sort epochs by sequence_number
    

    // Add epoch
    const addEpoch = () => {
      if (epochs.value.length < 7) {
        epochs.value.push({
          id: uid(),
          title: "",
          sequence_number: epochs.value.length
        });
      }
    };

    // Delete epoch
    const deleteEpoch = (id) => {
      if (epochs.value.length > 1) {
        epochs.value = epochs.value.filter((epoch) => epoch.id !== id);
        return;
      }
      errorMsg.value = "Error: Cannot remove, need to at least have one epoch";
      setTimeout(() => {
        errorMsg.value = false;
      }, 5000);
    };

    // Listens for chaging of exercise type input
    const exerciseChange = () => {
      epochs.value = [];
      addEpoch();
    };

    // Create exercise
    const createExercise = async () => {
      try {
        const { error } = await supabase.from("past_epochs").insert([
          {
            epochs: epochs.value,
          },
        ]);
        if (error) throw error;
        statusMsg.value = "Succes: Exercise Created!";
        exerciseName.value = null;
        exerciseType.value = "select-exercise";
        epochs.value = [];
        setTimeout(() => {
          statusMsg.value = false;
        }, 5000);
      } catch (error) {
        errorMsg.value = `Error: ${error.message}`;
        setTimeout(() => {
          errorMsg.value = false;
        }, 5000);
      }
    };

    return {
      exerciseName,
      exerciseType,
      epochs,
      statusMsg,
      errorMsg,
      addEpoch,
      exerciseChange,
      deleteEpoch,
      createExercise,
    };
  },
};
</script>
