<template>
  <div class="max-w-6xl mx-auto">

    <!-- SECTION FILTER -->
    <div class="bg-white p-6 rounded-lg shadow-sm border border-gray-200 mb-6">
      <h2 class="text-lg font-semibold mb-4 text-gray-700 flex items-center gap-2">
        <font-awesome-icon icon="filter" /> Filter & Sort Data
      </h2>
      <div class="grid grid-cols-1 md:grid-cols-4 gap-4">
        <!-- Filter UT-Daerah -->
        <div>
          <label class="block text-xs font-bold text-gray-500 uppercase mb-1">UT-Daerah</label>
          <select v-model="filterUpbjj"
            class="w-full p-2 border border-gray-300 rounded focus:ring-2 focus:ring-blue-500 outline-none">
            <option value="">Semua Daerah</option>
            <option v-for="kota in upbjjList" :key="kota" :value="kota">{{ kota }}</option>
          </select>
        </div>

        <!-- Filter Kategori (Dependent) -->
        <div>
          <label class="block text-xs font-bold text-gray-500 uppercase mb-1">Kategori MK</label>
          <select v-model="filterKategori" :disabled="!filterUpbjj"
            class="w-full p-2 border border-gray-300 rounded focus:ring-2 focus:ring-blue-500 disabled:bg-gray-100 disabled:text-gray-400">
            <option value="">{{ filterUpbjj ? 'Semua Kategori' : 'Pilih Daerah Dulu' }}</option>
            <option v-for="kat in uniqueCategories" :key="kat" :value="kat">{{ kat }}</option>
          </select>
        </div>

        <!-- Sort -->
        <div>
          <label class="block text-xs font-bold text-gray-500 uppercase mb-1">Urutkan</label>
          <select v-model="sortBy" class="w-full p-2 border border-gray-300 rounded">
            <option value="judul">Judul (A-Z)</option>
            <option value="qty">Stok (Terkecil)</option>
            <option value="harga">Harga (Termurah)</option>
          </select>
        </div>

        <!-- Reset & Checkbox -->
        <div class="flex flex-col justify-end space-y-2">
          <label class="flex items-center space-x-2 cursor-pointer select-none">
            <input type="checkbox" v-model="filterWarning" class="form-checkbox h-4 w-4 text-blue-600 rounded" />
            <span class="text-sm text-gray-700 font-medium">Stok Menipis / Kosong</span>
          </label>
          <button @click="resetFilters" class="text-sm text-red-600 hover:text-red-800 font-bold text-left underline">
            Reset Semua Filter
          </button>
        </div>
      </div>
    </div>

    <!-- SECTION TABEL -->
    <div class="bg-white rounded-lg shadow overflow-hidden border border-gray-200 mb-10">
      <table class="w-full text-left border-collapse">
        <thead class="bg-gray-100 text-gray-600 uppercase text-xs font-bold">
          <tr>
            <th class="p-4 border-b">Kode / Nama MK</th>
            <th class="p-4 border-b">Kategori</th>
            <th class="p-4 border-b">Lokasi</th>
            <th class="p-4 border-b text-right">Harga</th>
            <th class="p-4 border-b text-center">Stok</th>
            <th class="p-4 border-b text-center">Safety</th>
            <th class="p-4 border-b text-center">Status</th>
            <th class="p-4 border-b text-center">Aksi</th>
          </tr>
        </thead>
        <tbody class="text-sm divide-y divide-gray-100">
          <tr v-for="item in processedStok" :key="item.kode" class="hover:bg-blue-50 transition group">

            <!-- Kolom Kode & Judul -->
            <td class="p-4">
              <div class="font-bold text-gray-800">{{ item.judul }}</div>
              <div class="text-xs text-gray-500 font-mono">{{ item.kode }} | {{ item.upbjj }}</div>
            </td>

            <!-- Kolom Kategori -->
            <td class="p-4 text-gray-600">
              <span class="bg-gray-200 px-2 py-1 rounded text-xs">{{ item.kategori }}</span>
            </td>

            <!-- Kolom Lokasi -->
            <td class="p-4 text-gray-600 font-mono">{{ item.lokasiRak }}</td>

            <!-- Kolom Harga (Formatting Rp) -->
            <td class="p-4 text-right font-medium text-gray-700">
              {{ formatRupiah(item.harga) }}
            </td>

            <!-- Kolom Stok (Formatting buah) -->
            <td class="p-4 text-center font-bold" :class="item.qty === 0 ? 'text-red-600' : 'text-gray-800'">
              {{ item.qty }} <span class="text-xs font-normal text-gray-400">buah</span>
            </td>

            <!-- Kolom Safety (Formatting buah) -->
            <td class="p-4 text-center text-gray-500">
              {{ item.safety }} <span class="text-xs font-normal text-gray-400">buah</span>
            </td>

            <!-- Kolom Status + Tooltip -->
            <td class="p-4 text-center relative">
              <div class="relative inline-block group/tooltip cursor-help">
                <span :class="getStatusClass(item)"
                  class="px-3 py-1 rounded-full text-xs font-bold border flex items-center gap-1 justify-center w-24">
                  <font-awesome-icon :icon="getStatusIcon(item)" />
                  {{ getStatusLabel(item) }}
                </span>

                <!-- Tooltip Hover (Tampil jika ada catatanHTML) -->
                <div v-if="item.catatanHTML"
                  class="absolute z-50 bottom-full left-1/2 -translate-x-1/2 mb-2 w-48 p-3 bg-gray-800 text-white text-xs rounded shadow-xl opacity-0 invisible group-hover/tooltip:opacity-100 group-hover/tooltip:visible transition-all duration-200 text-left pointer-events-none">
                  <div class="font-bold border-b border-gray-600 pb-1 mb-1 text-yellow-400">Catatan:</div>
                  <!-- Render HTML dari JSON -->
                  <div v-html="item.catatanHTML"></div>
                  <!-- Arrow Tooltip -->
                  <div
                    class="absolute top-full left-1/2 -translate-x-1/2 border-8 border-transparent border-t-gray-800">
                  </div>
                </div>
              </div>
            </td>

            <!-- Kolom Aksi -->
            <td class="p-4 text-center">
              <div class="flex items-center justify-center gap-2">
                <button @click="openModal('edit', item)"
                  class="text-blue-600 hover:text-blue-800 hover:bg-blue-100 p-2 rounded transition" title="Edit">
                  <font-awesome-icon icon="pen" />
                </button>
                <button @click="confirmDelete(item)"
                  class="text-red-600 hover:text-red-800 hover:bg-red-100 p-2 rounded transition" title="Hapus">
                  <font-awesome-icon icon="trash" />
                </button>
              </div>
            </td>
          </tr>

          <!-- Empty State -->
          <tr v-if="processedStok.length === 0">
            <td colspan="8" class="p-10 text-center text-gray-500 flex-col justify-center items-center">
              <font-awesome-icon icon="box-open" class="text-4xl mb-2 text-gray-300" />
              <p>Tidak ada data yang cocok dengan filter.</p>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- ============================================ -->
    <!-- MODAL 1: FORM INPUT (Create / Update)        -->
    <!-- ============================================ -->
    <div v-if="showModal"
      class="fixed inset-0 bg-black bg-opacity-60 flex items-center justify-center z-50 p-4 backdrop-blur-sm">
      <div class="bg-white rounded-xl shadow-2xl w-full max-w-2xl overflow-hidden animate-fade-in">
        <div class="bg-blue-600 text-white p-5 flex justify-between items-center">
          <h3 class="font-bold text-lg flex items-center gap-2">
            <font-awesome-icon :icon="formMode === 'create' ? 'plus-circle' : 'pen-to-square'" />
            {{ formMode === 'create' ? 'Tambah Stok Baru' : 'Edit Stok Bahan Ajar' }}
          </h3>
          <button @click="closeModal"
            class="text-white hover:bg-blue-700 w-8 h-8 rounded-full flex items-center justify-center transition">
            <font-awesome-icon icon="times" />
          </button>
        </div>

        <div class="p-6">
          <form @submit.prevent="saveData">
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
              <!-- Kode -->
              <div>
                <label class="label-text">Kode MK</label>
                <input v-model="formData.kode" :disabled="formMode === 'edit'" placeholder="cth: MK001"
                  class="input-text disabled:bg-gray-100" required />
              </div>
              <!-- Judul -->
              <div>
                <label class="label-text">Nama Mata Kuliah</label>
                <input v-model="formData.judul" placeholder="cth: Pemrograman Web" class="input-text" required />
              </div>
              <!-- Kategori -->
              <div>
                <label class="label-text">Kategori</label>
                <select v-model="formData.kategori" class="input-text">
                  <option v-for="k in kategoriList" :key="k" :value="k">{{ k }}</option>
                </select>
              </div>
              <!-- UPBJJ -->
              <div>
                <label class="label-text">UT-Daerah</label>
                <select v-model="formData.upbjj" class="input-text">
                  <option v-for="u in upbjjList" :key="u" :value="u">{{ u }}</option>
                </select>
              </div>
              <!-- Lokasi -->
              <div>
                <label class="label-text">Lokasi Rak</label>
                <input v-model="formData.lokasiRak" placeholder="cth: R1-A1" class="input-text" />
              </div>
              <!-- Harga -->
              <div>
                <label class="label-text">Harga (Rp)</label>
                <input type="number" v-model.number="formData.harga" class="input-text" />
              </div>
              <!-- Qty -->
              <div>
                <label class="label-text">Jumlah Stok (Buah)</label>
                <input type="number" v-model.number="formData.qty" class="input-text" required />
              </div>
              <!-- Safety -->
              <div>
                <label class="label-text">Safety Stock (Buah)</label>
                <input type="number" v-model.number="formData.safety" class="input-text" />
              </div>
              <!-- Catatan -->
              <div class="md:col-span-2">
                <label class="label-text">Catatan (HTML Supported)</label>
                <input v-model="formData.catatanHTML" placeholder="Contoh: <b>Edisi Baru</b>" class="input-text" />
                <p class="text-xs text-gray-400 mt-1">*Tekan ENTER untuk simpan langsung</p>
              </div>
            </div>

            <!-- Footer Modal -->
            <div class="mt-8 flex justify-end gap-3 border-t pt-4">
              <button type="button" @click="closeModal"
                class="px-4 py-2 bg-gray-200 hover:bg-gray-300 text-gray-700 rounded font-bold transition">Batal</button>
              <button type="submit"
                class="px-6 py-2 bg-blue-600 hover:bg-blue-700 text-white rounded font-bold shadow transition flex items-center gap-2">
                <font-awesome-icon icon="save" /> Simpan
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>

    <!-- ============================================ -->
    <!-- MODAL 2: KONFIRMASI DELETE                   -->
    <!-- ============================================ -->
    <div v-if="showDeleteModal"
      class="fixed inset-0 bg-black bg-opacity-60 flex items-center justify-center z-50 p-4 backdrop-blur-sm">
      <div class="bg-white rounded-xl shadow-2xl w-full max-w-sm overflow-hidden text-center p-6">
        <div class="w-16 h-16 bg-red-100 text-red-600 rounded-full flex items-center justify-center mx-auto mb-4">
          <font-awesome-icon icon="triangle-exclamation" class="text-3xl" />
        </div>
        <h3 class="text-xl font-bold text-gray-800 mb-2">Hapus Data?</h3>
        <p class="text-gray-500 mb-6">Apakah Anda yakin ingin menghapus bahan ajar <br> <span
            class="font-bold text-red-600">{{ itemToDelete?.judul }}</span>?</p>
        <div class="flex justify-center gap-3">
          <button @click="showDeleteModal = false"
            class="px-4 py-2 bg-gray-200 hover:bg-gray-300 text-gray-700 rounded font-bold transition">Batal</button>
          <button @click="deleteItem"
            class="px-4 py-2 bg-red-600 hover:bg-red-700 text-white rounded font-bold transition">Ya, Hapus</button>
        </div>
      </div>
    </div>

  </div>
</template>

<script setup>
import { library } from '@fortawesome/fontawesome-svg-core';
import {
  faArrowLeft,
  faBan,
  faBoxOpen,
  faCheckCircle, faExclamationCircle,
  faFilter, faPen,
  faPenToSquare,
  faPlus,
  faPlusCircle,
  faRightFromBracket,
  faSave,
  faTimes,
  faTrash,
  faTriangleExclamation
} from '@fortawesome/free-solid-svg-icons';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import { computed, reactive, ref, watch } from 'vue';

library.add(
  faArrowLeft, faPlus, faRightFromBracket, faFilter, faPen, faTrash, faTimes,
  faSave, faPlusCircle, faPenToSquare, faBoxOpen, faTriangleExclamation,
  faCheckCircle, faExclamationCircle, faBan
);

// PROPS & EMITS
const props = defineProps(['initialData', 'upbjjList', 'kategoriList']);
const emit = defineEmits(['back', 'logout']);

// STATE UTAMA
const localStok = ref([]);
const filterUpbjj = ref("");
const filterKategori = ref("");
const filterWarning = ref(false);
const sortBy = ref("judul");

// STATE MODAL
const showModal = ref(false);
const formMode = ref('create'); // 'create' | 'edit'
const showDeleteModal = ref(false);
const itemToDelete = ref(null);

// STATE FORM
const formData = reactive({
  kode: "", judul: "", kategori: "", upbjj: "", lokasiRak: "", qty: 0, safety: 0, harga: 0, catatanHTML: ""
});

// SINKRONISASI DATA AWAL
watch(() => props.initialData, (newVal) => {
  if (newVal) localStok.value = JSON.parse(JSON.stringify(newVal));
}, { immediate: true });

// WATCHER FILTER DEPENDENT
watch(filterUpbjj, () => {
  filterKategori.value = "";
});

const uniqueCategories = computed(() => {
  const source = filterUpbjj.value
    ? localStok.value.filter(item => item.upbjj === filterUpbjj.value)
    : localStok.value;
  return [...new Set(source.map(item => item.kategori))];
});

const processedStok = computed(() => {
  let result = [...localStok.value];

  if (filterUpbjj.value) {
    result = result.filter(item => item.upbjj === filterUpbjj.value);
  }
  if (filterKategori.value) {
    result = result.filter(item => item.kategori === filterKategori.value);
  }
  if (filterWarning.value) {
    result = result.filter(item => item.qty < item.safety || item.qty === 0);
  }

  result.sort((a, b) => {
    if (sortBy.value === 'judul') return a.judul.localeCompare(b.judul);
    if (sortBy.value === 'qty') return a.qty - b.qty;
    if (sortBy.value === 'harga') return a.harga - b.harga;
    return 0;
  });

  return result;
});

// METHODS HELPER FORMATTING
const formatRupiah = (val) => new Intl.NumberFormat("id-ID", { style: "currency", currency: "IDR" }).format(val || 0);

// METHODS STATUS LOGIC
const getStatusClass = (item) => {
  if (item.qty === 0) return "bg-red-50 text-red-600 border-red-200"; // Kosong
  if (item.qty < item.safety) return "bg-orange-50 text-orange-600 border-orange-200"; // Menipis
  return "bg-green-50 text-green-600 border-green-200"; // Aman
};

const getStatusLabel = (item) => {
  if (item.qty === 0) return "Kosong";
  if (item.qty < item.safety) return "Menipis";
  return "Aman";
};

const getStatusIcon = (item) => {
  if (item.qty === 0) return "ban";
  if (item.qty < item.safety) return "exclamation-circle";
  return "check-circle";
};

const resetFilters = () => {
  filterUpbjj.value = "";
  filterKategori.value = "";
  filterWarning.value = false;
  sortBy.value = "judul";
};

const openModal = (mode, item = null) => {
  formMode.value = mode;
  if (mode === 'edit' && item) {
    Object.assign(formData, item);
  } else {
    Object.assign(formData, {
      kode: "", judul: "", kategori: "MK Wajib", upbjj: "Jakarta", lokasiRak: "", qty: 0, safety: 5, harga: 0, catatanHTML: ""
    });
  }
  showModal.value = true;
};

const closeModal = () => {
  showModal.value = false;
};

const saveData = () => {
  if (!formData.kode || !formData.judul) return alert("Kode dan Judul wajib diisi!");

  if (formMode.value === 'create') {
    localStok.value.unshift({ ...formData });
  } else {
    const index = localStok.value.findIndex(i => i.kode === formData.kode);
    if (index !== -1) {
      localStok.value[index] = { ...formData };
    }
  }
  closeModal();
};

const confirmDelete = (item) => {
  itemToDelete.value = item;
  showDeleteModal.value = true;
};

const deleteItem = () => {
  if (itemToDelete.value) {
    localStok.value = localStok.value.filter(i => i.kode !== itemToDelete.value.kode);
    showDeleteModal.value = false;
    itemToDelete.value = null;
  }
};
</script>

<style scoped>
.label-text {
  @apply block text-xs font-bold text-gray-500 uppercase mb-1;
}

.input-text {
  @apply w-full p-2 border border-gray-300 rounded focus:ring-2 focus:ring-blue-500 outline-none transition;
}
</style>