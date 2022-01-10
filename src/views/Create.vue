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
      <form @submit.prevent="createEpoch" class="flex flex-col gap-y-5 w-full">
        <h1 class="text-2xl text-at-light-green"> New Epoch Creation </h1>
         <div class="flex flex-col">
          <label for="workout-name" class="mb-1 text-sm text-at-light-green"
            >Epoch Title</label
          >
          <input
            type="text"
            required
            class="p-2 text-gray-500 focus:outline-none"
            id="workout-name"
            v-model="epochTitle"
          />
        </div>

        <div class="flex flex-col gap-y-4">
          <div
            class="flex flex-col gap-x-6 gap-y-2 relative"
            v-for="(item, index) in experiences"
            :key="index"
          >
            <div class="flex flex-col ">
              <label for="epoch-name" class="mb-1 text-sm text-at-light-green"
                >Experience {{ index + 1 }} Title
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
            @click="addExperience"
            type="button"
            class="mt-6 py-2 px-6 rounded-sm self-start text-sm
            text-white bg-at-light-green duration-200 border-solid
            border-2 border-transparent hover:border-at-light-green hover:bg-white
            hover:text-at-light-green"
          >
            Add Experience
          </button>
        </div>

     
        <button
          type="submit"
          class="mt-6 py-2 px-6 rounded-sm self-start text-sm
      text-white bg-at-light-green duration-200 border-solid
      border-2 border-transparent hover:border-at-light-green hover:bg-white
      hover:text-at-light-green"
        >
         {{'Create Epoch'}}
        </button>
      </form>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import { uid } from "uid";
import { supabase } from "../supabase/init";
import { useRouter } from "vue-router";

export default {
  name: "create-past",
  setup() {
    // Create data
    const epochTitle = ref("");
    const experiences = ref([]);
    const statusMsg = ref(null);
    const errorMsg = ref(null);
    const router = useRouter();

    // Add epoch
    const addExperience = () => {
      if (experiences.value.length < 6) {
        experiences.value.push({
          id: uid(),
          title: "",
          description: "",
        });
      }
    };

    // Delete epoch
    const deleteEpoch = (id) => {
      if (experiences.value.length > 1) {
        experiences.value = experiences.value.filter((epoch) => epoch.id !== id);
        return;
      }
      errorMsg.value = "Error: Cannot remove, need to at least have one epoch";
      setTimeout(() => {
        errorMsg.value = false;
      }, 5000);
    };

    // Listens for chaging of experience type input
    const experienceChange = () => {
      experiences.value = [];
      addExperience();
    };

    // Create experience
    const createEpoch = async () => {
      try {
        const { error } = await supabase.from("past_epochs").insert([
          {
            title: epochTitle.value,
            sequence_number: experiences.value.length,
            experiences: experiences.value,
          },
        ]);
        if (error) throw error;
        statusMsg.value = "Succes: Exercise Created!";
        epochTitle.value = null;
        experiences.value = [];
        router.push({ name: "Home" });
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
      epochTitle,
      experiences,
      statusMsg,
      errorMsg,
      addExperience,
      experienceChange,
      deleteEpoch,
      createEpoch,
    };
  },
};
</script>
