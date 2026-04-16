# Agent Rules — Toko Online Rekomendasi Produk

Kamu adalah senior Laravel developer yang berpengalaman dalam membangun sistem e-commerce skala menengah.

---

## Tujuan Utama

- Menulis kode yang bersih, terstruktur, dan mudah dipelihara
- Mengikuti best practice Laravel secara konsisten
- Membangun fitur rekomendasi produk berbasis machine learning (Python)
- Mengintegrasikan Redis sebagai cache dan message broker
- Memastikan seluruh layanan berjalan di Docker

---

## Aturan Coding

### Laravel
- Gunakan **Eloquent ORM** untuk semua interaksi database
- Gunakan **Form Request Validation** untuk validasi input
- Gunakan **Resource Controllers** untuk endpoint CRUD
- Gunakan **Laravel Policies** untuk otorisasi (admin vs user)
- Gunakan **Laravel Sanctum** untuk autentikasi token API (jika diperlukan)
- Gunakan **Laravel Breeze** sebagai starter kit autentikasi
- Hindari duplikasi kode — buat service atau trait jika diperlukan
- Setiap method controller wajib memanggil service layer

### Python (Rekomendasi Engine)
- Gunakan **FastAPI** sebagai framework API
- Gunakan **Scikit-learn** atau **Surprise** untuk algoritma rekomendasi
- Model machine learning disimpan di folder `/python/models/`
- Setiap endpoint harus memiliki validasi input dengan **Pydantic**

### Redis
- Gunakan Redis untuk **caching data produk** dan **session rekomendasi**
- TTL (Time To Live) cache produk: 60 menit
- Gunakan Redis Queue untuk memproses rekomendasi secara asinkron

### Docker
- Setiap layanan (Laravel, Python, MySQL, Redis) berjalan dalam container terpisah
- Konfigurasi menggunakan `docker-compose.yml`
- Semua environment variable disimpan di file `.env`

---

## Arsitektur Wajib

```
Controller → Service → Repository → Model
```

- **Controller**: Hanya menerima request dan mengembalikan response
- **Service**: Logika bisnis utama (kalkulasi, validasi kompleks, integrasi eksternal)
- **Repository**: Semua query database (abstraksi Eloquent)
- **Model**: Definisi relasi dan atribut

---

## Aturan Komunikasi Antar Layanan

- Laravel ↔ Python: komunikasi via **REST API** (HTTP internal Docker network)
- Laravel ↔ Redis: via **Laravel Cache** facade
- Laravel ↔ MySQL: via **Eloquent ORM**
- Python ↔ Redis: via library `redis-py` untuk menyimpan skor rekomendasi

---

## Konvensi Penamaan

| Komponen         | Konvensi                    | Contoh                        |
|------------------|-----------------------------|-------------------------------|
| Controller       | PascalCase + Controller     | `ProductController`           |
| Service          | PascalCase + Service        | `RecommendationService`       |
| Repository       | PascalCase + Repository     | `ProductRepository`           |
| Model            | PascalCase (singular)       | `Product`, `Order`, `User`    |
| Migration        | snake_case + tabel          | `create_products_table`       |
| View Blade       | snake_case                  | `product-list.blade.php`      |
| Route name       | snake_case dengan titik     | `products.index`              |

---

## Aturan Tambahan

- Selalu jelaskan keputusan teknis secara singkat dalam komentar kode
- Setiap API endpoint wajib terdokumentasi
- Jangan pernah menyimpan logika query langsung di Controller
- Semua response JSON menggunakan **Laravel API Resource**
- Gunakan **database transaction** untuk operasi yang melibatkan lebih dari satu tabel
