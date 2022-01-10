<template>
  <header class="bg-at-light-green text-white">
    <nav class="container py-5 px-4 flex flex-col gap-4 items-center sm:flex-row">
      <div class="flex item-center gap-x-4">
        <img src="../assets/images/dumbbell-light.png" class="w-14">
        <h1 class="text-lg mt-1">
          Mythologize Your Life And Become The Main Character
        </h1>
      </div>
      <ul class="flex flex-1 justify-end gap-x-10">
        <router-link v-if="user" class="cursor-pointer" :to="{name: 'Home'}"> Experiences </router-link> 
        <router-link v-if="user" class="cursor-pointer" :to="{name: 'Create'}"> Epochs </router-link> 
        <router-link v-if="!user" class="cursor-pointer" :to="{name: 'Login'}"> Login</router-link>
        <li v-if="user" @click="logout" class="cursor-pointer"> Logout</li>
      </ul>
    </nav> 
  </header>
</template>

<script>
import { supabase } from "../supabase/init"
import { useRouter } from "vue-router"
import store from "../store/index"
import { computed } from "vue"

export default {
  name: 'Navigation-component',
  setup() {
    // Get user from store
    const user = computed(() => store.state.user)
    // Setup ref to router
    const router = useRouter()
    // Logout function
    const logout = async () => {
      await supabase.auth.signOut()
      router.push({ name: 'Login' })
    }

    return {logout, user};
  },
};
</script>
