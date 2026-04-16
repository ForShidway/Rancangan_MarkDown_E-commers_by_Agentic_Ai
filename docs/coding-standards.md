# Standar Penulisan Kode — SmartShop E-Commerce

---

## Prinsip Umum

1. **Kode harus mudah dibaca** — tulis kode untuk manusia, bukan hanya untuk mesin
2. **DRY (Don't Repeat Yourself)** — buat helper/trait/service jika ada kode yang diulang
3. **KISS (Keep It Simple, Stupid)** — jangan over-engineer solusi sederhana
4. **Satu fungsi, satu tanggung jawab** — setiap method hanya melakukan satu hal

---

## PHP / Laravel

### Naming Convention

```php
// Controller — PascalCase + Controller
class ProductController extends Controller {}

// Service — PascalCase + Service
class RecommendationService {}

// Repository — PascalCase + Repository
class ProductRepository {}

// Model — PascalCase, singular
class Product extends Model {}
class OrderItem extends Model {}

// Method — camelCase
public function getActiveProducts() {}
public function createOrderFromCart() {}

// Variable — camelCase
$productList = [];
$totalPrice = 0;

// Constant — SCREAMING_SNAKE_CASE
const MAX_CART_ITEMS = 20;

// Route name — lowercase titik
Route::get('/products', [...])->name('products.index');
Route::get('/admin/products', [...])->name('admin.products.index');
```

### Struktur Controller

```php
// ✅ BENAR: Controller hanya terima & kirim, logika di Service
public function store(StoreProductRequest $request)
{
    $product = $this->productService->create($request->validated());
    return redirect()->route('admin.products.index')
                     ->with('success', 'Produk berhasil ditambahkan!');
}

// ❌ SALAH: Logika langsung di Controller
public function store(Request $request)
{
    $slug = Str::slug($request->name);
    $product = Product::create([...]);
    // Ini seharusnya di Service!
}
```

### Validasi Wajib dengan Form Request

```bash
php artisan make:request Admin/StoreProductRequest
```

```php
// app/Http/Requests/Admin/StoreProductRequest.php
public function rules(): array
{
    return [
        'name'        => ['required', 'string', 'max:255'],
        'price'       => ['required', 'numeric', 'min:0'],
        'stock'       => ['required', 'integer', 'min:0'],
        'category_id' => ['required', 'exists:categories,id'],
        'image'       => ['nullable', 'image', 'max:2048'],
        'description' => ['required', 'string'],
    ];
}

public function messages(): array
{
    return [
        'name.required'        => 'Nama produk wajib diisi.',
        'price.required'       => 'Harga wajib diisi.',
        'category_id.required' => 'Kategori wajib dipilih.',
    ];
}
```

### Eloquent & Query

```php
// ✅ Gunakan eager loading untuk hindari N+1
$products = Product::with(['category', 'images'])->paginate(12);

// ✅ Gunakan scope untuk query yang sering dipakai
// Di Model:
public function scopeActive($query)
{
    return $query->where('is_active', true)->where('stock', '>', 0);
}
// Di Repository:
return Product::active()->latest()->paginate(12);

// ❌ Hindari query di loop
foreach ($orders as $order) {
    $order->user->name; // N+1 query!
}
```

### Database Transaction

```php
// Selalu gunakan transaction untuk operasi yang melibatkan >1 tabel
DB::transaction(function () use ($userId, $cartItems) {
    $order = $this->orderRepository->create([...]);
    foreach ($cartItems as $item) {
        $this->orderItemRepository->create([...]);
        $this->productRepository->decrementStock($item->product_id, $item->qty);
    }
    $this->cartRepository->clearCart($userId);
});
```

---

## Blade Template

```blade
{{-- ✅ Gunakan @isset atau null coalescing untuk data opsional --}}
{{ $product->discount_price ?? $product->price }}

{{-- ✅ Tampilkan flash message dengan konsisten --}}
@if (session('success'))
    <div class="alert alert-success">{{ session('success') }}</div>
@endif

{{-- ✅ Buat komponen untuk elemen yang berulang --}}
<x-product-card :product="$product" />
```

---

## Python (FastAPI)

```python
# ✅ Selalu gunakan Pydantic untuk validasi
class RecommendRequest(BaseModel):
    user_id: int
    limit: int = Field(default=10, ge=1, le=50)

# ✅ Gunakan type hints
def get_recommendations(user_id: int, limit: int = 10) -> list[int]:
    ...

# ✅ Handle exception dengan try/except
try:
    result = model.recommend(user_id)
except Exception as e:
    logger.error(f"Recommendation failed for user {user_id}: {e}")
    return fallback_products()
```

---

## Komentar Kode

```php
// ✅ Komentar untuk menjelaskan KENAPA, bukan APA
// Gunakan null sebagai fallback karena Redis belum pasti tersedia saat startup
$cached = Cache::get($cacheKey, null);

// ❌ Komentar berlebihan yang menjelaskan kode itu sendiri
// Menambahkan 1 ke count
$count = $count + 1;
```

---

## Komit Git

Format pesan komit:
```
<type>: <deskripsi singkat dalam bahasa Indonesia>

Contoh:
feat: tambah fitur rekomendasi produk di homepage
fix: perbaiki bug stok tidak berkurang saat checkout
refactor: pindahkan logika checkout ke CheckoutService
docs: update dokumentasi arsitektur sistem
```

Tipe komit: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`
