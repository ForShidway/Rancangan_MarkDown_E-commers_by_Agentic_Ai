# SmartShop — Platform E-Commerce dengan Rekomendasi Produk AI

> **SmartShop** adalah platform e-commerce yang menyediakan pengalaman belanja personal dengan sistem rekomendasi produk otomatis berbasis kecerdasan buatan (Machine Learning).

---

## Sistem yang Dibangun

SmartShop adalah aplikasi web e-commerce yang memungkinkan:

### Untuk **Pembeli (User)**
- Melihat dan mencari produk dari katalog toko
- Menambahkan produk ke keranjang dan melakukan checkout
- Mendapatkan rekomendasi produk personal ("Rekomendasi Untuk Kamu") berdasarkan riwayat belanja
- Memberi rating dan ulasan pada produk yang telah dibeli
- Melihat riwayat pesanan dan status pengiriman

### Untuk **Admin (Pengelola Toko)**
- Mengelola katalog produk (tambah, edit, hapus) beserta gambar
- Mengelola kategori produk
- Melihat dashboard dengan statistik penjualan dan grafik
- Mengelola pesanan pelanggan dan mengubah status pengiriman
- Memantau data pengguna yang terdaftar

---

## 🔄 Alur Kerja Sistem

### **Alur Non-Rekomendasi** (Standard E-commerce)

```
User Login → Lihat Produk → Pilih Produk → Tambah ke Keranjang 
   ↓            ↓ (disimpan di database)
Checkout → Konfirmasi Order → Order Tersimpan di Database
```

- Semua data (produk, kategori, order, user) disimpan di MySQL
- Admin dapat mengelola inventory dan pesanan secara realtime

### **Alur Rekomendasi** (AI-Powered)

```
User Belanja/Browsing → Events Tercatat:
   • Produk apa yang dilihat (view_histories)
   • Produk apa yang dicari (search_histories)
   • Produk apa yang dibeli (purchase history)
   
   ↓
   
Recommendation Engine (Python) menerima user_id dari Laravel
   • Load data riwayat user dari database MySQL
   • Analisis preferensi menggunakan ML model (Scikit-learn)
   • Hitung similarity antar produk
   • Ranking top-N rekomendasi
   
   ↓
   
Cache hasil di Redis (TTL 60 menit)
   
   ↓
   
Display "Rekomendasi Untuk Kamu" di Homepage & Detail Produk
```

### **Admin Workflow**

```
Admin Login → Dashboard (Statistik & Grafik)
   ↓
   ├─ Kelola Produk (CRUD) → Upload Gambar → Simpan ke Database
   ├─ Kelola Kategori (CRUD) → Definisi produk grouping
   ├─ Kelola Order → Update Status → Notifikasi ke User
   └─ Lihat User Management → Monitor aktivitas pembeli
```

---

## Arsitektur Teknis

### **Komponen Utama**

```
┌─────────────────────────────────────────────────┐
│         Browser Client (User / Admin)           │
│                   HTTP/HTTPS                    │
└────────────────┬────────────────────────────────┘
                 │
┌────────────────▼──────────────────────────────┐
│      Laravel 11 Backend (Port 8000)           │
│  • Routes & Controllers                       │
│  • Autentikasi & Authorization                │
│  • Business Logic (Services)                  │
│  • Database Queries (Repositories)            │
└──┬──────────────────────────┬─────────────────┘
   │                          │
   │ (HTTP Call untuk AI)    │ (SQL Query)
   │                          │
   ▼                          ▼
┌─────────────────┐    ┌──────────────┐
│ Python FastAPI  │    │ MySQL 8.0    │
│ (Port 8001)     │    │ Database     │
│ • ML Model      │    │              │
│ • Predict API   │    └──────────────┘
│ • Cache Layer   │
└─────────────────┘    Redis 7
                       (Cache & Queue)
```

---

## 💻 Teknologi yang Digunakan

| Layer | Teknologi | Fungsi |
|-------|-----------|--------|
| **Frontend** | HTML5, CSS3, Blade Template | Rendering halaman web |
| **Backend** | Laravel 11, Laravel Breeze | Framework web, autentikasi |
| **Database** | MySQL 8.0 | Penyimpanan data terpusat |
| **AI Engine** | Python 3.11, FastAPI | REST API untuk rekomendasi |
| **ML Library** | Scikit-learn | Algoritma machine learning |
| **Cache & Queue** | Redis 7 | Caching hasil rekomendasi, session |
| **Container** | Docker, Docker Compose | Orchestration semua service |

### **Alasan Pemilihan Teknologi**

- **Laravel** → Framework web mature, built-in ORM (Eloquent), authorization system
- **MySQL** → Database relasional solid, cocok untuk e-commerce
- **Python + FastAPI** → FastAPI ringan & cepat, Scikit-learn library ML populer & mudah
- **Redis** → In-memory cache untuk performa tinggi, cocok untuk rekomendasi yang sering diakses
- **Docker** → Isolasi environment, scalability, mudah deployment di berbagai server

---

## Struktur Dokumentasi

Dokumentasi rancangan proyek tersimpan dalam folder-folder berikut:

```
├── agent/                      # Panduan untuk Development
│   ├── agent-rules.md          # Aturan coding & arsitektur
│   ├── context.md              # Konteks proyek lengkap
│   └── tasks.md                # Daftar tugas pengembangan per fase
│
├── docs/                       # Dokumentasi Teknis
│   ├── product.md              # Spesifikasi fitur & user stories
│   ├── architecture.md         # Diagram arsitektur & alur data
│   ├── coding-standards.md     # Standar penulisan kode
│   ├── api-endpoints.md        # Dokumentasi REST API endpoints
│   └── decisions.md            # Catatan keputusan teknis
│
└── workflows/                  # Panduan Implementasi
    ├── create-crud.md          # Cara implementasi fitur CRUD
    ├── create-feature.md       # Cara implementasi fitur kompleks
    ├── setup-docker.md         # Setup & troubleshoot Docker
    ├── setup-recommendation.md # Setup engine rekomendasi Python
    └── bugfix.md               # Alur perbaikan bug
```

**Untuk detail lengkap**, buka file-file dokumentasi di atas sesuai kebutuhan Anda.

---

### Akun Default (dari Seeder)

- **Admin** → `admin@smartshop.com` / `password`
- **User** → `user@smartshop.com` / `password`

---

## 📝 Fase Pengembangan

1. **Fase 1** → Setup infrastruktur & database schema
2. **Fase 2** → Sistem autentikasi & role management
3. **Fase 3** → Panel admin (CRUD produk, kategori, order)
4. **Fase 4** → Fitur user (browse, search, cart, checkout)
5. **Fase 5** → Sistem rating & ulasan
6. **Fase 6** → Engine rekomendasi AI & integrasi

Lihat `agent/tasks.md` untuk detail task per fase.

---


## Arsitektur Singkat

```
Browser → Laravel (Controller → Service → Repository → Model → MySQL)
                    ↓
              RecommendationService
                    ↓
            Python FastAPI ↔ Redis
```

Untuk detail lengkap lihat [`docs/architecture.md`](docs/architecture.md).

---

## Dokumentasi Lengkap

| Dokumen | Deskripsi |
|---------|-----------|
| [agent/agent-rules.md](agent/agent-rules.md) | Aturan coding untuk AI agent |
| [agent/context.md](agent/context.md) | Konteks dan stack proyek |
| [agent/tasks.md](agent/tasks.md) | Backlist tugas pengembangan |
| [docs/architecture.md](docs/architecture.md) | Arsitektur sistem lengkap |
| [docs/api-endpoints.md](docs/api-endpoints.md) | Dokumentasi semua endpoint |
| [docs/coding-standards.md](docs/coding-standards.md) | Standar penulisan kode |
| [docs/decisions.md](docs/decisions.md) | Catatan keputusan teknis |

---
