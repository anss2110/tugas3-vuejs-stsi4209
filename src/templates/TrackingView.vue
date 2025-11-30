<template>
  <div class="max-w-6xl mx-auto p-6">
    <!-- Header Tracking -->
    <header class="mb-8 flex flex-row justify-start items-center border-b pb-4 border-gray-200 gap-4">
      <button @click="$emit('back')"
        class="p-3 bg-white border hover:bg-gray-100 rounded-full text-sm font-medium transition shadow-sm w-10 h-10 flex items-center justify-center">
        <font-awesome-icon icon="arrow-left" />
      </button>
      <div class="flex-1">
        <h1 class="text-2xl font-bold text-blue-900">Tracking Delivery Order</h1>
        <p class="text-sm text-gray-500">Input & Lacak Pengiriman Bahan Ajar</p>
      </div>
      <button @click="$emit('logout')"
        class="bg-red-600 hover:bg-red-700 text-white px-5 py-3 rounded-lg font-bold transition flex items-center gap-2 shadow-lg"
        title="Logout">
        <font-awesome-icon icon="right-from-bracket" />
        <span class="hidden md:inline">Logout</span>
      </button>
    </header>

    <!-- Content Tracking -->
    <DoTracking :initial-tracking="initialTracking" :paket-list="paketList" :ekspedisi-list="ekspedisiList" />
  </div>
</template>

<script setup>
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import DoTracking from '../components/DoTracking.vue';

const props = defineProps(['initialTracking', 'paketList', 'ekspedisiList']);
const emit = defineEmits(['back', 'logout']);
</script>