# 🚀 TITANPRO — Panduan Lengkap Scraping TikTok & Instagram

Panduan ini menjelaskan cara menjalankan dan menggunakan dua sistem scraping yang telah dibangun untuk tim TITANPRO:
- **[TIKTOKSCRAP](https://github.com/TlTANPRO/TIKTOKSCRAP)** — Analitik akun TikTok
- **[INSTAGRAMSCRAP](https://github.com/TlTANPRO/INSTAGRAMSCRAP)** — Analitik akun Instagram

Keduanya menggunakan **[EnsembleData API](https://ensembledata.com)** sebagai penyedia data.

---

## 📋 Daftar Isi

1. [Token EnsembleData](#-token-ensembledata)
2. [Arsitektur Sistem](#-arsitektur-sistem)
3. [Setup TIKTOKSCRAP](#-setup-tiktokscrap)
4. [Setup INSTAGRAMSCRAP](#-setup-instagramscrap)
5. [Cara Scraping TikTok](#-cara-scraping-tiktok)
6. [Cara Scraping Instagram](#-cara-scraping-instagram)
7. [API Endpoints Lengkap](#-api-endpoints-lengkap)
8. [Rotasi Token](#-rotasi-token)
9. [Troubleshooting](#-troubleshooting)

---

## 🔑 Token EnsembleData

Tim TITANPRO memiliki **14 token aktif** dari EnsembleData. Gunakan token-token ini secara bergantian (rotasi) untuk menghindari rate limiting.

| No | Token |
|----|-------|
| 1  | `zuggud9kOyHnkIHL` |
| 2  | `yJDMwCD5oyI3ElEg` |
| 3  | `l9DuqQRF3KZd7MOs` |
| 4  | `GXQvY67hiyH2Ul26` |
| 5  | `rntdBXfZFtvNKpWy` |
| 6  | `nfj8aPTaIUMPF34z` |
| 7  | `xrKs7aJt6PyFmMOm` |
| 8  | `bjO5IrICDJ4Xtb0m` |
| 9  | `47VAv84ZMiGA4rkF` |
| 10 | `qAcU7jq8pbfdOSCW` |
| 11 | `LH8wW7LyNJnDjDIa` |
| 12 | `DoPVCNC5gKcRdZfc` |
| 13 | `c0Yi9f4flGoCLIvn` |
| 14 | `xhazovP1LRUjPTPr` |
| 15 | `KCMBLCVnYUmh1mik` |

> ⚠️ **Penting:** Jangan commit token langsung ke kode. Selalu gunakan environment variable `ENSEMBLEDATA_API_TOKEN`.

---

## 🏗️ Arsitektur Sistem

Kedua project memiliki arsitektur yang sama (pnpm monorepo):

```
TIKTOKSCRAP / INSTAGRAMSCRAP
├── artifacts/
│   ├── api-server/          ← Backend Express 5 (Node.js)
│   │   └── src/
│   │       ├── lib/         ← Logic scraping + analitik
│   │       └── routes/      ← Endpoint REST API
│   └── [frontend]/          ← Dashboard React (Vite)
├── lib/
│   ├── api-spec/            ← OpenAPI spec (kontrak API)
│   ├── api-client-react/    ← React Query hooks (auto-generated)
│   ├── api-zod/             ← Zod validation schemas
│   └── db/                  ← Drizzle ORM + schema PostgreSQL
└── pnpm-workspace.yaml
```

**Flow data:**
```
Input username/URL
      ↓
API Server (Express)
      ↓
EnsembleData API (data mentah)
      ↓
Normalisasi + Komputasi Analytics
      ↓
Simpan ke PostgreSQL
      ↓
Response JSON ke frontend/client
```

---

## ⚙️ Setup TIKTOKSCRAP

### 1. Clone repo

```bash
git clone https://github.com/TlTANPRO/TIKTOKSCRAP.git
cd TIKTOKSCRAP
```

### 2. Install dependencies

```bash
pnpm install
```

### 3. Setup environment variables

Buat file `.env` di root project:

```env
# Database PostgreSQL
DATABASE_URL=postgresql://user:password@localhost:5432/tiktokscrap

# EnsembleData API Token (pilih salah satu dari daftar token di atas)
ENSEMBLEDATA_API_TOKEN=zuggud9kOyHnkIHL

# Session secret (opsional, untuk auth)
SESSION_SECRET=random_secret_string_minimal_32_char
```

### 4. Setup database

```bash
# Push schema ke database
pnpm --filter @workspace/db run push
```

### 5. Jalankan API server

```bash
pnpm --filter @workspace/api-server run dev
```

Server berjalan di port **5000** (atau port yang dikonfigurasi di workflow).

### 6. Jalankan frontend (opsional)

```bash
pnpm --filter @workspace/tiktok-insight run dev
```

---

## ⚙️ Setup INSTAGRAMSCRAP

### 1. Clone repo

```bash
git clone https://github.com/TlTANPRO/INSTAGRAMSCRAP.git
cd INSTAGRAMSCRAP
```

### 2. Install dependencies

```bash
pnpm install
```

### 3. Setup environment variables

```env
# Database PostgreSQL
DATABASE_URL=postgresql://user:password@localhost:5432/instagramscrap

# EnsembleData API Token
ENSEMBLEDATA_API_TOKEN=yJDMwCD5oyI3ElEg

# Session secret
SESSION_SECRET=random_secret_string_minimal_32_char
```

### 4. Setup database

```bash
pnpm --filter @workspace/db run push
```

### 5. Jalankan server

```bash
pnpm --filter @workspace/api-server run dev
```

---

## 📱 Cara Scraping TikTok

### Melalui Frontend (Dashboard)

Buka browser, akses dashboard TIKTOKSCRAP, masukkan username atau URL akun TikTok, klik **Analisis**.

### Melalui API (curl / Postman)

#### Analisis akun baru

```bash
curl -X POST http://localhost:5000/api/tiktok/analyses \
  -H "Content-Type: application/json" \
  -d '{"input": "@namaakun"}'
```

Format input yang diterima:
- Username dengan @: `@namaakun`
- Username tanpa @: `namaakun`
- URL profil penuh: `https://www.tiktok.com/@namaakun`
- URL dengan parameter: `https://www.tiktok.com/@namaakun?lang=en`

#### Contoh response scraping TikTok

```json
{
  "id": 1,
  "input": "@namaakun",
  "profile": {
    "uniqueId": "namaakun",
    "nickname": "Nama Akun",
    "avatarUrl": "https://...",
    "bio": "Bio akun...",
    "verified": false,
    "followerCount": 150000,
    "followingCount": 300,
    "heartCount": 2500000,
    "videoCount": 87,
    "friendCount": 0
  },
  "videos": [
    {
      "id": "7123456789",
      "description": "Caption video #hashtag",
      "createTime": 1720000000,
      "coverUrl": "https://...",
      "videoUrl": "https://...",
      "playCount": 125000,
      "diggCount": 8500,
      "commentCount": 342,
      "shareCount": 1200,
      "collectCount": 450,
      "durationSeconds": 30
    }
  ],
  "aggregates": {
    "totalVideosAnalyzed": 87,
    "totalPlayCount": 5200000,
    "totalDiggCount": 380000,
    "avgPlayCount": 59770,
    "avgDiggCount": 4367,
    "engagementRate": 7.3,
    "postsPerWeek": 4.2
  },
  "insights": {
    "topByViews": [...],
    "topByLikes": [...],
    "topByComments": [...],
    "performanceTiers": {
      "viral": 3,
      "tinggi": 12,
      "bagus": 25,
      "rataRata": 30,
      "rendah": 17
    },
    "topHashtags": [
      {"tag": "#fyp", "count": 45},
      {"tag": "#viral", "count": 32}
    ],
    "performanceByDayOfWeek": [...],
    "marketInsights": {
      "strengths": ["Engagement rate 7.3% berada di atas rata-rata industri (5.5%)"],
      "weaknesses": ["..."],
      "recommendations": ["..."]
    },
    "industryBenchmark": {
      "engagementRateBenchmark": 5.5,
      "engagementRateComparison": "above",
      "viewsPerFollowerBenchmark": 0.5,
      "accountViewsPerFollower": 0.4,
      "viewsPerFollowerComparison": "below",
      "postingFrequencyBenchmark": 4,
      "postingFrequencyComparison": "average"
    },
    "growthPotential": {
      "score": 72,
      "label": "tinggi",
      "reasoning": "Kombinasi engagement rate, konsistensi posting, dan proporsi video berperforma tinggi menunjukkan momentum pertumbuhan yang kuat."
    }
  },
  "growthHistory": [
    {"date": "2025-06-01T00:00:00Z", "followerCount": 140000, "videoCount": 80},
    {"date": "2025-07-01T00:00:00Z", "followerCount": 150000, "videoCount": 87}
  ],
  "createdAt": "2025-07-13T10:00:00Z"
}
```

### Parameter Scraping TikTok (Internal)

File `artifacts/api-server/src/lib/tiktok.ts`:

```typescript
// Jumlah halaman yang diambil dari EnsembleData
// depth=10 → ±90-100 video terbaru (sekitar 17 detik)
export const SCRAPE_DEPTH = 10;
```

Untuk mengubah kedalaman scraping (lebih banyak/sedikit video), edit konstanta `SCRAPE_DEPTH`.

### Endpoint TikTok API: `/apis/tt`

EnsembleData TikTok endpoints yang digunakan:

| Endpoint | Parameter | Keterangan |
|----------|-----------|------------|
| `GET /apis/tt/user/info` | `username`, `token` | Profil akun TikTok |
| `GET /apis/tt/user/posts` | `username`, `depth`, `token` | Video terbaru |

---

## 📸 Cara Scraping Instagram

### Melalui Frontend (Dashboard)

Buka dashboard INSTAGRAMSCRAP, masukkan username atau URL profil Instagram, klik **Analisis**.

### Melalui API (curl / Postman)

#### Analisis akun baru

```bash
curl -X POST http://localhost:5000/api/instagram/analyses \
  -H "Content-Type: application/json" \
  -d '{"input": "@namaakun"}'
```

Format input yang diterima:
- Username dengan @: `@namaakun`
- Username tanpa @: `namaakun`
- URL profil: `https://www.instagram.com/namaakun/`
- URL dengan parameter: `https://www.instagram.com/namaakun/?hl=id`

#### Contoh response scraping Instagram

```json
{
  "id": 1,
  "input": "@namaakun",
  "profile": {
    "username": "namaakun",
    "fullName": "Nama Lengkap Akun",
    "profilePicUrl": "https://...",
    "biography": "Bio akun Instagram...",
    "verified": false,
    "followerCount": 85000,
    "followingCount": 500,
    "postCount": 142,
    "externalUrl": "https://linktr.ee/namaakun"
  },
  "posts": [
    {
      "id": "ABC123xyz",
      "caption": "Caption postingan #hashtag @mention",
      "createTime": 1720000000,
      "thumbnailUrl": "https://...",
      "postUrl": "https://www.instagram.com/p/ABC123xyz/",
      "mediaType": "GraphImage",
      "likeCount": 3200,
      "commentCount": 87,
      "viewCount": 15000,
      "saveCount": 420,
      "durationSeconds": 0
    }
  ],
  "aggregates": {
    "totalPostsAnalyzed": 96,
    "totalLikeCount": 280000,
    "totalCommentCount": 7500,
    "totalViewCount": 1200000,
    "totalSaveCount": 35000,
    "avgLikeCount": 2916,
    "avgCommentCount": 78,
    "avgViewCount": 12500,
    "avgSaveCount": 364,
    "engagementRate": 3.8,
    "postsPerWeek": 2.1,
    "mostViewedPostId": "DEF456",
    "mostLikedPostId": "GHI789"
  },
  "insights": {
    "topByViews": [...],
    "topByLikes": [...],
    "topByComments": [...],
    "performanceTiers": {
      "viral": 5,
      "tinggi": 18,
      "bagus": 30,
      "rataRata": 28,
      "rendah": 15
    },
    "topHashtags": [
      {"tag": "#indonesia", "count": 60},
      {"tag": "#lifestyle", "count": 45}
    ],
    "topMentions": [
      {"mention": "@brand_partner", "count": 12}
    ],
    "performanceByDayOfWeek": [
      {"day": "Senin", "avgLikeCount": 3100, "avgCommentCount": 82},
      {"day": "Selasa", "avgLikeCount": 2800, "avgCommentCount": 75}
    ],
    "marketInsights": {
      "strengths": ["Engagement rate 3.8% cukup kompetitif untuk niche lifestyle"],
      "weaknesses": ["Frekuensi posting 2.1x/minggu masih rendah"],
      "recommendations": ["Tingkatkan konsistensi posting minimal 3-4x per minggu"]
    },
    "growthPotential": {
      "score": 55,
      "label": "sedang",
      "reasoning": "Ada sinyal pertumbuhan positif, namun beberapa metrik masih bisa dioptimalkan."
    }
  },
  "growthHistory": [...],
  "createdAt": "2025-07-13T10:00:00Z"
}
```

### Parameter Scraping Instagram (Internal)

File `artifacts/api-server/src/lib/instagram.ts`:

```typescript
// Jumlah halaman pagination yang diambil
// depth=8 → ±96 postingan terbaru
export const SCRAPE_DEPTH = 8;
```

### Endpoint Instagram API: `/apis/ig`

EnsembleData Instagram endpoints yang digunakan:

| Endpoint | Parameter | Keterangan |
|----------|-----------|------------|
| `GET /apis/ig/user/info` | `username`, `token` | Profil akun Instagram |
| `GET /apis/ig/user/posts` | `username`, `depth`, `token` | Postingan terbaru |

---

## 🌐 API Endpoints Lengkap

### TikTok Endpoints

| Method | Path | Keterangan |
|--------|------|------------|
| `GET` | `/api/tiktok/analyses` | Daftar semua analisis yang tersimpan |
| `POST` | `/api/tiktok/analyses` | Analisis akun TikTok baru |
| `GET` | `/api/tiktok/analyses/:id` | Detail satu analisis |
| `DELETE` | `/api/tiktok/analyses/:id` | Hapus satu analisis |
| `GET` | `/api/healthz` | Cek status server |

### Instagram Endpoints

| Method | Path | Keterangan |
|--------|------|------------|
| `GET` | `/api/instagram/analyses` | Daftar semua analisis yang tersimpan |
| `POST` | `/api/instagram/analyses` | Analisis akun Instagram baru |
| `GET` | `/api/instagram/analyses/:id` | Detail satu analisis |
| `DELETE` | `/api/instagram/analyses/:id` | Hapus satu analisis |
| `GET` | `/api/healthz` | Cek status server |

### Contoh Penggunaan Lengkap (curl)

```bash
# Cek status server
curl http://localhost:5000/api/healthz

# --- TikTok ---

# Scrape akun TikTok (username)
curl -X POST http://localhost:5000/api/tiktok/analyses \
  -H "Content-Type: application/json" \
  -d '{"input": "namaakun"}'

# Scrape akun TikTok (URL)
curl -X POST http://localhost:5000/api/tiktok/analyses \
  -H "Content-Type: application/json" \
  -d '{"input": "https://www.tiktok.com/@namaakun"}'

# Lihat semua analisis TikTok
curl http://localhost:5000/api/tiktok/analyses

# Lihat detail analisis TikTok id=1
curl http://localhost:5000/api/tiktok/analyses/1

# Hapus analisis TikTok id=1
curl -X DELETE http://localhost:5000/api/tiktok/analyses/1

# --- Instagram ---

# Scrape akun Instagram
curl -X POST http://localhost:5000/api/instagram/analyses \
  -H "Content-Type: application/json" \
  -d '{"input": "namaakun"}'

# Scrape via URL Instagram
curl -X POST http://localhost:5000/api/instagram/analyses \
  -H "Content-Type: application/json" \
  -d '{"input": "https://www.instagram.com/namaakun/"}'

# Lihat semua analisis Instagram
curl http://localhost:5000/api/instagram/analyses

# Lihat detail analisis Instagram id=3
curl http://localhost:5000/api/instagram/analyses/3

# Hapus analisis Instagram id=3
curl -X DELETE http://localhost:5000/api/instagram/analyses/3
```

---

## 🔄 Rotasi Token

Karena EnsembleData menerapkan rate limiting per token, gunakan strategi rotasi berikut saat scraping massal:

### Strategi 1: Rotasi Manual

Ganti `ENSEMBLEDATA_API_TOKEN` di `.env` setiap kali muncul error rate limit (HTTP 429):

```bash
# Token 1 habis → ganti ke token 2
ENSEMBLEDATA_API_TOKEN=yJDMwCD5oyI3ElEg
```

Restart server setelah ganti token:
```bash
# Stop server (Ctrl+C), lalu:
pnpm --filter @workspace/api-server run dev
```

### Strategi 2: Rotasi Otomatis (Script Bash)

Script ini melakukan scraping ke banyak akun dengan rotasi token otomatis:

```bash
#!/bin/bash
# rotate_scrape.sh — Scraping dengan rotasi token otomatis

TOKENS=(
  "zuggud9kOyHnkIHL"
  "yJDMwCD5oyI3ElEg"
  "l9DuqQRF3KZd7MOs"
  "GXQvY67hiyH2Ul26"
  "rntdBXfZFtvNKpWy"
  "nfj8aPTaIUMPF34z"
  "xrKs7aJt6PyFmMOm"
  "bjO5IrICDJ4Xtb0m"
  "47VAv84ZMiGA4rkF"
  "qAcU7jq8pbfdOSCW"
  "LH8wW7LyNJnDjDIa"
  "DoPVCNC5gKcRdZfc"
  "c0Yi9f4flGoCLIvn"
  "xhazovP1LRUjPTPr"
  "KCMBLCVnYUmh1mik"
)

# Daftar akun yang mau di-scrape
ACCOUNTS=(
  "akun1"
  "akun2"
  "akun3"
  # tambah akun lain di sini
)

TOKEN_INDEX=0
PLATFORM="tiktok"  # Ganti ke "instagram" untuk Instagram

for account in "${ACCOUNTS[@]}"; do
  TOKEN="${TOKENS[$TOKEN_INDEX]}"
  echo "Scraping @$account dengan token ke-$((TOKEN_INDEX + 1))..."

  # Update token di .env (jika server butuh restart)
  # Atau jika server sudah support multi-token, kirim via header

  RESPONSE=$(curl -s -o /tmp/response.json -w "%{http_code}" \
    -X POST "http://localhost:5000/api/${PLATFORM}/analyses" \
    -H "Content-Type: application/json" \
    -d "{\"input\": \"$account\"}")

  if [ "$RESPONSE" = "429" ]; then
    echo "Rate limit! Rotate ke token berikutnya..."
    TOKEN_INDEX=$(( (TOKEN_INDEX + 1) % ${#TOKENS[@]} ))
    sleep 5
    # Retry dengan token baru (perlu update env dan restart)
  elif [ "$RESPONSE" = "201" ]; then
    echo "✅ Berhasil scrape @$account"
    TOKEN_INDEX=$(( (TOKEN_INDEX + 1) % ${#TOKENS[@]} ))
  else
    echo "❌ Error $RESPONSE untuk @$account"
  fi

  # Jeda antar request untuk menghindari rate limit
  sleep 2
done

echo "Selesai!"
```

### Strategi 3: Modifikasi Server untuk Multi-Token

Edit `artifacts/api-server/src/lib/tiktok.ts` (atau `instagram.ts`) untuk rotasi token otomatis di level kode:

```typescript
// Tambahkan di awal file
const ENSEMBLE_TOKENS = [
  "zuggud9kOyHnkIHL",
  "yJDMwCD5oyI3ElEg",
  "l9DuqQRF3KZd7MOs",
  "GXQvY67hiyH2Ul26",
  "rntdBXfZFtvNKpWy",
  "nfj8aPTaIUMPF34z",
  "xrKs7aJt6PyFmMOm",
  "bjO5IrICDJ4Xtb0m",
  "47VAv84ZMiGA4rkF",
  "qAcU7jq8pbfdOSCW",
  "LH8wW7LyNJnDjDIa",
  "DoPVCNC5gKcRdZfc",
  "c0Yi9f4flGoCLIvn",
  "xhazovP1LRUjPTPr",
  "KCMBLCVnYUmh1mik",
];

let tokenIndex = 0;

function getApiToken(): string {
  // Rotasi round-robin
  const token = ENSEMBLE_TOKENS[tokenIndex % ENSEMBLE_TOKENS.length];
  tokenIndex++;
  return token;
}

// Ganti fungsi getApiToken() yang ada (yang membaca dari env)
// dengan versi di atas untuk rotasi otomatis tanpa restart
```

---

## 🗃️ Schema Database

### TikTok (`tiktok_analyses`)

| Kolom | Tipe | Keterangan |
|-------|------|------------|
| `id` | serial (PK) | ID unik analisis |
| `input` | text | Input asli user (@username / URL) |
| `unique_id` | text | Username TikTok (normalized) |
| `nickname` | text | Nama tampilan akun |
| `avatar_url` | text | URL foto profil |
| `follower_count` | integer | Jumlah followers |
| `video_count` | integer | Jumlah video |
| `profile` | jsonb | Data profil lengkap |
| `videos` | jsonb | Array semua video yang diambil |
| `aggregates` | jsonb | Statistik agregat |
| `insights` | jsonb | Insights & analitik mendalam |
| `created_at` | timestamp | Waktu scraping |

### Instagram (`instagram_analyses`)

| Kolom | Tipe | Keterangan |
|-------|------|------------|
| `id` | serial (PK) | ID unik analisis |
| `input` | text | Input asli user |
| `username` | text | Username Instagram (normalized) |
| `full_name` | text | Nama lengkap akun |
| `profile_pic_url` | text | URL foto profil |
| `follower_count` | integer | Jumlah followers |
| `post_count` | integer | Jumlah postingan |
| `profile` | jsonb | Data profil lengkap |
| `posts` | jsonb | Array semua postingan yang diambil |
| `aggregates` | jsonb | Statistik agregat |
| `insights` | jsonb | Insights & analitik mendalam |
| `created_at` | timestamp | Waktu scraping |

---

## 📊 Metrik Analytics yang Dihitung

### TikTok

- **Engagement Rate** = (likes + comments + shares) / plays × 100%
- **Posts per Week** — Frekuensi posting mingguan
- **Performance Tiers** — Klasifikasi video: Viral (3x rata-rata likes), Tinggi (1.5x), Bagus (0.75x), Rata-rata (0.3x), Rendah (<0.3x)
- **Top Hashtags** — Hashtag paling sering digunakan
- **Top Mentions** — Akun paling sering di-mention
- **Performance by Day of Week** — Hari terbaik untuk posting
- **Duration Analysis** — Performa berdasarkan durasi video
- **Industry Benchmark** — Perbandingan vs rata-rata industri TikTok (ER: 5.5%, views/follower: 0.5, posting: 4x/minggu)
- **Growth Potential Score** — Skor 0-100 dengan label rendah/sedang/tinggi

### Instagram

- **Engagement Rate** = (likes + comments + saves) / followers × 100%
- **Posts per Week** — Frekuensi posting mingguan
- **Performance Tiers** — Sama seperti TikTok
- **Top Hashtags** — Hashtag paling sering dipakai
- **Top Mentions** — Akun paling sering di-mention
- **Performance by Month** — Tren bulanan performa konten
- **Duration Analysis** — Untuk konten Reels
- **Industry Benchmark** — Perbandingan vs standar industri Instagram
- **Growth Potential Score** — Skor 0-100 dengan label rendah/sedang/tinggi

---

## 🔧 Troubleshooting

### Error: `ENSEMBLEDATA_API_TOKEN is not configured`

**Penyebab:** Environment variable belum di-set.

**Solusi:**
```bash
# Pastikan .env ada dan berisi token
cat .env | grep ENSEMBLEDATA

# Atau set langsung saat jalankan server
ENSEMBLEDATA_API_TOKEN=zuggud9kOyHnkIHL pnpm --filter @workspace/api-server run dev
```

---

### Error: `Failed to reach the TikTok/Instagram data provider` (502)

**Penyebab:** EnsembleData tidak bisa diakses atau token invalid.

**Solusi:**
1. Cek koneksi internet server
2. Verifikasi token masih aktif di dashboard EnsembleData
3. Rotasi ke token lain

---

### Error: `Akun TikTok/Instagram tidak ditemukan` (404)

**Penyebab:** Username salah, akun privat, atau akun sudah dihapus.

**Solusi:**
- Cek ulang username/URL
- Akun privat tidak bisa di-scrape via EnsembleData
- Coba tanpa `@` di depan

---

### Error: `DATABASE_URL is not set` / Koneksi database gagal

**Penyebab:** PostgreSQL belum running atau DATABASE_URL salah.

**Solusi:**
```bash
# Pastikan PostgreSQL running
pg_isready -h localhost -p 5432

# Cek DATABASE_URL
cat .env | grep DATABASE_URL

# Jalankan ulang schema push
pnpm --filter @workspace/db run push
```

---

### Rate Limit (HTTP 429)

**Penyebab:** Token habis quota request.

**Solusi:**
- Tunggu beberapa menit sebelum retry
- Rotasi ke token lain (lihat bagian [Rotasi Token](#-rotasi-token))
- Kurangi `SCRAPE_DEPTH` untuk menghemat quota (1 analisis = 1 call profil + depth calls untuk posts)

---

### Typecheck Error setelah edit kode

```bash
# Jalankan typecheck lengkap
pnpm run typecheck

# Atau hanya untuk package tertentu
pnpm --filter @workspace/api-server run typecheck
```

---

## 🚀 Quick Start (Ringkasan)

```bash
# 1. Clone salah satu repo
git clone https://github.com/TlTANPRO/TIKTOKSCRAP.git
cd TIKTOKSCRAP

# 2. Install dependencies
pnpm install

# 3. Buat .env
echo 'DATABASE_URL=postgresql://user:pass@localhost:5432/tiktokscrap' > .env
echo 'ENSEMBLEDATA_API_TOKEN=zuggud9kOyHnkIHL' >> .env

# 4. Push schema database
pnpm --filter @workspace/db run push

# 5. Jalankan server
pnpm --filter @workspace/api-server run dev

# 6. Scrape akun TikTok pertama
curl -X POST http://localhost:5000/api/tiktok/analyses \
  -H "Content-Type: application/json" \
  -d '{"input": "@namaakuntiktok"}'
```

---

*Panduan ini dibuat untuk tim internal TITANPRO. Diperbarui: Juli 2025.*
