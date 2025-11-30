<template>
  <div class="min-h-screen flex items-center justify-center p-6 font-sans"
    :style="{ backgroundImage: `url(${bgImage})` }">
    <div class="w-full max-w-md bg-white rounded-xl shadow-xl overflow-hidden border border-gray-200">

      <!-- Header Login -->
      <div class="bg-blue-900 p-8 text-center">
        <div
          class="inline-flex items-center justify-center w-16 h-16 rounded-full bg-white text-blue-900 mb-4 shadow-lg">
          <font-awesome-icon icon="university" class="text-3xl" />
        </div>
        <h1 class="text-2xl font-bold text-white tracking-wide">SITTA UT</h1>
        <p class="text-blue-200 text-xs mt-1">Sistem Informasi Tiras & Transaksi</p>
      </div>

      <!-- Form Login -->
      <div class="p-8">
        <form @submit.prevent="handleSubmit" class="space-y-5">
          <div v-if="errorMsg"
            class="bg-red-50 text-red-600 text-sm p-3 rounded border border-red-200 flex items-center gap-2">
            <font-awesome-icon icon="circle-exclamation" />
            {{ errorMsg }}
          </div>

          <div>
            <label class="block text-xs font-bold text-gray-500 uppercase mb-1 ml-1">Email</label>
            <div class="relative">
              <span class="absolute left-3 top-2.5 text-gray-400">
                <font-awesome-icon icon="envelope" />
              </span>
              <input v-model="email" type="email" placeholder="Email"
                class="w-full pl-10 pr-3 py-2 border border-gray-300 rounded focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none transition"
                required>
            </div>
          </div>

          <div>
            <label class="block text-xs font-bold text-gray-500 uppercase mb-1 ml-1">Password</label>
            <div class="relative">
              <span class="absolute left-3 top-2.5 text-gray-400">
                <font-awesome-icon icon="lock" />
              </span>
              <input v-model="password" type="password" placeholder="Password"
                class="w-full pl-10 pr-3 py-2 border border-gray-300 rounded focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none transition"
                required>
            </div>
          </div>

          <button type="submit"
            class="w-full bg-green-700 hover:bg-green-800 text-white font-bold py-3 rounded transform active:scale-95 transition flex justify-center gap-2 items-center"
            :disabled="isLoading">
            <span v-if="isLoading">
              <font-awesome-icon icon="spinner" spin /> Loading...
            </span>
            <span v-else>
              Masuk <font-awesome-icon icon="arrow-right" class="ml-1" />
            </span>
          </button>
        </form>
      </div>
      <div class="bg-gray-50 p-4 text-center text-xs text-gray-400 border-t">
        &copy; 2025 Created by Ananda Saputra
      </div>
    </div>
  </div>
</template>

<script setup>
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import { ref } from 'vue';
import cloudBg from '../assets/img/cloud.jpg';

const props = defineProps(['isLoading', 'errorMsg']);
const emit = defineEmits(['login']);

const email = ref('');
const password = ref('');
const bgImage = cloudBg;

const handleSubmit = () => {
  emit('login', { email: email.value, password: password.value });
};
</script>