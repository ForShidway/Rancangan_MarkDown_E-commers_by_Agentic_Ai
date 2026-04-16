# Spesifikasi Produk — SmartShop E-Commerce

---

## Ringkasan Produk

**SmartShop** adalah platform e-commerce berbasis web yang menyediakan pengalaman belanja personal dengan sistem rekomendasi produk otomatis berbasis kecerdasan buatan.

---

## Target Pengguna

| Pengguna | Kebutuhan Utama |
|----------|-----------------|
| Pembeli (User) | Belanja online mudah, mendapatkan rekomendasi relevan |
| Pengelola Toko (Admin) | Kelola produk, pantau pesanan, lihat laporan |

---

## Fitur Produk

### Fitur Wajib (Must Have)

#### Autentikasi
- Registrasi akun baru
- Login dengan email dan password
- Logout
- Lupa password & reset password
- Edit profil (nama, foto, alamat)

#### Katalog Produk
- Tampilan daftar produk dengan gambar, nama, harga
- Filter produk berdasarkan kategori
- Filter berdasarkan rentang harga
- Pencarian produk dengan kata kunci
- Halaman detail produk (foto, deskripsi, harga, stok, rating)

#### Keranjang & Checkout
- Tambah/hapus/ubah jumlah item di keranjang
- Lihat total belanja
- Form pengisian alamat pengiriman
- Konfirmasi order
- Riwayat pesanan

#### Panel Admin
- Dashboard statistik (total produk, user, order, pendapatan)
- CRUD Kategori
- CRUD Produk (dengan upload gambar)
- Manajemen Order (ubah status)
- Manajemen Pengguna

#### Sistem Rekomendasi
- Tampil section "Rekomendasi Untuk Kamu" di homepage
- Tampil section "Produk Serupa" di halaman detail produk
- Rekomendasi berdasarkan riwayat beli, cari, dan lihat produk

### Fitur Tambahan (Nice to Have)

- Rating dan ulasan produk (hanya untuk produk yang sudah dibeli)
- Wishlist/favorit produk
- Notifikasi pesanan via email
- Grafik penjualan di dashboard admin
- Badge promo/diskon pada produk

---

## User Stories

### Sebagai User (Pembeli)

| ID | User Story | Prioritas |
|----|-----------|-----------|
| US-01 | Sebagai user, saya ingin mendaftar akun baru agar bisa berbelanja | Tinggi |
| US-02 | Sebagai user, saya ingin login ke akun saya | Tinggi |
| US-03 | Sebagai user, saya ingin melihat daftar produk | Tinggi |
| US-04 | Sebagai user, saya ingin mencari produk dengan kata kunci | Tinggi |
| US-05 | Sebagai user, saya ingin melihat detail produk | Tinggi |
| US-06 | Sebagai user, saya ingin menambahkan produk ke keranjang | Tinggi |
| US-07 | Sebagai user, saya ingin melakukan checkout dan pembelian | Tinggi |
| US-08 | Sebagai user, saya ingin melihat riwayat pesanan saya | Tinggi |
| US-09 | Sebagai user, saya ingin mendapat rekomendasi produk personal | Menengah |
| US-10 | Sebagai user, saya ingin memberi rating dan ulasan produk | Rendah |

### Sebagai Admin

| ID | User Story | Prioritas |
|----|-----------|-----------|
| UA-01 | Sebagai admin, saya ingin menambahkan produk baru | Tinggi |
| UA-02 | Sebagai admin, saya ingin mengedit dan menghapus produk | Tinggi |
| UA-03 | Sebagai admin, saya ingin mengelola kategori produk | Tinggi |
| UA-04 | Sebagai admin, saya ingin melihat dan mengubah status pesanan | Tinggi |
| UA-05 | Sebagai admin, saya ingin melihat statistik di dashboard | Menengah |
| UA-06 | Sebagai admin, saya ingin mengelola akun pengguna | Menengah |

---

## Batasan Produk (Constraints)

- Tidak menyediakan sistem pembayaran nyata (hanya simulasi)
- Pengiriman tidak terintegrasi dengan layanan ekspedisi nyata
- Rekomendasi memerlukan data historis minimum untuk akurat
- Hanya tersedia dalam Bahasa Indonesia
