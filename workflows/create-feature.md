---
description: Cara membuat fitur baru (non-CRUD) di proyek SmartShop
---

# Workflow: Membuat Fitur Baru

Gunakan workflow ini untuk membuat fitur yang lebih kompleks dari sekedar CRUD, seperti checkout, sistem rekomendasi, atau integrasi eksternal.

---

## Langkah-langkah

### 1. Analisis Kebutuhan

Sebelum mulai coding, jawab pertanyaan berikut:
- Siapa yang mengakses fitur ini? (User / Admin / Keduanya)
- Data apa yang dibutuhkan? (Tabel mana yang terlibat?)
- Apakah ada integrasi eksternal? (Python API, Redis, dll)
- Apakah memerlukan queue/job?

---

### 2. Rancang Alur Data

Tulis pseudocode atau diagram alur:

```
User klik "Checkout"
  → CartController@checkout
    → CheckoutService@process($user, $cart)
      → validasi stok (ProductRepository)
      → buat Order + OrderItems (OrderRepository)
      → kurangi stok (ProductRepository@decrementStock)
      → kosongkan Cart (CartRepository@clearCart)
      → kirim notifikasi email (Queue)
    ← return Order
  ← redirect ke halaman konfirmasi
```

---

### 3. Buat Migration (jika tabel baru)

```bash
php artisan make:migration add_{kolom}_to_{tabel}_table
# atau
php artisan make:migration create_{tabel}_table
```

---

### 4. Buat / Update Model

Pastikan relasi antar model sudah benar.

---

### 5. Buat Repository

Tambahkan method spesifik yang dibutuhkan fitur ini ke repository yang ada, atau buat repository baru.

---

### 6. Buat Service

Service adalah tempat utama logika bisnis. Jika fitur ini:
- Melibatkan banyak model → buat service baru
- Hanya tambahan logika pada fitur yang ada → tambahkan method di service yang ada

```bash
# Contoh membuat service baru
# Buat file: app/Services/CheckoutService.php
```

---

### 7. Buat Controller

```bash
php artisan make:controller {NamaFitur}Controller
```

Untuk fitur yang tidak butuh semua method CRUD, jangan gunakan `--resource`. Buat method spesifik saja.

---

### 8. Buat Form Request (jika ada input user)

```bash
php artisan make:request {NamaFitur}Request
```

---

### 9. (Opsional) Buat Job/Queue

Jika fitur ini memerlukan proses asinkron (email, notifikasi, retraing model ML):

```bash
php artisan make:job Process{NamaFitur}Job
```

---

### 10. Daftarkan Route

```php
// Di routes/web.php
Route::middleware(['auth'])->group(function () {
    Route::post('/checkout', [CheckoutController::class, 'process'])->name('checkout.process');
    Route::get('/checkout/success/{order}', [CheckoutController::class, 'success'])->name('checkout.success');
});
```

---

### 11. Buat View Blade

Buat view yang sesuai di folder `resources/views/`.

---

### 12. Uji Fitur

- Test semua skenario: sukses, gagal, edge case
- Pastikan validasi bekerja
- Gunakan `php artisan tinker` untuk test cepat jika perlu

---

## Checklist

- [ ] Kebutuhan fitur sudah dianalisis
- [ ] Alur data sudah dirancang
- [ ] Migration dibuat (jika perlu)
- [ ] Model diperbarui
- [ ] Repository method ditambahkan
- [ ] Service dibuat dengan logika bisnis
- [ ] Controller dibuat
- [ ] Form Request dibuat
- [ ] Job/Queue dibuat (jika perlu)
- [ ] Route terdaftar
- [ ] View Blade dibuat
- [ ] Uji manual berhasil
