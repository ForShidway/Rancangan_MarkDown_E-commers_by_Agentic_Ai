# SmartShop — Toko Online dengan Rekomendasi Produk Otomatis

> Platform e-commerce berbasis Laravel yang memberikan rekomendasi produk personal menggunakan kecerdasan buatan (Python + Machine Learning).

---

## Tech Stack

| Komponen | Teknologi |
|----------|-----------|
| 🌐 Backend | Laravel 11 + Laravel Breeze |
| 🗄️ Database | MySQL 8.0 |
| 🤖 AI Engine | Python 3.11 + FastAPI + Scikit-learn |
| ⚡ Cache / Queue | Redis 7 |
| 🐳 Container | Docker + Docker Compose |

---

## Fitur Utama

- ✅ **Autentikasi** — Register, Login, Logout (Laravel Breeze)
- ✅ **2 Role** — User (pembeli) dan Admin (pengelola toko)
- ✅ **CRUD Produk & Kategori** — Panel admin lengkap
- ✅ **Keranjang & Checkout** — Alur belanja lengkap
- ✅ **Riwayat Pesanan** — User bisa lihat semua pesanan
- ✅ **Rating & Ulasan** — Feedback produk dari pembeli
- ✅ **Rekomendasi AI** — "Mungkin Kamu Suka" berdasarkan riwayat belanja
- ✅ **Dashboard Admin** — Statistik, grafik, produk terlaris

---

## Cara Menjalankan (Development)

### 1. Clone & Setup

```bash
# Clone repository
git clone <url-repo> smartshop
cd smartshop

# Copy environment file
cp .env.example .env
```

### 2. Jalankan dengan Docker

```bash
# Build dan jalankan semua container
docker-compose up -d --build

# Masuk ke container Laravel
docker-compose exec app bash

# Setup aplikasi
composer install
php artisan key:generate
php artisan migrate --seed
php artisan storage:link
```

### 3. Akses Aplikasi

| Layanan | URL |
|---------|-----|
| Aplikasi Web | http://localhost:8000 |
| Python API | http://localhost:8001 |
| phpMyAdmin (opsional) | http://localhost:8080 |

### 4. Akun Default (Seeder)

| Role | Email | Password |
|------|-------|----------|
| Admin | admin@smartshop.com | password |
| User | user@smartshop.com | password |

---

## Struktur Proyek

```
smartshop/
├── agent/                  # Panduan untuk AI coding agent
│   ├── agent-rules.md      # Aturan coding & arsitektur
│   ├── context.md          # Konteks proyek lengkap
│   └── tasks.md            # Daftar tugas per fase
│
├── docs/                   # Dokumentasi teknis
│   ├── product.md          # Spesifikasi fitur & user stories
│   ├── architecture.md     # Diagram arsitektur sistem
│   ├── coding-standards.md # Standar penulisan kode
│   ├── api-endpoints.md    # Daftar semua endpoint API
│   └── decisions.md        # Catatan keputusan teknis
│
├── workflows/              # Alur kerja pengembangan
│   ├── create-crud.md      # Cara buat fitur CRUD baru
│   ├── create-feature.md   # Cara buat fitur kompleks
│   ├── setup-docker.md     # Setup & troubleshoot Docker
│   ├── setup-recommendation.md  # Setup Python ML engine
│   └── bugfix.md           # Alur perbaikan bug
│
├── app/                    # Kode Laravel
├── python/                 # Python Recommendation Engine
├── docker/                 # Konfigurasi Docker
├── database/               # Migration & Seeder
├── routes/                 # Route web & API
└── docker-compose.yml
```

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

## Lisensi

MIT License — dibuat untuk kebutuhan akademik / pembelajaran.
