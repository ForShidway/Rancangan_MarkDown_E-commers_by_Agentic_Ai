# Daftar Endpoint API — SmartShop

Dokumentasi semua endpoint API yang tersedia, termasuk API internal antara Laravel dan Python.

---

## Laravel Web Routes (Browser)

### Auth (Laravel Breeze)

| Method | URL | Nama Route | Keterangan |
|--------|-----|------------|------------|
| GET | `/register` | `register` | Halaman registrasi |
| POST | `/register` | - | Proses registrasi |
| GET | `/login` | `login` | Halaman login |
| POST | `/login` | - | Proses login |
| POST | `/logout` | `logout` | Proses logout |

### User Routes

| Method | URL | Nama Route | Keterangan |
|--------|-----|------------|------------|
| GET | `/` | `home` | Halaman utama + rekomendasi |
| GET | `/products` | `products.index` | Daftar semua produk |
| GET | `/products/{slug}` | `products.show` | Detail produk |
| GET | `/categories/{slug}` | `categories.show` | Produk per kategori |
| GET | `/search` | `products.search` | Pencarian produk |
| GET | `/cart` | `cart.index` | Keranjang belanja |
| POST | `/cart` | `cart.add` | Tambah item ke cart |
| PATCH | `/cart/{id}` | `cart.update` | Update qty di cart |
| DELETE | `/cart/{id}` | `cart.remove` | Hapus item dari cart |
| GET | `/checkout` | `checkout.index` | Halaman checkout |
| POST | `/checkout` | `checkout.process` | Proses checkout |
| GET | `/orders` | `orders.index` | Riwayat pesanan user |
| GET | `/orders/{id}` | `orders.show` | Detail pesanan |
| POST | `/reviews` | `reviews.store` | Kirim ulasan |
| GET | `/profile` | `profile.edit` | Edit profil |
| PATCH | `/profile` | `profile.update` | Update profil |

### Admin Routes (Prefix: `/admin`, Middleware: `auth`, `admin`)

| Method | URL | Nama Route | Keterangan |
|--------|-----|------------|------------|
| GET | `/admin/dashboard` | `admin.dashboard` | Dashboard admin |
| GET | `/admin/products` | `admin.products.index` | Daftar produk |
| GET | `/admin/products/create` | `admin.products.create` | Form tambah produk |
| POST | `/admin/products` | `admin.products.store` | Simpan produk baru |
| GET | `/admin/products/{id}/edit` | `admin.products.edit` | Form edit produk |
| PATCH | `/admin/products/{id}` | `admin.products.update` | Update produk |
| DELETE | `/admin/products/{id}` | `admin.products.destroy` | Hapus produk |
| GET | `/admin/categories` | `admin.categories.index` | Daftar kategori |
| POST | `/admin/categories` | `admin.categories.store` | Simpan kategori baru |
| PATCH | `/admin/categories/{id}` | `admin.categories.update` | Update kategori |
| DELETE | `/admin/categories/{id}` | `admin.categories.destroy` | Hapus kategori |
| GET | `/admin/orders` | `admin.orders.index` | Semua pesanan |
| GET | `/admin/orders/{id}` | `admin.orders.show` | Detail pesanan |
| PATCH | `/admin/orders/{id}/status` | `admin.orders.status` | Update status |
| GET | `/admin/users` | `admin.users.index` | Daftar pengguna |
| PATCH | `/admin/users/{id}/toggle` | `admin.users.toggle` | Aktif/nonaktif user |

---

## Laravel API Routes (`/api/`, Middleware: `auth:sanctum`)

| Method | URL | Keterangan |
|--------|-----|------------|
| GET | `/api/recommendations` | Ambil rekomendasi produk (user login) |
| POST | `/api/track/view` | Rekam produk yang dilihat |
| POST | `/api/track/search` | Rekam kata kunci pencarian |

---

## Python FastAPI Endpoints (Internal, Port 8001)

> Endpoint ini hanya diakses oleh Laravel, bukan browser pengguna langsung.

| Method | URL | Request Body | Response | Keterangan |
|--------|-----|--------------|----------|------------|
| GET | `/health` | - | `{"status": "ok"}` | Health check |
| POST | `/api/recommend` | `{"user_id": int, "limit": int}` | `{"product_ids": [1,2,3], "source": "ml_model"}` | Dapatkan rekomendasi |
| POST | `/api/train` | `{"model_type": "collaborative"}` | `{"message": "Training started"}` | Latih ulang model ML |
| GET | `/api/popular` | - | `{"product_ids": [...]}` | Produk paling populer |

---

## Contoh Request & Response

### POST `/api/recommend` (Python API)

**Request:**
```json
{
  "user_id": 42,
  "limit": 8
}
```

**Response (sukses):**
```json
{
  "user_id": 42,
  "product_ids": [15, 23, 7, 31, 44, 12, 8, 19],
  "source": "ml_model"
}
```

**Response (fallback — model belum siap):**
```json
{
  "user_id": 42,
  "product_ids": [1, 2, 3, 4, 5, 6, 7, 8],
  "source": "fallback"
}
```

### POST `/api/track/view` (Laravel API)

**Request:**
```json
{
  "product_id": 15
}
```

**Response:**
```json
{
  "message": "View recorded"
}
```
