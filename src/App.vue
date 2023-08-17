<script setup lang="ts">
import { ref, watch } from 'vue'
import axios from 'axios'
import { BASE_URL } from '@/constants/api'
import { UserInterface } from '@/constants/types'

const users = ref<UserInterface[]>([])
const search_input = ref('')

const loading = ref(false)

const searchUsers = async (e: Event) => {
  e.preventDefault()
  if (!search_input.value) return alert('Please enter a username')

  loading.value = true

  try {
    const res = await axios.get(`${BASE_URL}?q=${search_input.value}`)
    users.value = res.data.items

    loading.value = false
    return res
  } catch (error) {
    console.log(error)
    loading.value = false
  }
}
</script>
<template>
  <main class="bg-background w-full h-screen flex justify-center items-center">
    <div class="w-[800px] min-h-[400px] p-6 bg-primary rounded-xl border border-gray-700">
      <form
        :on-submit="searchUsers"
        class="flex h-12 bg-slate-700 rounded-lg overflow-hidden focus-within:ring"
      >
        <input
          type="text"
          class="h-full w-full bg-slate-700 border-none outline-none px-4 text-white"
          placeholder="Search Github username..."
          v-model="search_input"
        />
        <button
          class="px-8 text-white font-bold bg-[#0079FF] my-2 rounded-md mr-3"
          @click="searchUsers"
        >
          Search
        </button>
      </form>

      <div class="mt-6">
        <h3 v-if="users.length > 0 && !loading" class="text-white text-lg font-semibold mb-4">
          Results
        </h3>
        <pre v-if="users.length === 0 && !loading" class="text-white">No users found.</pre>
        <div v-if="loading" class="text-white">Loading...</div>

        <ul class="max-h-[400px] overflow-y-auto overflow-x-hidden space-y-6">
          <li v-for="user in users" :key="user.id">
            <div class="flex gap-3">
              <div class="w-14 aspect-square">
                <img
                  :src="user.avatar_url"
                  :alt="user.login"
                  class="rounded-full w-full h-full oject-cover"
                />
              </div>
              <div>
                <h4 class="text-white text-lg font-semibold">{{ user.login }}</h4>
                <pre
                  class="text-white text-sm"
                ><a target="_blank" referrerpolicy="no-referrer" :href="user.html_url" class="hover:underline ">{{user.html_url}}</a></pre>
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </main>
</template>
