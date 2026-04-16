# Context Proyek — Toko Online Rekomendasi Produk Otomatis

---

## Nama Proyek

**SmartShop** — Toko Online dengan Rekomendasi Produk Berbasis AI

---

## Deskripsi Proyek

SmartShop adalah platform e-commerce yang mampu memberikan **rekomendasi produk otomatis** kepada pengguna berdasarkan:
- Riwayat pembelian (purchase history)
- Riwayat pencarian (search history)
- Produk yang sering dilihat (view history)

Sistem rekomendasi dibangun menggunakan Python (machine learning), sedangkan platform utama menggunakan Laravel sebagai backend dan frontend berbasis Blade Template.

---

## Stack Teknologi

| Komponen             | Teknologi                          | Versi         |
|----------------------|------------------------------------|---------------|
| Backend Framework    | Laravel + Laravel Breeze           | Laravel 11+   |
| Database Utama       | MySQL                              | 8.0+          |
| Engine Rekomendasi   | Python + FastAPI + Scikit-learn    | Python 3.11+  |
| Cache & Queue        | Redis                              | 7.x           |
| Containerisasi       | Docker + Docker Compose            | Latest        |
| Autentikasi          | Laravel Breeze (Blade)             | -             |
| CSS Framework        | Bootstrap 5 / Tailwind CSS         | -             |

---

## Role Pengguna

### 1. Admin
- Login ke panel admin
- Kelola **produk** (tambah, edit, hapus, lihat detail)
- Kelola **kategori** produk
- Kelola **order/pesanan** pengguna
- Lihat **laporan penjualan** dan **dashboard statistik**
- Kelola **pengguna terdaftar**
- Konfigurasi **banner/promo** halaman utama

### 2. User (Pembeli)
- Registrasi dan login
- Browsing produk berdasarkan kategori
- **Menerima rekomendasi produk** secara personal
- Tambah produk ke **keranjang belanja (cart)**
- **Checkout dan pembelian**
- Lihat **riwayat pesanan**
- Berikan **ulasan/rating** produk
- Kelola **profil akun**

---

## Fitur Utama

### Fitur E-Commerce
- [x] Autentikasi (Register, Login, Logout) — Laravel Breeze
- [x] Manajemen Produk (CRUD) — Admin
- [x] Manajemen Kategori (CRUD) — Admin
- [x] Halaman Detail Produk
- [x] Keranjang Belanja (Cart)
- [x] Proses Checkout & Order
- [x] Manajemen Order (Admin & User)
- [x] Upload Gambar Produk
- [x] Pencarian Produk
- [x] Filter Produk per Kategori
- [x] Rating & Ulasan Produk

### Fitur Rekomendasi Produk (AI/ML)
- [x] Rekam riwayat browsing produk
- [x] Rekam riwayat pencarian
- [x] Kirim data ke Python Recommendation Engine via API
- [x] Tampilkan rekomendasi "Produk Mungkin Kamu Suka"
- [x] Cache hasil rekomendasi di Redis
- [x] Update rekomendasi saat ada pembelian baru

### Fitur Admin Dashboard
- [x] Statistik total produk, order, pengguna
- [x] Grafik penjualan harian/bulanan
- [x] Produk terlaris
- [x] Order terbaru

---

## Arsitektur Sistem

```
┌─────────────────────────────────────────────────────┐
│                  Docker Cloud                        │
│                                                      │
│  ┌──────────────┐    ┌──────────────────────────┐   │
│  │   Laravel    │◄──►│  Python FastAPI           │   │
│  │  (Port 8000) │    │  Recommendation Engine   │   │
│  │              │    │  (Port 8001)             │   │
│  └──────┬───────┘    └──────────────────────────┘   │
│         │                                            │
│  ┌──────▼───────┐    ┌──────────────────────────┐   │
│  │    MySQL     │    │         Redis             │   │
│  │  (Port 3306) │    │       (Port 6379)         │   │
│  └──────────────┘    └──────────────────────────┘   │
└─────────────────────────────────────────────────────┘
```

---

## Struktur Database Utama

| Tabel              | Fungsi                                      |
|--------------------|---------------------------------------------|
| `users`            | Data pengguna (pembeli & admin)             |
| `categories`       | Kategori produk                             |
| `products`         | Data produk                                 |
| `product_images`   | Galeri gambar produk                        |
| `orders`           | Data pesanan                                |
| `order_items`      | Item dalam pesanan                          |
| `carts`            | Keranjang belanja                           |
| `cart_items`       | Item dalam keranjang                        |
| `reviews`          | Rating dan ulasan produk                    |
| `search_histories` | Riwayat pencarian pengguna                  |
| `view_histories`   | Riwayat produk yang dilihat                 |
| `recommendations`  | Cache hasil rekomendasi dari Python         |

---

## Struktur Folder Proyek

```
project-root/
│
├── docs/                        # Dokumentasi proyek
│   ├── product.md               # Spesifikasi produk
│   ├── architecture.md          # Diagram arsitektur sistem
│   ├── coding-standards.md      # Standar penulisan kode
│   ├── api-endpoints.md         # Daftar endpoint API
│   └── decisions.md             # Catatan keputusan teknis
│
├── agent/                       # Panduan untuk AI Agent
│   ├── agent-rules.md           # Aturan coding dan arsitektur
│   ├── context.md               # Konteks proyek ini
│   └── tasks.md                 # Daftar tugas pengembangan
│
├── workflows/                   # Alur kerja pengembangan
│   ├── create-crud.md           # Cara membuat fitur CRUD baru
│   ├── create-feature.md        # Cara membuat fitur baru
│   ├── setup-docker.md          # Setup Docker environment
│   ├── setup-recommendation.md  # Setup Python recommendation engine
│   └── bugfix.md                # Alur kerja perbaikan bug
│
├── app/                         # Kode Laravel
│   ├── Http/Controllers/
│   │   ├── Admin/               # Controller khusus admin
│   │   └── User/                # Controller khusus user
│   ├── Services/                # Service layer
│   ├── Repositories/            # Repository layer
│   └── Models/
│
├── python/                      # Kode Python Recommendation Engine
│   ├── app/
│   ├── models/                  # Saved ML models
│   └── requirements.txt
│
├── docker/                      # Konfigurasi Docker
│   ├── nginx/
│   ├── php/
│   └── python/
│
├── database/
│   ├── migrations/
│   └── seeders/
│
├── routes/
│   ├── web.php                  # Routes web (user & admin)
│   └── api.php                  # Routes API (untuk Python)
│
├── docker-compose.yml           # Konfigurasi Docker Compose
├── .env.example
└── README.md
```

---

## Environment Variables Utama

```env
# Laravel
APP_NAME=SmartShop
APP_URL=http://localhost:8000

# Database
DB_HOST=mysql
DB_DATABASE=smartshop
DB_USERNAME=root
DB_PASSWORD=secret

# Redis
REDIS_HOST=redis
REDIS_PORT=6379

# Python Recommendation Engine
RECOMMENDATION_API_URL=http://python:8001

# Cache
CACHE_DRIVER=redis
QUEUE_CONNECTION=redis
SESSION_DRIVER=redis
```
