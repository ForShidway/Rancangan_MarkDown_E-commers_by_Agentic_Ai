---
description: Setup Python Recommendation Engine dan integrasinya dengan Laravel via Redis
---

# Workflow: Setup Python Recommendation Engine

Gunakan workflow ini untuk menyiapkan engine rekomendasi produk berbasis Python dan mengintegrasikannya dengan Laravel.

---

## Prasyarat

- Docker sudah berjalan (lihat `setup-docker.md`)
- Container `python` dan `redis` sudah aktif

---

## Struktur Folder Python

```
python/
├── app/
│   ├── main.py              # Entry point FastAPI
│   ├── routes/
│   │   └── recommendation.py
│   ├── services/
│   │   └── recommender.py   # Logika ML
│   └── schemas/
│       └── recommendation.py # Pydantic schemas
├── models/                  # Saved ML models (.pkl)
├── Dockerfile
└── requirements.txt
```

---

## Langkah-langkah

### 1. Siapkan `requirements.txt`

```txt
fastapi==0.110.0
uvicorn==0.29.0
scikit-learn==1.4.0
pandas==2.2.0
numpy==1.26.0
redis==5.0.0
pydantic==2.6.0
httpx==0.27.0
```

---

### 2. Buat Dockerfile untuk Python

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8001", "--reload"]
```

---

### 3. Buat Entry Point `app/main.py`

```python
from fastapi import FastAPI
from app.routes import recommendation

app = FastAPI(title="SmartShop Recommendation Engine", version="1.0.0")

app.include_router(recommendation.router, prefix="/api", tags=["Recommendation"])

@app.get("/health")
def health_check():
    return {"status": "ok", "service": "recommendation-engine"}
```

---

### 4. Buat Schema `app/schemas/recommendation.py`

```python
from pydantic import BaseModel
from typing import List

class RecommendRequest(BaseModel):
    user_id: int
    limit: int = 10

class RecommendResponse(BaseModel):
    user_id: int
    product_ids: List[int]
    source: str  # "ml_model" atau "fallback"
```

---

### 5. Buat Service ML `app/services/recommender.py`

```python
import redis
import json
import pickle
import numpy as np

redis_client = redis.Redis(host='redis', port=6379, decode_responses=True)
CACHE_TTL = 3600  # 60 menit

def get_recommendations(user_id: int, limit: int = 10) -> dict:
    # Cek cache Redis dulu
    cache_key = f"recommend:user:{user_id}"
    cached = redis_client.get(cache_key)
    
    if cached:
        return {"product_ids": json.loads(cached), "source": "cache"}
    
    # Jalankan model ML
    try:
        with open("models/collaborative_filter.pkl", "rb") as f:
            model = pickle.load(f)
        
        product_ids = model.recommend(user_id, n=limit)
        
        # Simpan ke cache Redis
        redis_client.setex(cache_key, CACHE_TTL, json.dumps(product_ids))
        
        return {"product_ids": product_ids, "source": "ml_model"}
    
    except Exception:
        # Fallback ke produk populer
        popular = get_popular_products(limit)
        return {"product_ids": popular, "source": "fallback"}

def get_popular_products(limit: int = 10) -> list:
    # Ambil dari Redis jika ada
    cached = redis_client.get("popular_products")
    if cached:
        return json.loads(cached)[:limit]
    return []
```

---

### 6. Buat Route `app/routes/recommendation.py`

```python
from fastapi import APIRouter
from app.schemas.recommendation import RecommendRequest, RecommendResponse
from app.services.recommender import get_recommendations

router = APIRouter()

@router.post("/recommend", response_model=RecommendResponse)
def recommend(request: RecommendRequest):
    result = get_recommendations(request.user_id, request.limit)
    return RecommendResponse(
        user_id=request.user_id,
        product_ids=result["product_ids"],
        source=result["source"]
    )
```

---

### 7. Integrasi di Laravel — `RecommendationService`

Buat file `app/Services/RecommendationService.php`:

```php
<?php

namespace App\Services;

use Illuminate\Support\Facades\Http;
use Illuminate\Support\Facades\Cache;

class RecommendationService
{
    private string $apiUrl;

    public function __construct()
    {
        $this->apiUrl = config('services.recommendation.url', env('RECOMMENDATION_API_URL'));
    }

    public function getRecommendations(int $userId, int $limit = 10): array
    {
        $cacheKey = "recommendations.user.{$userId}";

        return Cache::remember($cacheKey, 3600, function () use ($userId, $limit) {
            try {
                $response = Http::timeout(5)->post("{$this->apiUrl}/api/recommend", [
                    'user_id' => $userId,
                    'limit'   => $limit,
                ]);

                if ($response->successful()) {
                    return $response->json('product_ids', []);
                }

                return $this->getFallbackProductIds();

            } catch (\Exception $e) {
                return $this->getFallbackProductIds();
            }
        });
    }

    private function getFallbackProductIds(): array
    {
        // Ambil produk terlaris sebagai fallback
        return \App\Models\Product::orderByDesc('sold_count')
            ->limit(10)
            ->pluck('id')
            ->toArray();
    }
}
```

---

### 8. Uji Endpoint Python

```bash
# Tes health check
curl http://localhost:8001/health

# Tes rekomendasi
curl -X POST http://localhost:8001/api/recommend \
  -H "Content-Type: application/json" \
  -d '{"user_id": 1, "limit": 5}'
```

---

### 9. Invalidasi Cache Saat Ada Pembelian Baru

Di `OrderService.php`, setelah order berhasil dibuat:

```php
// Hapus cache rekomendasi agar diperbarui
Cache::forget("recommendations.user.{$userId}");
```

---

## Checklist

- [ ] `requirements.txt` lengkap
- [ ] `Dockerfile` Python dibuat
- [ ] Entry point FastAPI berjalan
- [ ] Schema Pydantic didefinisikan
- [ ] Service ML dengan cache Redis
- [ ] Route `/recommend` aktif
- [ ] `RecommendationService.php` di Laravel dibuat
- [ ] Uji endpoint berhasil
- [ ] Invalidasi cache berfungsi
