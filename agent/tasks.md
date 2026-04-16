# Daftar Tugas Pengembangan — SmartShop E-Commerce

Daftar tugas pengembangan proyek SmartShop secara bertahap, dari setup awal hingga fitur rekomendasi produk.

---

## FASE 1: Setup Awal & Infrastruktur

### 1.1 Setup Proyek
- [ ] Inisialisasi proyek Laravel baru
- [ ] Install Laravel Breeze untuk autentikasi
- [ ] Konfigurasi file `.env` (database, redis, dll)
- [ ] Setup Docker Compose (Laravel, MySQL, Redis, Python)
- [ ] Uji koneksi semua container Docker
- [ ] Setup `.env.example` sebagai template

### 1.2 Konfigurasi Database
- [ ] Buat database `smartshop` di MySQL
- [ ] Rancang dan buat semua migration tabel utama:
  - [ ] `users` (dengan kolom `role`: admin/user)
  - [ ] `categories`
  - [ ] `products`
  - [ ] `product_images`
  - [ ] `orders`
  - [ ] `order_items`
  - [ ] `carts`
  - [ ] `cart_items`
  - [ ] `reviews`
  - [ ] `search_histories`
  - [ ] `view_histories`
  - [ ] `recommendations`
- [ ] Buat Seeder untuk data dummy (kategori, produk, admin)

---

## FASE 2: Autentikasi & Role Management

### 2.1 Laravel Breeze Setup
- [ ] Install dan konfigurasi Laravel Breeze
- [ ] Tambahkan kolom `role` pada tabel `users` (admin/user)
- [ ] Buat middleware `AdminMiddleware` untuk proteksi route admin
- [ ] Redirect setelah login berdasarkan role

### 2.2 Halaman Auth
- [ ] Halaman Register (dengan validasi)
- [ ] Halaman Login (dengan validasi)
- [ ] Halaman Lupa Password
- [ ] Halaman Edit Profil User

---

## FASE 3: Panel Admin (CRUD)

### 3.1 Dashboard Admin
- [ ] Tampilkan total produk, pengguna, order
- [ ] Grafik penjualan (chart.js)
- [ ] Tabel order terbaru
- [ ] Tabel produk terlaris

### 3.2 Manajemen Kategori
- [ ] `CategoryController` (index, create, store, edit, update, destroy)
- [ ] `CategoryService` — logika validasi kategori
- [ ] `CategoryRepository` — query database
- [ ] View: list kategori, form tambah, form edit
- [ ] Validasi: nama kategori unik, tidak boleh kosong

### 3.3 Manajemen Produk
- [ ] `ProductController` (index, create, store, show, edit, update, destroy)
- [ ] `ProductService` — handle upload gambar, kalkulasi stok
- [ ] `ProductRepository` — query database produk
- [ ] View: list produk, form tambah, form edit, detail produk
- [ ] Upload gambar produk (support multiple images)
- [ ] Validasi: nama, harga, stok, deskripsi wajib diisi

### 3.4 Manajemen Order
- [ ] `OrderController` (index, show, update status)
- [ ] Tampilkan semua order dengan filter status
- [ ] Update status order (pending → processing → shipped → delivered)
- [ ] Detail order (produk apa saja, alamat pengiriman, total)

### 3.5 Manajemen Pengguna
- [ ] Lihat daftar semua pengguna
- [ ] Blokir/aktifkan akun pengguna
- [ ] Lihat riwayat order per pengguna

---

## FASE 4: Fitur E-Commerce (User)

### 4.1 Halaman Utama (Homepage)
- [ ] Banner/hero section
- [ ] Produk rekomendasi (dari engine Python)
- [ ] Produk terbaru
- [ ] Produk terlaris
- [ ] Kategori populer

### 4.2 Halaman Produk
- [ ] Daftar produk dengan paginasi
- [ ] Filter produk berdasarkan kategori
- [ ] Filter berdasarkan harga (min-max)
- [ ] Pencarian produk (search bar)
- [ ] Halaman detail produk

### 4.3 Keranjang Belanja
- [ ] Tambah produk ke cart
- [ ] Update jumlah item di cart
- [ ] Hapus item dari cart
- [ ] Tampilkan subtotal dan total harga
- [ ] Simpan cart ke database (bukan session)

### 4.4 Checkout & Order
- [ ] Form checkout (alamat pengiriman, metode pembayaran)
- [ ] Validasi stok sebelum checkout
- [ ] Buat order dari cart
- [ ] Kurangi stok produk saat order dibuat
- [ ] Halaman konfirmasi order

### 4.5 Riwayat Pesanan User
- [ ] Daftar semua pesanan user
- [ ] Detail pesanan (status, produk, total)
- [ ] Batalkan pesanan (jika masih pending)

### 4.6 Rating & Ulasan
- [ ] Form rating bintang (1-5) + komentar
- [ ] Tampilkan rating dan ulasan di halaman produk
- [ ] User hanya bisa ulasan produk yang sudah dibeli

---

## FASE 5: Sistem Rekomendasi (Python + Redis)

### 5.1 Pengumpulan Data Perilaku
- [ ] Rekam setiap produk yang dilihat user (`view_histories`)
- [ ] Rekam setiap kata kunci pencarian user (`search_histories`)
- [ ] Rekam riwayat pembelian (dari `order_items`)

### 5.2 Python Recommendation Engine
- [ ] Setup project FastAPI di folder `/python`
- [ ] Endpoint `POST /recommend` — menerima `user_id`, mengembalikan list `product_id`
- [ ] Implementasi algoritma rekomendasi:
  - [ ] **Collaborative Filtering** (berdasarkan kesamaan user)
  - [ ] **Content-Based Filtering** (berdasarkan kategori & tag produk)
  - [ ] **Hybrid** (gabungan keduanya)
- [ ] Simpan model yang sudah dilatih ke `/python/models/`
- [ ] Tambahkan endpoint `POST /train` untuk melatih ulang model

### 5.3 Integrasi Laravel ↔ Python
- [ ] Buat `RecommendationService` di Laravel
- [ ] Panggil Python API dari `RecommendationService`
- [ ] Simpan hasil rekomendasi ke Redis (TTL: 60 menit)
- [ ] Fallback ke produk populer jika rekomendasi gagal

### 5.4 Tampilan Rekomendasi di Frontend
- [ ] Section "Mungkin Kamu Suka" di homepage
- [ ] Section "Produk Serupa" di halaman detail produk
- [ ] Refresh rekomendasi setelah user melakukan pembelian

---

## FASE 6: Pengujian & Deployment

### 6.1 Testing
- [ ] Unit test untuk Service layer
- [ ] Feature test untuk alur checkout
- [ ] Test endpoint API rekomendasi Python
- [ ] Test integrasi Laravel ↔ Python ↔ Redis

### 6.2 Deployment
- [ ] Konfigurasi environment production di `.env`
- [ ] Build Docker image untuk production
- [ ] Deploy ke cloud (DigitalOcean / AWS / GCP)
- [ ] Setup CI/CD pipeline (opsional)
- [ ] Konfigurasi SSL/HTTPS

---

## Catatan Prioritas

| Fase | Prioritas | Estimasi |
|------|-----------|----------|
| Fase 1 — Setup | 🔴 Kritis | 1-2 hari |
| Fase 2 — Auth | 🔴 Kritis | 1 hari |
| Fase 3 — Admin CRUD | 🔴 Kritis | 3-4 hari |
| Fase 4 — E-Commerce | 🟠 Tinggi | 4-5 hari |
| Fase 5 — Rekomendasi | 🟡 Menengah | 3-4 hari |
| Fase 6 — Testing & Deploy | 🟢 Rendah | 2-3 hari |
