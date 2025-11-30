<template>
  <div class="relative w-full h-screen overflow-hidden bg-gray-100">
    <Transition :name="authTransition">
      <LoginView v-if="!isLoggedIn" class="absolute inset-0 w-full h-full z-50" :is-loading="isLoggingIn"
        :error-msg="loginError" @login="handleLogin" />
      <div v-else class="absolute inset-0 w-full h-full z-40 bg-gray-50 font-sans text-gray-800">
        <div v-if="isLoadingData" class="h-full flex flex-col items-center justify-center text-gray-500">
          <font-awesome-icon icon="spinner" spin class="text-4xl text-blue-500 mb-4" />
          <p>Sedang memuat data aplikasi...</p>
        </div>
        <div v-else class="relative w-full h-full overflow-hidden">
          <Transition :name="transitionName">
            <div :key="tab" class="absolute w-full h-full top-0 left-0 bg-gray-50 overflow-y-auto">
              <DashboardMenu v-if="tab === 'menu'" @navigate="changeTab" @logout="handleLogout" />
              <StockView v-else-if="tab === 'stok'" :initial-data="state.stok" :upbjj-list="state.upbjjList"
                :kategori-list="state.kategoriList" @back="changeTab('menu')" @logout="handleLogout" />
              <TrackingView v-else-if="tab === 'tracking'" :initial-tracking="state.tracking" :paket-list="state.paket"
                :ekspedisi-list="state.pengirimanList" @back="changeTab('menu')" @logout="handleLogout" />
            </div>
          </Transition>
        </div>
      </div>
    </Transition>
  </div>
</template>

<script setup>
import { library } from '@fortawesome/fontawesome-svg-core';
import {
  faArrowLeft, faArrowRight, faBan, faBox, faBoxOpen, faBuilding,
  faCheckCircle, faCircleExclamation, faClipboardList, faEnvelope,
  faExclamationCircle, faFilter, faFolderOpen, faLock, faMagnifyingGlass,
  faMapPin, faPen, faPenToSquare, faPlus, faPlusCircle, faRightFromBracket,
  faSave, faSpinner, faTimes, faTrash, faTriangleExclamation, faTruck,
  faTruckFast, faUniversity
} from '@fortawesome/free-solid-svg-icons';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import { onMounted, reactive, ref } from 'vue';
import DashboardMenu from './templates/DashboardMenu.vue';
import LoginView from './templates/LoginView.vue';
import StockView from './templates/StockView.vue';
import TrackingView from './templates/TrackingView.vue';
library.add(
  faUniversity, faCircleExclamation, faEnvelope, faLock, faSpinner, faArrowRight,
  faRightFromBracket, faBox, faTruck, faFolderOpen, faTruckFast, faArrowLeft, faPlus,
  faMagnifyingGlass, faSave, faTimes, faClipboardList, faPen, faTrash, faFilter,
  faPlusCircle, faPenToSquare, faBoxOpen, faTriangleExclamation,
  faCheckCircle, faExclamationCircle, faBan, faMapPin, faBuilding
);
const isLoggedIn = ref(false);
const loginError = ref('');
const isLoggingIn = ref(false);
const COOKIE_NAME = 'sitta_session_v3';
const authTransition = ref('zoom-in');
const tab = ref('menu');
const isLoadingData = ref(false);
const transitionName = ref('slide-left');
const state = reactive({
  stok: [],
  tracking: [],
  upbjjList: [],
  kategoriList: [],
  pengirimanList: [],
  paket: []
});
const tabDepth = { 'menu': 0, 'stok': 1, 'tracking': 1 };
const changeTab = (target) => {
  const fromDepth = tabDepth[tab.value];
  const toDepth = tabDepth[target];
  transitionName.value = toDepth < fromDepth ? 'slide-right' : 'slide-left';
  tab.value = target;
};
const handleLogin = ({ email, password }) => {
  isLoggingIn.value = true;
  loginError.value = '';
  setTimeout(() => {
    if (email === 'admin@ut.ac.id' && password === 'P@ssw0rd') {
      const d = new Date();
      d.setTime(d.getTime() + (3 * 60 * 60 * 1000));
      const token = btoa(email + ':' + Date.now());
      document.cookie = `${COOKIE_NAME}=${token};expires=${d.toUTCString()};path=/`;
      authTransition.value = 'zoom-in';
      isLoggedIn.value = true;
      tab.value = 'menu';
      fetchData();
    } else {
      loginError.value = 'Email atau Password salah!';
    }
    isLoggingIn.value = false;
  }, 800);
};
const handleLogout = () => {
  document.cookie = `${COOKIE_NAME}=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/;`;
  authTransition.value = 'zoom-out';
  isLoggedIn.value = false;
  setTimeout(() => {
    tab.value = 'menu';
    state.stok = [];
  }, 500);
};
const checkAuth = () => {
  const cookies = document.cookie.split(';');
  const session = cookies.find(c => c.trim().startsWith(`${COOKIE_NAME}=`));
  if (session) {
    isLoggedIn.value = true;
    fetchData();
  }
};
const fetchData = async () => {
  isLoadingData.value = true;
  try {
    const response = await fetch('/data/dataBahanAjar.json');
    if (!response.ok) throw new Error('Gagal load data (404)');
    const data = await response.json();
    Object.assign(state, data);
  } catch (e) {
    console.error("Error Fetch Data:", e);
  } finally {
    isLoadingData.value = false;
  }
};
onMounted(() => {
  checkAuth();
});
</script>

<style>
.zoom-in-enter-active,
.zoom-in-leave-active {
  transition: all 0.5s cubic-bezier(0.22, 1, 0.36, 1);
}

.zoom-in-enter-from {
  opacity: 0;
  transform: scale(0.9);
}

.zoom-in-enter-to {
  opacity: 1;
  transform: scale(1);
}

.zoom-in-leave-from {
  opacity: 1;
  transform: scale(1);
}

.zoom-in-leave-to {
  opacity: 0;
  transform: scale(1.1);
}

.zoom-out-enter-active,
.zoom-out-leave-active {
  transition: all 0.5s cubic-bezier(0.22, 1, 0.36, 1);
}

.zoom-out-enter-from {
  opacity: 0;
  transform: scale(1.1);
}

.zoom-out-enter-to {
  opacity: 1;
  transform: scale(1);
}

.zoom-out-leave-from {
  opacity: 1;
  transform: scale(1);
}

.zoom-out-leave-to {
  opacity: 0;
  transform: scale(0.9);
  z-index: 50;
}

.slide-left-enter-active,
.slide-left-leave-active,
.slide-right-enter-active,
.slide-right-leave-active {
  transition: all 0.4s cubic-bezier(0.2, 0.8, 0.2, 1);
}

.slide-left-enter-from {
  opacity: 0;
  transform: translateX(100%);
}

.slide-left-leave-to {
  opacity: 0.5;
  transform: translateX(-25%);
}

.slide-right-enter-from {
  opacity: 0.5;
  transform: translateX(-25%);
}

.slide-right-leave-to {
  opacity: 0;
  transform: translateX(100%);
  z-index: 10;
}
</style>