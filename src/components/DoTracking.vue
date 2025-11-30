<template>
  <div class="max-w-6xl mx-auto">

    <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">

      <div class="lg:col-span-1 space-y-6">

        <div class="bg-blue-600 p-6 rounded-lg shadow-lg text-white">
          <h2 class="text-lg font-bold mb-2 flex items-center gap-2">
            <font-awesome-icon icon="magnifying-glass" /> Cek Resi / DO
          </h2>
          <p class="text-blue-100 text-xs mb-4">Tekan <kbd class="bg-blue-800 px-1 rounded">Enter</kbd> cari, <kbd
              class="bg-blue-800 px-1 rounded">Esc</kbd> reset.</p>
          <div class="relative">
            <input v-model="keyword" @keyup.enter="handleSearch" @keyup.esc="resetSearch" placeholder="No. DO / NIM"
              class="w-full p-2 pr-10 rounded text-gray-800 focus:ring-2 focus:ring-blue-300 outline-none uppercase font-mono" />
            <button @click="handleSearch" class="absolute right-2 top-2 text-blue-600 hover:text-blue-800">
              <font-awesome-icon icon="arrow-right" />
            </button>
          </div>
        </div>

        <div class="bg-white p-6 rounded-lg shadow-sm border border-gray-200">
          <h2 class="text-lg font-bold text-gray-800 mb-4 flex items-center gap-2">
            <span class="bg-blue-100 text-blue-600 w-8 h-8 flex items-center justify-center rounded-full text-sm">
              <font-awesome-icon icon="box" />
            </span>
            Input DO Baru
          </h2>

          <form @submit.prevent="simpanDO" class="space-y-4">
            <!-- Nomor DO Auto -->
            <div>
              <label class="label-text">Nomor DO (Otomatis)</label>
              <input :value="generatedDONumber" readonly
                class="input-text bg-gray-100 font-mono text-gray-600 cursor-not-allowed" />
            </div>

            <!-- NIM & Nama -->
            <div class="grid grid-cols-2 gap-2">
              <div>
                <label class="label-text">NIM</label>
                <input v-model="inputDO.nim" placeholder="043xxxx" required class="input-text" />
              </div>
              <div>
                <label class="label-text">Nama</label>
                <input v-model="inputDO.nama" required class="input-text" />
              </div>
            </div>

            <!-- Ekspedisi -->
            <div>
              <label class="label-text">Ekspedisi</label>
              <select v-model="inputDO.ekspedisi" class="input-text">
                <option v-for="eks in ekspedisiList" :key="eks.kode" :value="eks.nama">{{ eks.nama }}</option>
              </select>
            </div>

            <!-- Paket -->
            <div>
              <label class="label-text">Pilih Paket BA</label>
              <select v-model="inputDO.paketId" required class="input-text">
                <option value="">-- Pilih Paket --</option>
                <option v-for="pkt in paketList" :key="pkt.kode" :value="pkt.kode">
                  {{ pkt.kode }} - {{ pkt.nama }}
                </option>
              </select>

              <!-- Detail Paket (Dependent Info) -->
              <div v-if="selectedPaketDetail"
                class="mt-2 p-3 bg-blue-50 border border-blue-100 rounded text-sm text-blue-800 animate-fade-in">
                <p class="font-bold text-xs uppercase text-blue-400 mb-1">Isi Paket:</p>
                <ul class="list-disc list-inside">
                  <li v-for="isi in selectedPaketDetail.isi" :key="isi">{{ isi }}</li>
                </ul>
              </div>
            </div>

            <!-- Harga & Tgl -->
            <div class="grid grid-cols-2 gap-2">
              <div>
                <label class="label-text">Total Harga</label>
                <input :value="formatRupiah(inputDO.hargaDisplay)" readonly
                  class="input-text bg-gray-100 text-right font-bold text-gray-700" />
              </div>
              <div>
                <label class="label-text">Tgl Kirim</label>
                <input v-model="inputDO.tanggal" type="date" required class="input-text" />
              </div>
            </div>

            <button type="submit"
              class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-3 rounded transition shadow-lg mt-4 flex justify-center items-center gap-2">
              <font-awesome-icon icon="save" /> Simpan DO
            </button>
          </form>
        </div>
      </div>

      <!-- KOLOM KANAN: TABEL RIWAYAT INPUT -->
      <div class="lg:col-span-2">
        <div class="bg-white rounded-lg shadow-sm border border-gray-200 overflow-hidden h-full flex flex-col">
          <div class="p-4 border-b bg-gray-50 flex justify-between items-center">
            <h3 class="font-bold text-gray-700">Riwayat Input (Sesi Ini)</h3>
            <span class="text-xs bg-gray-200 px-2 py-1 rounded text-gray-600">{{ localTracking.length }} Data</span>
          </div>

          <div v-if="localTracking.length === 0"
            class="p-8 text-center text-gray-400 flex-1 flex flex-col justify-center items-center">
            <font-awesome-icon icon="clipboard-list" class="text-4xl mb-2 text-gray-300" />
            <p>Belum ada data tracking yang diinput.</p>
          </div>

          <div v-else class="overflow-y-auto flex-1 max-h-[800px]">
            <table class="w-full text-left border-collapse">
              <thead class="bg-gray-100 text-gray-600 uppercase text-xs font-bold sticky top-0">
                <tr>
                  <th class="p-3 border-b">No. DO</th>
                  <th class="p-3 border-b">Mahasiswa</th>
                  <th class="p-3 border-b">Ekspedisi / Paket</th>
                  <th class="p-3 border-b text-right">Biaya</th>
                </tr>
              </thead>
              <tbody class="text-sm divide-y divide-gray-100">
                <tr v-for="item in localTracking" :key="item.noDO" class="hover:bg-gray-50 cursor-pointer"
                  @click="viewDetail(item)">
                  <td class="p-3 align-top">
                    <span class="font-mono font-bold text-blue-600">{{ item.noDO }}</span><br />
                    <span class="text-xs text-gray-500">{{ formatDateIndo(item.tanggalKirim) }}</span>
                  </td>
                  <td class="p-3 align-top">
                    <div class="font-medium text-gray-900">{{ item.nama }}</div>
                    <div class="text-xs text-gray-500">{{ item.nim }}</div>
                  </td>
                  <td class="p-3 align-top">
                    <div class="text-gray-900">{{ item.ekspedisi }}</div>
                    <div class="text-xs text-blue-500">{{ item.paket }}</div>
                  </td>
                  <td class="p-3 align-top text-right font-medium text-gray-700">
                    {{ formatRupiah(item.total) }}
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>

    <!-- ============================================= -->
    <!-- MODAL TRACKING TIMELINE & UPDATE STATUS       -->
    <!-- ============================================= -->
    <div v-if="showModal"
      class="fixed inset-0 bg-black bg-opacity-60 flex items-center justify-center z-50 p-4 backdrop-blur-sm">
      <div class="bg-white rounded-xl shadow-2xl w-full max-w-lg overflow-hidden flex flex-col max-h-[90vh]">
        <!-- Header -->
        <div class="bg-blue-600 text-white p-5 flex justify-between items-center shrink-0">
          <div>
            <h3 class="font-bold text-lg">Lacak Paket</h3>
            <p class="text-xs text-blue-200 font-mono">{{ searchResult?.noDO }}</p>
          </div>
          <button @click="closeModal"
            class="text-white hover:bg-blue-700 w-8 h-8 rounded-full flex items-center justify-center transition">
            <font-awesome-icon icon="times" />
          </button>
        </div>

        <!-- Body (Scrollable) -->
        <div class="p-6 bg-gray-50 overflow-y-auto">
          <!-- Info Ringkas -->
          <div class="bg-white p-4 rounded-lg shadow-sm mb-6 border border-gray-100">
            <div class="grid grid-cols-2 gap-4 text-sm">
              <div>
                <span class="block text-xs text-gray-400 font-bold uppercase">Penerima</span>
                <span class="font-bold text-gray-800">{{ searchResult?.nama }}</span>
              </div>
              <div>
                <span class="block text-xs text-gray-400 font-bold uppercase">Ekspedisi</span>
                <span class="font-bold text-blue-600">{{ searchResult?.ekspedisi }}</span>
              </div>
              <div class="col-span-2">
                <span class="block text-xs text-gray-400 font-bold uppercase">Isi Paket</span>
                <span class="text-gray-600">{{ searchResult?.paket }}</span>
              </div>
            </div>
          </div>

          <!-- Timeline -->
          <h4 class="text-sm font-bold text-gray-700 mb-4 ml-2">History Perjalanan</h4>
          <div class="relative border-l-2 border-gray-300 ml-4 space-y-6 pb-2">
            <div v-for="(log, index) in searchResult?.perjalanan" :key="index" class="relative pl-6">
              <!-- Dot -->
              <div class="absolute -left-[9px] top-1 w-4 h-4 rounded-full border-2 border-white bg-green-500"></div>
              <!-- Content -->
              <div>
                <p class="text-xs font-mono text-gray-400 mb-1">{{ log.waktu }}</p>
                <p class="font-bold text-sm text-gray-800">{{ log.keterangan }}</p>
              </div>
            </div>
            <!-- Empty State Timeline -->
            <div v-if="!searchResult?.perjalanan?.length" class="pl-6 text-sm text-gray-400 italic">
              Belum ada data perjalanan.
            </div>
          </div>

          <!-- Form Tambah Progress (Sesuai Soal) -->
          <div class="mt-8 pt-4 border-t border-gray-200">
            <h4 class="text-sm font-bold text-gray-700 mb-3">Update Status Pengiriman</h4>
            <form @submit.prevent="addProgress" class="flex gap-2">
              <input v-model="newProgressText" placeholder="Keterangan (misal: Paket Transit)"
                class="input-text text-sm" required />
              <button type="submit"
                class="bg-green-600 hover:bg-green-700 text-white px-4 rounded font-bold text-sm transition">
                Update
              </button>
            </form>
            <p class="text-xs text-gray-400 mt-2">*Waktu otomatis diambil dari server (local time)</p>
          </div>
        </div>

        <!-- Footer -->
        <div class="p-4 bg-white border-t flex justify-end shrink-0">
          <button @click="closeModal"
            class="px-4 py-2 bg-gray-200 hover:bg-gray-300 text-gray-700 rounded font-bold text-sm">Tutup</button>
        </div>
      </div>
    </div>

  </div>
</template>

<script setup>
import { library } from '@fortawesome/fontawesome-svg-core';
import {
  faArrowLeft,
  faArrowRight,
  faBox,
  faClipboardList,
  faMagnifyingGlass,
  faRightFromBracket,
  faSave,
  faTimes
} from '@fortawesome/free-solid-svg-icons';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import { computed, ref, watch } from 'vue';

library.add(faArrowLeft, faRightFromBracket, faMagnifyingGlass, faBox, faSave, faArrowRight, faTimes, faClipboardList);

// PROPS
const props = defineProps(['initialTracking', 'paketList', 'ekspedisiList']);
defineEmits(['back', 'logout']);

// STATE
const localTracking = ref([]);
const keyword = ref("");
const searchResult = ref(null);
const showModal = ref(false);
const newProgressText = ref("");
const doCounter = ref(1);

// FORM STATE
const inputDO = ref({
  nim: "", nama: "", ekspedisi: "", paketId: "", tanggal: new Date().toISOString().slice(0, 10), hargaDisplay: 0
});

// WATCHERS
// Sinkronisasi data awal dari JSON (karena struktur JSON sedikit nested, langsung ratakan)

watch(() => props.initialTracking, (val) => {
  if (val && val.length > 0) {
    // Mapping structure data JSON soal { "DO1": {...} } menjadi Array [{noDO: "DO1", ...}]
    const mapped = val.map(item => {
      const key = Object.keys(item)[0];
      return { noDO: key, ...item[key] };
    });
    // Merge dengan data lokal jika ada, atau replace
    if (localTracking.value.length === 0) {
      localTracking.value = mapped;
      doCounter.value = mapped.length + 1; // Update counter
    }
  }
}, { immediate: true });

// Watcher Harga Paket
watch(() => inputDO.value.paketId, (newId) => {
  const paket = props.paketList.find(p => p.kode === newId);
  inputDO.value.hargaDisplay = paket ? paket.harga : 0;
});

// COMPUTED
const generatedDONumber = computed(() => {
  const year = new Date().getFullYear();
  const seq = String(doCounter.value).padStart(4, "0");
  return `DO${year}-${seq}`;
});

const selectedPaketDetail = computed(() => {
  return props.paketList.find(p => p.kode === inputDO.value.paketId);
});

// METHODS HELPER
const formatRupiah = (val) => new Intl.NumberFormat("id-ID", { style: "currency", currency: "IDR" }).format(val || 0);

const formatDateIndo = (dateStr) => {
  if (!dateStr) return "-";
  const options = { year: 'numeric', month: 'long', day: 'numeric' };
  return new Date(dateStr).toLocaleDateString('id-ID', options);
};

// METHODS ACTION
const handleSearch = () => {
  if (!keyword.value) return alert("Masukkan Nomor DO atau NIM!");

  const found = localTracking.value.find(t =>
    t.noDO.toLowerCase() === keyword.value.toLowerCase() ||
    t.nim === keyword.value
  );

  if (found) {
    searchResult.value = found; // Pass by reference agar reaktif saat update progress
    showModal.value = true;
  } else {
    alert("Data tidak ditemukan!");
  }
};

const resetSearch = () => {
  keyword.value = "";
  searchResult.value = null;
};

const viewDetail = (item) => {
  searchResult.value = item;
  showModal.value = true;
};

const closeModal = () => {
  showModal.value = false;
  searchResult.value = null;
  newProgressText.value = "";
};

const simpanDO = () => {
  if (!inputDO.value.paketId || !inputDO.value.ekspedisi) return alert("Lengkapi Form!");

  const newDO = {
    noDO: generatedDONumber.value,
    nim: inputDO.value.nim,
    nama: inputDO.value.nama,
    ekspedisi: inputDO.value.ekspedisi,
    paket: inputDO.value.paketId,
    tanggalKirim: inputDO.value.tanggal,
    total: inputDO.value.hargaDisplay,
    status: "Manifested",
    perjalanan: [
      { waktu: new Date().toLocaleString('id-ID'), keterangan: "Data DO Dibuat (Manifested)" }
    ]
  };

  localTracking.value.unshift(newDO); // Add to top
  doCounter.value++;

  // Reset Form
  inputDO.value.nim = "";
  inputDO.value.nama = "";
  alert(`DO ${newDO.noDO} Berhasil Disimpan!`);
};

// Add Progress Feature (Sesuai Soal)
const addProgress = () => {
  if (!newProgressText.value) return;

  // Ambil waktu local
  const now = new Date().toLocaleString('id-ID'); // "d/M/yyyy, hh:mm:ss"

  if (!searchResult.value.perjalanan) {
    searchResult.value.perjalanan = [];
  }

  // Push ke array perjalanan item yang sedang aktif
  searchResult.value.perjalanan.unshift({
    waktu: now,
    keterangan: newProgressText.value
  });

  newProgressText.value = "";
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