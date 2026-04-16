# Catatan Keputusan Teknis — SmartShop E-Commerce

Dokumen ini mencatat setiap keputusan arsitektur atau teknis penting beserta alasannya. Tujuannya agar tim selalu ingat MENGAPA sesuatu dibuat dengan cara tertentu.

---

## Keputusan #001 — Gunakan Python Terpisah untuk Rekomendasi

**Tanggal:** Awal Proyek  
**Status:** ✅ Diputuskan

**Konteks:**  
Sistem rekomendasi memerlukan library machine learning (scikit-learn, numpy, pandas) yang tidak efisien jika diintegrasikan langsung ke Laravel.

**Keputusan:**  
Buat layanan Python terpisah menggunakan FastAPI yang berkomunikasi dengan Laravel via HTTP internal Docker.

**Alasan:**
- PHP tidak punya ekosistem ML sebaik Python
- Layanan Python bisa di-scale independen jika kebutuhan komputasi meningkat
- Pemisahan concerns — tim bisa bekerja di dua codebase secara paralel

**Trade-off:**
- Menambah kompleksitas deployment (dua container + network)
- Latency tambahan (1 HTTP call ke Python)

---

## Keputusan #002 — Gunakan Redis untuk Cache Rekomendasi

**Tanggal:** Awal Proyek  
**Status:** ✅ Diputuskan

**Konteks:**  
Setiap request rekomendasi harus memanggil Python API yang menjalankan model ML — ini mahal secara komputasi.

**Keputusan:**  
Cache hasil rekomendasi di Redis dengan TTL 60 menit.

**Alasan:**
- Mengurangi beban komputasi Python API
- Respons lebih cepat untuk user
- Rekomendasi tidak perlu diperbarui setiap detik

**Trade-off:**
- Rekomendasi bisa "basi" selama 60 menit
- Invalidasi cache secara manual diperlukan saat ada pembelian baru

---

## Keputusan #003 — Repository Pattern

**Tanggal:** Awal Proyek  
**Status:** ✅ Diputuskan

**Konteks:**  
Perlu menentukan bagaimana abstraksikan akses database.

**Keputusan:**  
Gunakan Repository Pattern dengan struktur `Controller → Service → Repository → Model`.

**Alasan:**
- Memudahkan testing (bisa mock repository)
- Logika query tidak tersebar di seluruh codebase
- Konsisten dengan standar Laravel enterprise

**Trade-off:**
- Lebih banyak file untuk dibuat
- Overhead untuk fitur CRUD sederhana

---

## Keputusan #004 — Gunakan Laravel Breeze (bukan Jetstream)

**Tanggal:** Awal Proyek  
**Status:** ✅ Diputuskan

**Konteks:**  
Perlu memilih starter kit autentikasi Laravel.

**Keputusan:**  
Gunakan Laravel Breeze dengan template Blade.

**Alasan:**
- Breeze lebih ringan dan mudah dikustomisasi
- Jetstream terlalu kompleks untuk proyek ini (ada Livewire/Inertia)
- Blade sudah cukup — tidak perlu SPA (Single Page Application)

---

## Keputusan #005 — Role Admin Menggunakan Kolom di Tabel Users

**Tanggal:** Awal Proyek  
**Status:** ✅ Diputuskan

**Konteks:**  
Perlu menentukan cara implementasi role (admin vs user).

**Keputusan:**  
Tambahkan kolom `role` ENUM dengan nilai `user` dan `admin` langsung di tabel `users`.

**Alasan:**
- Hanya ada 2 role — tidak perlu sistem RBAC kompleks (seperti Spatie Permission)
- Lebih sederhana dan cukup untuk kebutuhan proyek ini

**Trade-off:**
- Jika di masa depan ada role baru (misal: `seller`), perlu migration

---

## Keputusan #006 — Fallback ke Produk Populer

**Tanggal:** Awal Proyek  
**Status:** ✅ Diputuskan

**Konteks:**  
Sistem rekomendasi ML butuh data historis yang cukup. User baru tidak punya riwayat, dan Python API bisa gagal.

**Keputusan:**  
Jika rekomendasi ML gagal atau user belum punya riwayat, tampilkan produk terlaris sebagai fallback.

**Alasan:**
- Menghindari section rekomendasi kosong
- Produk terlaris tetap relevan untuk user baru
- Graceful degradation — sistem tetap berfungsi walau ML turun

---

## Template Keputusan Baru

```
## Keputusan #XXX — [Judul]

**Tanggal:**  
**Status:** ⏳ Sedang Dipertimbangkan / ✅ Diputuskan / ❌ Dibatalkan

**Konteks:**  
[Jelaskan situasi yang memaksa keputusan ini]

**Keputusan:**  
[Apa yang diputuskan]

**Alasan:**
- [Alasan 1]
- [Alasan 2]

**Trade-off:**
- [Kerugian atau risiko]
```
