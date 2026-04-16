---
description: Setup Docker environment untuk proyek SmartShop (Laravel, MySQL, Redis, Python)
---

# Workflow: Setup Docker Environment

Gunakan workflow ini untuk menjalankan semua layanan SmartShop di Docker secara lokal maupun di cloud.

---

## Prasyarat

- Docker Desktop sudah terinstall
- Docker Compose sudah tersedia
- Port berikut tidak digunakan: `8000`, `8001`, `3306`, `6379`

---

## Langkah-langkah

### 1. Pastikan File `docker-compose.yml` Ada

File ini harus ada di root proyek. Isi contohnya:

```yaml
version: '3.8'

services:
  # Laravel App
  app:
    build:
      context: .
      dockerfile: docker/php/Dockerfile
    container_name: smartshop_app
    ports:
      - "8000:80"
    volumes:
      - .:/var/www/html
    depends_on:
      - mysql
      - redis
    networks:
      - smartshop_network

  # MySQL Database
  mysql:
    image: mysql:8.0
    container_name: smartshop_mysql
    environment:
      MYSQL_DATABASE: smartshop
      MYSQL_ROOT_PASSWORD: secret
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql
    networks:
      - smartshop_network

  # Redis
  redis:
    image: redis:7-alpine
    container_name: smartshop_redis
    ports:
      - "6379:6379"
    networks:
      - smartshop_network

  # Python Recommendation Engine
  python:
    build:
      context: ./python
      dockerfile: Dockerfile
    container_name: smartshop_python
    ports:
      - "8001:8001"
    depends_on:
      - redis
    networks:
      - smartshop_network

networks:
  smartshop_network:
    driver: bridge

volumes:
  mysql_data:
```

---

### 2. Build dan Jalankan Semua Container

```bash
docker-compose up -d --build
```

Flag `-d` = berjalan di background (detached mode)
Flag `--build` = rebuild image jika ada perubahan

---

### 3. Verifikasi Container Berjalan

```bash
docker-compose ps
```

Semua container harus berstatus `Up`.

---

### 4. Jalankan Setup Laravel (Pertama Kali)

```bash
# Masuk ke container Laravel
docker-compose exec app bash

# Install dependencies
composer install

# Copy .env
cp .env.example .env

# Generate application key
php artisan key:generate

# Jalankan migration + seeder
php artisan migrate --seed

# Set permission storage
chmod -R 775 storage bootstrap/cache
```

---

### 5. Akses Aplikasi

| Layanan                    | URL                          |
|----------------------------|------------------------------|
| Laravel App                | http://localhost:8000         |
| Python Recommendation API  | http://localhost:8001         |
| MySQL                      | localhost:3306                |
| Redis                      | localhost:6379                |

---

### 6. Hentikan Semua Container

```bash
docker-compose down
```

Untuk menghapus volume (data database) sekaligus:

```bash
docker-compose down -v
```

---

### 7. Cek Log Jika Ada Error

```bash
# Log semua container
docker-compose logs

# Log container tertentu
docker-compose logs app
docker-compose logs python
docker-compose logs mysql
```

---

## Troubleshooting Umum

| Masalah | Solusi |
|---------|--------|
| Port sudah digunakan | Ganti port di `docker-compose.yml` |
| Container tidak bisa konek ke MySQL | Tunggu beberapa detik, MySQL butuh waktu startup |
| Permission denied di storage | Jalankan `chmod -R 775 storage` di dalam container |
| Python tidak bisa konek ke Redis | Pastikan `REDIS_HOST=redis` di `.env` Python |
