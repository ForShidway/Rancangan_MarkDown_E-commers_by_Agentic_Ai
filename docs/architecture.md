# Arsitektur Sistem — SmartShop E-Commerce

---

## Gambaran Umum

SmartShop menggunakan arsitektur **microservice sederhana** di mana laravel sebagai backend utama dan Python sebagai layanan terpisah untuk engine rekomendasi. Semua dijalankan di Docker.

---

## Diagram Arsitektur

```
┌─────────────────────────────────────────────────────────────────┐
│                        Docker Cloud Network                      │
│                                                                  │
│  ┌───────────────────────┐       ┌────────────────────────────┐ │
│  │   Browser / Client    │       │   Admin Browser            │ │
│  └───────────┬───────────┘       └──────────────┬─────────────┘ │
│              │ HTTP                              │ HTTP          │
│  ┌───────────▼───────────────────────────────────▼─────────────┐│
│  │                  Laravel App (Port 8000)                     ││
│  │                                                              ││
│  │  ┌──────────────┐  ┌─────────────────┐  ┌────────────────┐ ││
│  │  │  Controllers │→ │    Services     │→ │  Repositories  │ ││
│  │  └──────────────┘  └────────┬────────┘  └───────┬────────┘ ││
│  │                             │                    │          ││
│  │                   ┌─────────▼──────────┐  ┌─────▼────────┐ ││
│  │                   │ RecommendService   │  │   Models     │ ││
│  │                   │ (HTTP ke Python)   │  │  (Eloquent)  │ ││
│  │                   └─────────┬──────────┘  └─────┬────────┘ ││
│  └─────────────────────────────│──────────────────│───────────┘│
│                                │                  │             │
│  ┌─────────────────────────────▼──┐   ┌──────────▼────────┐   │
│  │  Python FastAPI (Port 8001)    │   │   MySQL (3306)    │   │
│  │                                │   │                   │   │
│  │  POST /api/recommend           │   │  Database Utama   │   │
│  │  POST /api/train               │   │                   │   │
│  └──────────────┬─────────────────┘   └───────────────────┘   │
│                 │                                               │
│  ┌──────────────▼──────────────────────────────────────────┐   │
│  │                  Redis (Port 6379)                       │   │
│  │  - Cache rekomendasi (TTL 60 menit)                     │   │
│  │  - Session data                                         │   │
│  │  - Queue jobs                                           │   │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Arsitektur Laravel (Internal)

```
Request
   │
   ▼
Middleware (Auth, Admin, Throttle)
   │
   ▼
Route (web.php / api.php)
   │
   ▼
Controller (hanya terima request & kirim response)
   │
   ▼
Form Request (validasi input)
   │
   ▼
Service (logika bisnis)
   │
   ├──────────────────────┐
   ▼                      ▼
Repository          RecommendationService
(query DB)          (HTTP ke Python API)
   │
   ▼
Model (Eloquent)
   │
   ▼
Database (MySQL)
```

---

## Arsitektur Python (Recommendation Engine)

```
POST /api/recommend
   │
   ▼
Pydantic Validation (RecommendRequest)
   │
   ▼
RecommenderService
   │
   ├── Cek Redis Cache ─── (hit) ──► return cached result
   │
   ├── (miss) Load saved ML model (.pkl)
   │
   ├── Generate rekomendasi untuk user_id
   │
   ├── Simpan ke Redis (TTL 3600s)
   │
   └── return RecommendResponse
```

---

## Aliran Data: Rekomendasi Produk

```
1. User membuka homepage Laravel
         │
         ▼
2. Controller memanggil RecommendationService
         │
         ▼
3. Cek Redis: ada cache? ──YES──► return cache ke view
         │ NO
         ▼
4. HTTP POST ke Python /api/recommend
         │
         ▼
5. Python cek Redis → load model → generate rekomendasi
         │
         ▼
6. Python simpan hasil ke Redis, return product_ids
         │
         ▼
7. Laravel ambil data produk dari MySQL berdasarkan product_ids
         │
         ▼
8. Tampilkan ke user di section "Rekomendasi Untuk Kamu"
```

---

## Struktur Database (ERD Sederhana)

```
users ─────────────────────────┐
  │                            │
  ├── orders ─── order_items ──┤── products ── product_images
  │                            │        │
  ├── carts ──── cart_items ───┘        ├── categories
  │                                     │
  ├── reviews ──────────────────────────┘
  │
  ├── search_histories
  ├── view_histories
  └── recommendations
```

---

## Keputusan Arsitektur

| Keputusan | Alasan |
|-----------|--------|
| Pisahkan Python sebagai microservice | Isolasi kode ML, bisa scale independen |
| Redis untuk cache rekomendasi | Rekomendasi mahal (komputasi ML), perlu cache |
| Repository Pattern | Abstraksi query DB, mudah ditest dan diganti |
| Service Layer | Logika bisnis tidak bocor ke controller |
| Docker Compose | Memudahkan setup dan deployment semua layanan |
