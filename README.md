# SITTA UT - Sistem Informasi Tiras & Transaksi Bahan Ajar (Vue 3 SFC Version)

#### Dibuat oleh: \[Ananda Ning Saputra\] - \[043464564\]

#### Mata Kuliah: Pemrograman Berbasis Web (STSI4209)

##

Aplikasi web modern untuk pengelolaan stok bahan ajar dan pelacakan pengiriman (Delivery Order) di Universitas Terbuka.

Proyek ini merupakan **pengembangan lanjutan** dari Tugas 2, kini menggunakan arsitektur **Vue 3 Single File Components (SFC)** dengan **Vite** sebagai _build tool_ untuk performa yang lebih cepat dan struktur kode yang lebih modular.

## 🌟 Fitur Unggulan

### 1\. 🔐 Sistem Autentikasi & Keamanan

- **Login Page :** Halaman login interaktif dengan validasi dan background visual.
- **Session Management :** Menggunakan **Cookies** (valid 3 jam) untuk menyimpan sesi login.
- **Navigation Guard :** Mencegah akses ke halaman internal tanpa login, sehingga aplikasi lebih aman.
- **Animasi Transisi :** Efek _Zoom In/Out_ halus saat Login/Logout, dan sliding saat berpindah halaman, mirip dengan aplikasi _iOS Native_ dan dapat meningkatkan UX _satisfied_ juga.

### 2\. 📦 Manajemen Stok (StockView.vue)

- **CRUD Lengkap :** Tambah, Edit, dan Hapus data bahan ajar.
- **Modal Form :** Input dan edit data dilakukan via _pop-up modal_ yang bersih.
- **Advanced Filter :**
  - Filter UT-Daerah & Kategori (Dependent Dropdown).
  - Filter khusus Stok Menipis/Kosong.
- **Visual Indikator :** Status stok (Aman, Menipis, Kosong) dengan warna dan ikon dinamis.
- **Tooltips :** Informasi tambahan (HTML) saat kursor diarahkan ke status.

### 3\. 🚚 Tracking Delivery Order (TrackingView.vue)

- **Live Search :** Cari status pengiriman berdasarkan **Nomor DO** atau **NIM**.
- **Visual Timeline :** Melacak perjalanan paket langkah demi langkah dengan tampilan grafis.
- **Update Status :** Fitur untuk menambahkan _progress log_ baru ke dalam timeline.
- **Auto Numbering :** Nomor DO otomatis ter-generate (DO2025-XXXX).
- **Kalkulasi Otomatis :** Total harga terhitung otomatis berdasarkan paket yang dipilih.

### 4\. 📱 UI/UX Modern

- **Animasi Slide :** Transisi antar menu (Dashboard &lt;-&gt; Fitur) dengan efek geser ala iOS.
- **Responsive Design :** Tampilan optimal di Desktop dan Tablet.
- **Clean Code :** Struktur komponen terpisah (templates/ dan components/).

## 🛠️ Teknologi yang Digunakan

- **Framework :** Vue.js 3 (Composition API, &lt;script setup&gt;)
- **Build Tool :** Vite
- **Styling :** Tailwind CSS (Utility-first)
- **Icons :** FontAwesome 6 (Vue Component)
- **Data Source :** JSON File (Simulasi REST API)

## 📂 Struktur Proyek

```src/
├── assets/ # Gambar & Font statis
├── components/ # Komponen kecil (Re-usable)
│ ├── StockTable.vue
│ └── DoTracking.vue
├── templates/ # Komponen Halaman (Views)
│ ├── LoginView.vue
│ ├── DashboardMenu.vue
│ ├── StockView.vue
│ └── TrackingView.vue
├── App.vue # Main Controller (Auth & Controlling SFC View)
├── main.js # Entry Point
└── style.css # Tailwind Import
public/
└── data/
└── dataBahanAjar.json # Database Dummy
```

## 🚀 Cara Menjalankan (Installation)

Pastikan **Node.js** sudah terinstall di komputer Anda.

- #### Install Dependencies

  Masuk ke folder proyek dan jalankan:  
   npm install

- #### Jalankan Mode Development
  Untuk menjalankan server lokal dengan fitur Hot Reload:  
   `npm run dev`  
   <br/>Buka alamat yang muncul di terminal (biasanya <http://localhost:5173>).
- #### Build untuk Production (Opsional)
  Jika ingin membuat file statis siap upload:  
   `npm run build`

## 🔐 Akun Demo

Gunakan kredensial berikut untuk masuk ke aplikasi:

| **Role**  | **Email**        | **Password** |
| --------- | ---------------- | ------------ |
| **Admin** | <admin@ut.ac.id> | P@ssw0rd     |

## ⚠️ Catatan Pengembang

- **Penyimpanan Data :** Aplikasi ini menggunakan simulasi data (fetch JSON). Perubahan data (Tambah/Edit/Hapus) hanya tersimpan di **memori browser (State)** dan akan hilang jika halaman di-_refresh_ (F5). Ini adalah perilaku wajar untuk aplikasi _Frontend-only_ tanpa _Database Backend_.
- **Gambar Background :** Pastikan file src/assets/img/cloud.jpg tersedia agar background halaman login muncul.
