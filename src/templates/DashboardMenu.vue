<template>
  <div class="h-screen flex items-center justify-center p-4">
    <div class="bg-white p-8 rounded-lg shadow-lg text-center max-w-md w-full">
      <h1 class="text-4xl font-bold text-blue-900 mb-2">SITTA UT</h1>
      <p class="text-gray-500 mb-8">Sistem Informasi Tiras & Transaksi Bahan Ajar</p>

      <div class="space-y-4">
        <button @click="$emit('navigate', 'stok')"
          class="w-full py-4 bg-blue-600 hover:bg-blue-700 text-white rounded font-bold transition flex items-center justify-center gap-3">
          <font-awesome-icon icon="folder-open" class="text-xl" />
          Kelola Stok Bahan Ajar
        </button>

        <button @click="$emit('navigate', 'tracking')"
          class="w-full py-4 bg-green-600 hover:bg-green-700 text-white rounded font-bold transition flex items-center justify-center gap-3">
          <font-awesome-icon icon="truck-fast" class="text-xl" />
          Tracking Delivery Order
        </button>

        <button @click="$emit('logout')"
          class="w-full py-4 bg-red-600 hover:bg-red-700 text-white rounded font-bold transition flex items-center justify-center gap-3">
          <font-awesome-icon icon="right-from-bracket" class="text-xl" />
          Logout
        </button>
      </div>

      <div class="mt-8 text-xs text-gray-400">
        Tugas 3 - created by Ananda Saputra
      </div>
    </div>
  </div>
</template>

<script setup>
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
defineEmits(['navigate', 'logout']);
</script>