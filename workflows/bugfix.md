---
description: Alur kerja perbaikan bug di proyek SmartShop
---

# Workflow: Perbaikan Bug (Bugfix)

Gunakan workflow ini setiap kali menemukan bug atau error di proyek SmartShop.

---

## Langkah-langkah

### 1. Identifikasi Bug

Kumpulkan informasi lengkap tentang bug:
- **Apa** yang terjadi? (Deskripsi error)
- **Di mana** terjadinya? (URL, halaman, endpoint)
- **Kapan** terjadi? (Aksi apa yang memicu)
- **Error message** apa yang muncul? (Log, console)

---

### 2. Cek Log Error

```bash
# Log Laravel
tail -f storage/logs/laravel.log

# Log Docker container
docker-compose logs app --follow
docker-compose logs python --follow

# Log MySQL query (aktifkan query log di .env)
DB_LOG_QUERIES=true
```

---

### 3. Reproduksi Bug

Sebelum memperbaiki, pastikan bisa **mereproduksi bug secara konsisten**:
- Catat langkah-langkah untuk memicu bug
- Cek apakah terjadi di semua user atau hanya tertentu
- Cek apakah data-dependent (hanya terjadi untuk data tertentu)

---

### 4. Analisis Root Cause

Telusuri dari:
1. **Controller** — apakah request diterima dengan benar?
2. **Service** — apakah logika bisnis benar?
3. **Repository** — apakah query menghasilkan data yang tepat?
4. **Model** — apakah relasi atau accessor bermasalah?
5. **View** — apakah variabel dikirim dengan benar ke template?

**Tools yang berguna:**
```php
// Dump dan tampilkan variabel
dd($variable);          // Laravel dump & die
dump($variable);        // Dump tanpa stop
Log::info($variable);   // Log ke storage/logs/laravel.log
```

---

### 5. Perbaiki Bug

- Perbaiki **hanya bagian yang bermasalah**, jangan terlalu banyak berubah
- Ikuti arsitektur: Controller → Service → Repository → Model
- Tambahkan komentar singkat jika perbaikannya tidak obvious

```php
// Fix: Cek apakah user sudah memiliki pesanan aktif sebelum checkout
// Bug: User bisa checkout beberapa kali untuk cart yang sama
if ($this->orderRepository->hasActiveOrder($userId)) {
    throw new \Exception('Kamu sudah memiliki pesanan yang sedang diproses.');
}
```

---

### 6. Uji Perbaikan

- Uji skenario yang menyebabkan bug dulu
- Uji skenario normal (happy path)
- Uji edge case (data kosong, data besar, dll)

---

### 7. Cek Side Effect

Pastikan perbaikan tidak merusak fitur lain:
- Jalankan test yang ada (jika tersedia)
- Uji manual fitur yang terkait

```bash
php artisan test
```

---

### 8. (Opsional) Tambahkan Test

Jika bug ini tidak ada testnya, tambahkan test agar tidak terulang:

```bash
php artisan make:test {NamaFitur}Test --unit
```

---

### 9. Catat di Decisions.md

Jika bug ini disebabkan oleh keputusan arsitektur yang kurang tepat, catat di `docs/decisions.md` agar tidak terulang.

---

## Checklist

- [ ] Bug berhasil diidentifikasi dan direproduksi
- [ ] Log error sudah dicek
- [ ] Root cause ditemukan
- [ ] Perbaikan dilakukan dengan perubahan minimal
- [ ] Uji skenario bug sudah sukses
- [ ] Uji regresi tidak ada yang rusak
- [ ] (Opsional) Test ditambahkan
- [ ] (Opsional) Dicatat di `decisions.md`

---

## Bugs Umum di Proyek Ini

| Bug | Penyebab Umum | Solusi |
|-----|---------------|--------|
| Koneksi gagal ke Python API | Container Python belum ready | Restart `python` container |
| Cache Redis tidak ter-invalidasi | Lupa `Cache::forget()` | Tambahkan di service setelah update data |
| Stok produk tidak berkurang | Tidak ada transaction di checkout | Bungkus dengan `DB::transaction()` |
| Gambar produk 404 | Storage link belum dibuat | Jalankan `php artisan storage:link` |
| Rekomendasi selalu kosong | Model ML belum dilatih | Jalankan endpoint `/train` di Python |
