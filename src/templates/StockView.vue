<template>
  <div class="max-w-6xl mx-auto p-6">
    <!-- Header Stok -->
    <header class="mb-8 flex flex-row gap-4 justify-between items-center h-fit">
      <div class="flex gap-4 items-center flex-1">
        <button @click="$emit('back')"
          class="p-3 bg-white border hover:bg-gray-100 rounded-full text-sm font-medium transition shadow-sm w-10 h-10 flex items-center justify-center">
          <font-awesome-icon icon="arrow-left" />
        </button>
        <div class="flex-1">
          <h1 class="text-3xl font-bold text-blue-900">SITTA UT</h1>
          <p class="text-gray-500 text-sm hidden md:block">
            Sistem Informasi Tiras & Transaksi Bahan Ajar
          </p>
        </div>
      </div>

      <!-- Tombol Tambah Data -->
      <button @click="triggerAddStock"
        class="bg-blue-600 hover:bg-blue-700 text-white px-5 py-3 rounded-lg font-bold transition flex items-center gap-2 shadow-lg">
        <font-awesome-icon icon="plus" />
        <span class="hidden md:inline">Tambah Data</span>
      </button>

      <!-- Tombol Logout Kecil -->
      <button @click="$emit('logout')"
        class="bg-red-600 hover:bg-red-700 text-white px-5 py-3 rounded-lg font-bold transition flex items-center gap-2 shadow-lg"
        title="Logout">
        <font-awesome-icon icon="right-from-bracket" />
        <span class="hidden md:inline">Logout</span>
      </button>
    </header>

    <!-- Content Stok -->
    <StockTable ref="stockTableRef" :initial-data="initialData" :upbjj-list="upbjjList" :kategori-list="kategoriList" />
  </div>
</template>

<script setup>
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import { ref } from 'vue';
import StockTable from '../components/StockTable.vue';

const props = defineProps(['initialData', 'upbjjList', 'kategoriList']);
const emit = defineEmits(['back', 'logout']);

const stockTableRef = ref(null);

const triggerAddStock = () => {
  if (stockTableRef.value && stockTableRef.value.openInputModal) {
    stockTableRef.value.openInputModal();
  }
};
</script>