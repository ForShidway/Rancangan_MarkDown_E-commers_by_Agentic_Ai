---
description: Cara membuat fitur CRUD baru di proyek SmartShop
---

# Workflow: Membuat Fitur CRUD Baru

Gunakan workflow ini setiap kali kamu ingin membuat fitur CRUD baru (misal: kategori, produk, brand, dsb).

---

## Langkah-langkah

### 1. Buat Migration

Buat file migration untuk tabel baru:

```bash
php artisan make:migration create_{nama_tabel}_table
```

Isi kolom yang dibutuhkan di dalam migration, lalu jalankan:

```bash
php artisan migrate
```

---

### 2. Buat Model

```bash
php artisan make:model {NamaModel}
```

Isi `$fillable`, relasi, dan accessor jika diperlukan.

**Contoh:**
```php
class Category extends Model
{
    protected $fillable = ['name', 'slug', 'description', 'is_active'];

    public function products(): HasMany
    {
        return $this->hasMany(Product::class);
    }
}
```

---

### 3. Buat Repository

Buat file di `app/Repositories/{NamaModel}Repository.php`:

```php
<?php

namespace App\Repositories;

use App\Models\Category;

class CategoryRepository
{
    public function getAll()
    {
        return Category::latest()->paginate(10);
    }

    public function findById(int $id): Category
    {
        return Category::findOrFail($id);
    }

    public function create(array $data): Category
    {
        return Category::create($data);
    }

    public function update(Category $category, array $data): bool
    {
        return $category->update($data);
    }

    public function delete(Category $category): bool
    {
        return $category->delete();
    }
}
```

---

### 4. Buat Service

Buat file di `app/Services/{NamaModel}Service.php`:

```php
<?php

namespace App\Services;

use App\Repositories\CategoryRepository;

class CategoryService
{
    public function __construct(
        private CategoryRepository $repository
    ) {}

    public function getAll()
    {
        return $this->repository->getAll();
    }

    public function create(array $data)
    {
        // Tambahkan logika bisnis di sini jika perlu
        $data['slug'] = Str::slug($data['name']);
        return $this->repository->create($data);
    }

    public function update(int $id, array $data)
    {
        $category = $this->repository->findById($id);
        $data['slug'] = Str::slug($data['name']);
        $this->repository->update($category, $data);
        return $category;
    }

    public function delete(int $id): void
    {
        $category = $this->repository->findById($id);
        $this->repository->delete($category);
    }
}
```

---

### 5. Buat Form Request Validation

```bash
php artisan make:request Admin/Store{NamaModel}Request
php artisan make:request Admin/Update{NamaModel}Request
```

Isi aturan validasi di method `rules()`.

---

### 6. Buat Resource Controller

```bash
php artisan make:controller Admin/{NamaModel}Controller --resource
```

Inject `{NamaModel}Service` di constructor:

```php
public function __construct(
    private CategoryService $service
) {}
```

Isi method: `index`, `create`, `store`, `show`, `edit`, `update`, `destroy`.

---

### 7. Daftarkan Route

Buka `routes/web.php` dan tambahkan:

```php
// Khusus Admin
Route::middleware(['auth', 'admin'])->prefix('admin')->name('admin.')->group(function () {
    Route::resource('categories', Admin\CategoryController::class);
});
```

---

### 8. Buat View Blade

Buat folder `resources/views/admin/{nama_fitur}/`:
- `index.blade.php` — daftar data
- `create.blade.php` — form tambah
- `edit.blade.php` — form edit

---

### 9. Uji Fitur

- Buka `http://localhost:8000/admin/{nama_fitur}`
- Test tambah, edit, hapus data
- Pastikan validasi berjalan dengan benar

---

### 10. (Opsional) Buat API Resource

Jika endpoint ini dibutuhkan oleh Python atau frontend lain:

```bash
php artisan make:resource {NamaModel}Resource
```

---

## Checklist

- [ ] Migration dibuat dan dijalankan
- [ ] Model dengan `$fillable` dan relasi
- [ ] Repository dengan semua method CRUD
- [ ] Service dengan logika bisnis
- [ ] Form Request Validation (Store & Update)
- [ ] Resource Controller
- [ ] Route terdaftar dengan middleware yang benar
- [ ] View Blade (index, create, edit)
- [ ] Uji manual berhasil
