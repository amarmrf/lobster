<template>
  <div class="max-w-screen-sm mx-auto px-4 py-10">
    <!-- App Msg -->
    <div
      v-if="statusMsg || errorMsg"
      class="mb-10 p-4 rounded-md shadow-md bg-light-grey"
    >
      <p class="text-at-light-green">
        {{ statusMsg }}
      </p>
      <p class="text-red-500">
        {{ errorMsg }}
      </p>
    </div>

    <div v-if="dataLoaded">
      <!-- General Epoch Info -->
      <div
        class="flex flex-col items-center p-8 rounded-md shadow-md 
      bg-light-grey relative"
      >
        <div v-if="user" class="flex absolute left-2 top-2 gap-x-2">
          <div
            class="h-7 w-7 rounded-full flex justify-center items-center cursor-pointer
        bg-at-light-green shadow-lg"
            @click="editMode"
          >
            <img class="h-3.5 w-auto" src="@/assets/images/pencil-light.png" alt="" />
          </div>
          <div
            @click="deleteEpoch"
            class="h-7 w-7 rounded-full flex justify-center items-center cursor-pointer
        bg-at-light-green shadow-lg"
          >
            <img class="h-3.5 w-auto" src="@/assets/images/trash-light.png" alt="" />
          </div>
        </div>

        <img
          v-if="data.title === 'cardio'"
          class="h-24 w-auto"
          src="@/assets/images/running-light-green.png"
          alt=""
        />

        <img
          v-else
          class="h-24 w-auto"
          src="@/assets/images/dumbbell-light-green.png"
          alt=""
        />

        <span
          class="mt-6 py-1.5 px-5 text-xs text-white bg-at-light-green
        rounded-lg shadow-md"
        >
          {{ data.title }}
        </span>

        <div class="w-full mt-6">
          <input
            v-if="edit"
            type="text"
            class="p-2 w-full text-gray-500 focus:outline-none"
            v-model="data.epochTitle"
          />
          <h1 v-else class="text-at-light-green text-2xl text-center">
            {{ data.epochTitle }}
          </h1>
        </div>
      </div>

      <!-- Experiences -->
      <div
        class="mt-10 p-8 rounded-md flex flex-col item-center
      bg-light-grey shadow-md"
      >
        <!-- Strength Training -->
        <div v-if="data.title === 'strength'" class="flex flex-col gap-y-4 w-full">
          <div
            class="flex flex-col gap-x-6 gap-y-2 relative sm:flex-row"
            v-for="(item, index) in data.experiences"
            :key="index"
          >
            <div class="flex flex-2 flex-col md:w-1/3">
              <label for="experience-name" class="mb-1 text-sm text-at-light-green">
                Experience
              </label>
              <input
                id="experience-name"
                v-if="edit"
                class="p-2 w-full text-gray-500 focus:outline-none"
                type="text"
                v-model="item.experience"
              />
              <p v-else>{{ item.experience }}</p>
            </div>
            <div class="flex flex-1 flex-col">
              <label for="sets" class="mb-1 text-sm text-at-light-green">
                Sets
              </label>
              <input
                v-if="edit"
                id="sets"
                class="p-2 w-full text-gray-500 focus:outline-none"
                type="text"
                v-model="item.sets"
              />
              <p v-else>{{ item.sets }}</p>
            </div>
            <div class="flex flex-1 flex-col">
              <label for="reps" class="mb-1 text-sm text-at-light-green">
                Reps
              </label>
              <input
                v-if="edit"
                id="reps"
                class="p-2 w-full text-gray-500 focus:outline-none"
                type="text"
                v-model="item.reps"
              />
              <p v-else>{{ item.reps }}</p>
            </div>
            <div class="flex flex-1 flex-col">
              <label for="weight" class="mb-1 text-sm text-at-light-green">
                Weight (LB's)
              </label>
              <input
                v-if="edit"
                id="weight"
                class="p-2 w-full text-gray-500 focus:outline-none"
                type="text"
                v-model="item.weight"
              />
              <p v-else>{{ item.weight }}</p>
            </div>
            <img
              v-if="edit"
              @click="deleteExperience(item.id)"
              class="absolute h-4 w-auto -left-5 cursor-pointer"
              src="@/assets/images/trash-light-green.png"
              alt=""
            />
          </div>
          <button
            v-if="edit"
            @click="addExperience"
            type="button"
            class="py-2 px-6 rounded-sm self-start text-sm text-white
            bg-at-light-green duration-200 border-solid border-2 border-transparent
            hover:border-at-light-green hover:bg-white hover:text-at-light-green"
          >
            Add Experience
          </button>
        </div>

        <!-- Cardio -->
        <div v-else class="flex flex-col gap-y-4 w-full">
          <div
            class="flex flex-col gap-x-6 gap-y-2 relative sm:flex-row"
            v-for="(item, index) in data.experiences"
            :key="index"
          >
            <div class="flex flex-2 flex-col md:w-1/3">
              <label for="cardioType" class="mb-1 text-sm text-at-light-green">
                Type
              </label>
              <select
                id="cardioType"
                v-if="edit"
                class="p-2 w-full text-gray-500 focus:outline-none"
                type="text"
                v-model="item.cardioType"
              >
                <option value="#">Select Type</option>
                <option value="run">Runs</option>
                <option value="walk">Walk</option>
              </select>
              <p v-else>{{ item.cardioType }}</p>
            </div>
            <div class="flex flex-1 flex-col">
              <label for="distance" class="mb-1 text-sm text-at-light-green">
                Distance
              </label>
              <input
                v-if="edit"
                id="distance"
                class="p-2 w-full text-gray-500 focus:outline-none"
                type="text"
                v-model="item.distance"
              />
              <p v-else>{{ item.distance }}</p>
            </div>
            <div class="flex flex-1 flex-col">
              <label for="duration" class="mb-1 text-sm text-at-light-green">
                Duration
              </label>
              <input
                v-if="edit"
                id="duration"
                class="p-2 w-full text-gray-500 focus:outline-none"
                type="text"
                v-model="item.duration"
              />
              <p v-else>{{ item.duration }}</p>
            </div>
            <div class="flex flex-1 flex-col">
              <label for="pace" class="mb-1 text-sm text-at-light-green">
                Pace
              </label>
              <input
                v-if="edit"
                id="pace"
                class="p-2 w-full text-gray-500 focus:outline-none"
                type="text"
                v-model="item.pace"
              />
              <p v-else>{{ item.pace }}</p>
            </div>
            <img
              @click="deleteExperience(item.id)"
              v-if="edit"
              class="absolute h-4 w-auto -left-5 cursor-pointer"
              src="@/assets/images/trash-light-green.png"
              alt=""
            />
          </div>
          <button
            @click="addExperience"
            v-if="edit"
            type="button"
            class="py-2 px-6 rounded-sm self-start text-sm text-white
            bg-at-light-green duration-200 border-solid border-2 border-transparent
            hover:border-at-light-green hover:bg-white hover:text-at-light-green"
          >
            Add Experience
          </button>
        </div>
      </div>

      <!-- Update -->
      <button
        v-if="edit"
        @click="update"
        type="button"
        class="mt-10 py-2 px-6 rounded-sm self-start text-sm text-white
            bg-at-light-green duration-200 border-solid border-2 border-transparent
            hover:border-at-light-green hover:bg-white hover:text-at-light-green"
      >
        Update Epoch
      </button>
    </div>
  </div>
</template>

<script>
import { ref, computed } from "vue";
import { supabase } from "../supabase/init";
import { useRoute, useRouter } from "vue-router";
import store from "../store/index";
import { uid } from "uid";

export default {
  name: "view-epoch",
  setup() {
    // Create data / vars
    const data = ref(null);
    const dataLoaded = ref(null);
    const errorMsg = ref(null);
    const statusMsg = ref(null);
    const route = useRoute();
    const router = useRouter();
    const user = computed(() => store.state.user);

    // Get current Id of route
    const currentId = route.params.workoutId;

    // Get epoch data
    const getData = async () => {
      try {
        const { data: epochs, error } = await supabase
          .from("past_epochs")
          .select("*")
          .eq("id", currentId);
        if (error) throw error;
        data.value = epochs[0];
        dataLoaded.value = true;
        console.log(data.value);
      } catch (error) {
        errorMsg.value = error.message;
        setTimeout(() => {
          errorMsg.value = false;
        }, 5000);
      }
    };

    getData();

    // Delete epoch
    const deleteEpoch = async () => {
      try {
        const { error } = await supabase
          .from("epochs")
          .delete()
          .eq("id", currentId);
        if (error) throw error;
        router.push({ name: "Home" });
      } catch (error) {
        errorMsg.value = `Error: ${error.message}`;
        setTimeout(() => {
          errorMsg.value = false;
        }, 5000);
      }
    };

    // Edit mode
    const edit = ref(null);

    const editMode = () => {
      edit.value = !edit.value;
    };

    // Add experience
    const addExperience = () => {
      if (data.value.experiences.value.length < 6) {
        data.value.experiences.value.push({
          id: uid(),
          title: "",
          description: "",
        });
      }
    };

    // Delete experience
    const deleteExperience = (id) => {
      if (data.value.experiences.length > 1) {
        data.value.experiences = data.value.experiences.filter(
          (experience) => experience.id !== id
        );
        return;
      }
      errorMsg.value = "Error: Cannot remove, need to at least have one experience";
      setTimeout(() => {
        errorMsg.value = false;
      }, 5000);
    };

    // Update Epoch
    const update = async () => {
      try {
        const { error } = await supabase
          .from("epochs")
          .update({
            epochTitle: data.value.epochTitle,
            experiences: data.value.experiences,
          })
          .eq("id", currentId);
        if (error) throw error;
        edit.value = false;
        statusMsg.value = "Success: Epoch Updated!";
        setTimeout(() => {
          statusMsg.value = false;
        }, 5000);
      } catch (error) {
        errorMsg.value`Error: ${error.message}`;
        setTimeout(() => {
          errorMsg.value = false;
        }, 5000);
      }
    };

    return {
      statusMsg,
      data,
      dataLoaded,
      errorMsg,
      edit,
      editMode,
      user,
      deleteEpoch,
      addExperience,
      deleteExperience,
      update,
    };
  },
};
</script>
