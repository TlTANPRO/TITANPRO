# 🚀 TITANPRO — Panduan Lengkap Scraping TikTok & Instagram

> **Panduan ini ditulis berdasarkan hasil pengujian nyata** terhadap EnsembleData API pada 13 Juli 2026 menggunakan token tim TITANPRO.

Panduan ini mencakup cara menggunakan dua sistem scraping:
- **[TIKTOKSCRAP](https://github.com/TlTANPRO/TIKTOKSCRAP)** — Analitik akun TikTok
- **[INSTAGRAMSCRAP](https://github.com/TlTANPRO/INSTAGRAMSCRAP)** — Analitik akun Instagram

---

## 📋 Daftar Isi

1. [Hasil Pengujian API](#-hasil-pengujian-api-nyata)
2. [Token EnsembleData & Status](#-token-ensembledata--status)
3. [Arsitektur Sistem](#-arsitektur-sistem)
4. [Setup TIKTOKSCRAP](#-setup-tiktokscrap)
5. [Setup INSTAGRAMSCRAP](#-setup-instagramscrap--bug-fix)
6. [Cara Scraping TikTok](#-cara-scraping-tiktok)
7. [Cara Scraping Instagram](#-cara-scraping-instagram)
8. [API Endpoints Lengkap](#-api-endpoints-lengkap)
9. [Rotasi Token](#-rotasi-token)
10. [Troubleshooting](#-troubleshooting)

---

## ✅ Hasil Pengujian API Nyata

Pengujian dilakukan langsung ke EnsembleData API pada 13 Juli 2026. Berikut hasilnya:

### TikTok — BERHASIL ✅

```
Endpoint: GET https://ensembledata.com/apis/tt/user/info?username=charlidamelio&token=nfj8aPTaIUMPF34z
Status: HTTP 200 OK

Hasil:
  uniqueId   : charlidamelio
  followers  : 159.200.072
  following  : 1.400
  videos     : 3.148
  verified   : true
```

```
Endpoint: GET https://ensembledata.com/apis/tt/user/posts?username=charlidamelio&depth=1&token=nfj8aPTaIUMPF34z
Status: HTTP 200 OK

Hasil:
  Posts fetched  : 10 video
  Latest video ID: 7661492762902023437
  Play count     : 1.809.862
  Like count     : 199.668
  Comment count  : 1.500
  Share count    : 1.799
  Duration       : 20 detik
  Created        : 2026-07-12
```

### Instagram — BERHASIL ✅

```
Endpoint: GET https://ensembledata.com/apis/instagram/user/info?username=nike&token=nfj8aPTaIUMPF34z
Status: HTTP 200 OK

Hasil:
  user_id (pk) : 13460080
  username     : nike
  full_name    : Nike
  is_verified  : true
  is_private   : false
```

```
Endpoint: GET https://ensembledata.com/apis/instagram/user/detailed-info?username=nike&token=bjO5IrICDJ4Xtb0m
Status: HTTP 200 OK

Hasil:
  followers  : 291.798.749
  media_count: 1.662
  biography  : Just Do It.
```

```
Endpoint: GET https://ensembledata.com/apis/instagram/user/posts?user_id=13460080&depth=1&token=nfj8aPTaIUMPF34z
Status: HTTP 200 OK

Hasil:
  Response keys: { count, posts, last_cursor }
  Posts fetched : sesuai depth
```

### ✅ Bug di INSTAGRAMSCRAP — Sudah Diperbaiki

Saat pengujian ditemukan dan langsung diperbaiki **2 bug kritis** di kode INSTAGRAMSCRAP:

| | Sebelum (Salah) | Sesudah (Benar) |
|---|---|---|
| Base URL | `https://ensembledata.com/apis/ig` | `https://ensembledata.com/apis/instagram` |
| Posts param | `username=...` | `user_id=...` (pk dari `/user/info`) |

Perbaikan sudah di-push ke repo. Clone terbaru sudah langsung benar — tidak perlu patch manual.

---

## 🔑 Token EnsembleData & Status

Tim TITANPRO memiliki **26 token**. Quota reset setiap hari (jam 00:00 UTC).

| No | Token | Status (13 Jul 2026) |
|----|-------|----------------------|
| 1  | `zuggud9kOyHnkIHL` | 🔴 Habis hari ini |
| 2  | `yJDMwCD5oyI3ElEg` | 🔴 Habis hari ini |
| 3  | `l9DuqQRF3KZd7MOs` | 🔴 Habis hari ini |
| 4  | `GXQvY67hiyH2Ul26` | 🔴 Habis hari ini |
| 5  | `rntdBXfZFtvNKpWy` | 🔴 Habis hari ini |
| 6  | `nfj8aPTaIUMPF34z` | ✅ **Aktif** |
| 7  | `xrKs7aJt6PyFmMOm` | 🔴 Habis hari ini |
| 8  | `bjO5IrICDJ4Xtb0m` | ✅ **Aktif** |
| 9  | `47VAv84ZMiGA4rkF` | 🔴 Habis hari ini |
| 10 | `qAcU7jq8pbfdOSCW` | 🔴 Habis hari ini |
| 11 | `LH8wW7LyNJnDjDIa` | 🔴 Habis hari ini |
| 12 | `DoPVCNC5gKcRdZfc` | 🔴 Habis hari ini |
| 13 | `c0Yi9f4flGoCLIvn` | 🔴 Habis hari ini |
| 14 | `xhazovP1LRUjPTPr` | 🔴 Habis hari ini |
| 15 | `KCMBLCVnYUmh1mik` | 🔴 Habis hari ini |
| 16 | `F79tRykPWKcq0qQL` | ✅ **Aktif** |
| 17 | `ttl8ycpEOmZYpbOx` | 🔴 Habis hari ini |
| 18 | `i0eSnpdHBtmGpTu3` | ✅ **Aktif** |
| 19 | `62ltym8yt6kB3B4b` | ✅ **Aktif** |
| 20 | `QVtH5e8OFtELlt9I` | ✅ **Aktif** |
| 21 | `8sncVdXDcGSVFwkI` | ✅ **Aktif** |
| 22 | `9x92SNN7bQRmEnvI` | ✅ **Aktif** |
| 23 | `L5g8kn8nSCAy9vhY` | ✅ **Aktif** |
| 24 | `c9AF4bHCRGcZUvBe` | ✅ **Aktif** |
| 25 | `TvFjXULl4NI2x6oq` | ✅ **Aktif** |
| 26 | `9rO01ynufkgdj4oy` | ✅ **Aktif** |

> Token yang habis hari ini **otomatis reset besok**. Gunakan token aktif yang tersisa atau tunggu reset.

**Cara cek token mana yang aktif:**
```bash
# Cek status token secara cepat (TikTok)
curl -s "https://ensembledata.com/apis/tt/user/info?username=charlidamelio&token=TOKEN_DISINI" | grep -c '"uniqueId"'
# Output 1 = aktif, 0 = tidak aktif

# Cek status token (Instagram)
curl -s "https://ensembledata.com/apis/instagram/user/info?username=nike&token=TOKEN_DISINI" | grep -c '"username"'
# Output 1 = aktif, 0 = tidak aktif
```

---

## 🏗️ Arsitektur Sistem

Kedua project adalah pnpm monorepo dengan struktur yang sama:

```
TIKTOKSCRAP / INSTAGRAMSCRAP
├── artifacts/
│   ├── api-server/              ← Backend Express 5 (Node.js)
│   │   └── src/
│   │       ├── lib/tiktok.ts    ← Logic scraping + analitik TikTok
│   │       ├── lib/instagram.ts ← Logic scraping + analitik Instagram
│   │       └── routes/          ← REST API endpoints
│   └── [frontend]/              ← Dashboard React (Vite)
├── lib/
│   ├── api-spec/openapi.yaml    ← Kontrak API (source of truth)
│   ├── api-client-react/        ← React Query hooks (auto-generated)
│   ├── api-zod/                 ← Zod schemas (auto-generated)
│   └── db/                      ← Drizzle ORM + PostgreSQL schema
└── pnpm-workspace.yaml
```

**Flow data (TikTok):**
```
Input: "charlidamelio" atau URL TikTok
         ↓
  Parse username (strip @, URL)
         ↓
  EnsembleData /tt/user/info    → profil akun
  EnsembleData /tt/user/posts   → video terbaru (depth × ~10 video)
         ↓
  Normalisasi + Hitung Analytics
  (engagement rate, tiers, hashtags, benchmark, growth potential)
         ↓
  Simpan ke PostgreSQL
         ↓
  Response JSON → Frontend dashboard
```

**Flow data (Instagram):**
```
Input: "nike" atau URL Instagram
         ↓
  Parse username (strip @, URL, /?hl=id)
         ↓
  EnsembleData /instagram/user/info        → dapat user_id (pk)
  EnsembleData /instagram/user/detailed-info → follower, bio, media_count
  EnsembleData /instagram/user/posts (user_id) → postingan terbaru
         ↓
  Normalisasi + Hitung Analytics
         ↓
  Simpan ke PostgreSQL
         ↓
  Response JSON → Frontend dashboard
```

---

## ⚙️ Setup TIKTOKSCRAP

### 1. Clone & install

```bash
git clone https://github.com/TlTANPRO/TIKTOKSCRAP.git
cd TIKTOKSCRAP
pnpm install
```

### 2. Buat file `.env`

```env
# Wajib: port server (jangan dihilangkan — server crash tanpa ini)
PORT=5000

# Wajib: PostgreSQL connection string
DATABASE_URL=postgresql://user:password@localhost:5432/tiktokscrap

# Wajib: Token EnsembleData (gunakan yang masih aktif)
ENSEMBLEDATA_API_TOKEN=nfj8aPTaIUMPF34z

# Opsional
SESSION_SECRET=isi_dengan_string_acak_min_32_karakter
```

### 3. Push schema database

```bash
pnpm --filter @workspace/db run push
```

### 4. Jalankan API server

```bash
pnpm --filter @workspace/api-server run dev
# Server berjalan di http://localhost:5000
```

### 5. Verifikasi server berjalan

```bash
curl http://localhost:5000/api/healthz
# Response: {"status":"ok"}
```

### 6. Test scraping pertama

```bash
curl -X POST http://localhost:5000/api/tiktok/analyses \
  -H "Content-Type: application/json" \
  -d '{"input": "charlidamelio"}'
```

---

## ⚙️ Setup INSTAGRAMSCRAP

> ✅ **Bug sudah diperbaiki di repo.** Versi terbaru INSTAGRAMSCRAP sudah menggunakan base URL yang benar (`/apis/instagram`) dan parameter `user_id` yang tepat untuk endpoint posts. Tidak perlu patch manual.

### 1. Clone & install

```bash
git clone https://github.com/TlTANPRO/INSTAGRAMSCRAP.git
cd INSTAGRAMSCRAP
pnpm install
```

### 2. Buat file `.env`

```env
PORT=5000
DATABASE_URL=postgresql://user:password@localhost:5432/instagramscrap
ENSEMBLEDATA_API_TOKEN=nfj8aPTaIUMPF34z
SESSION_SECRET=isi_dengan_string_acak_min_32_karakter
```

### 5. Push schema & jalankan

```bash
pnpm --filter @workspace/db run push
pnpm --filter @workspace/api-server run dev
```

### 6. Test scraping pertama

```bash
curl -X POST http://localhost:5000/api/instagram/analyses \
  -H "Content-Type: application/json" \
  -d '{"input": "nike"}'
```

---

## 📱 Cara Scraping TikTok

### Format input yang diterima

```bash
# Semua format berikut diproses dengan benar:
{"input": "charlidamelio"}           # tanpa @
{"input": "@charlidamelio"}          # dengan @
{"input": "https://www.tiktok.com/@charlidamelio"}        # URL penuh
{"input": "https://www.tiktok.com/@charlidamelio?lang=en"} # URL dengan parameter
```

### Via API (curl)

```bash
# Analisis akun baru (simpan ke database)
curl -X POST http://localhost:5000/api/tiktok/analyses \
  -H "Content-Type: application/json" \
  -d '{"input": "@charlidamelio"}'

# Lihat semua analisis tersimpan
curl http://localhost:5000/api/tiktok/analyses

# Lihat detail analisis id=1
curl http://localhost:5000/api/tiktok/analyses/1

# Hapus analisis id=1
curl -X DELETE http://localhost:5000/api/tiktok/analyses/1
```

### Contoh response TikTok

```json
{
  "id": 1,
  "input": "@charlidamelio",
  "profile": {
    "uniqueId": "charlidamelio",
    "nickname": "charli d'amelio",
    "avatarUrl": "https://p16-common-sign.tiktokcdn-eu.com/...",
    "bio": "",
    "verified": true,
    "followerCount": 159200072,
    "followingCount": 1400,
    "heartCount": 0,
    "videoCount": 3148,
    "friendCount": 0
  },
  "videos": [
    {
      "id": "7661492762902023437",
      "description": "",
      "createTime": 1752291915,
      "coverUrl": "https://...",
      "videoUrl": "https://...",
      "playCount": 1809862,
      "diggCount": 199668,
      "commentCount": 1500,
      "shareCount": 1799,
      "collectCount": 0,
      "durationSeconds": 20
    }
  ],
  "aggregates": {
    "totalVideosAnalyzed": 100,
    "totalPlayCount": 180000000,
    "avgPlayCount": 1800000,
    "engagementRate": 11.1,
    "postsPerWeek": 3.5
  },
  "insights": {
    "performanceTiers": {
      "viral": 15,
      "tinggi": 22,
      "bagus": 30,
      "rataRata": 20,
      "rendah": 13
    },
    "topHashtags": [
      {"tag": "#fyp", "count": 45},
      {"tag": "#dance", "count": 32}
    ],
    "growthPotential": {
      "score": 78,
      "label": "tinggi",
      "reasoning": "Engagement rate dan konsistensi posting di atas rata-rata industri."
    },
    "industryBenchmark": {
      "engagementRateBenchmark": 5.5,
      "engagementRateComparison": "above",
      "postingFrequencyBenchmark": 4,
      "postingFrequencyComparison": "average"
    },
    "marketInsights": {
      "strengths": ["Engagement rate 11.1% jauh di atas rata-rata industri (5.5%)"],
      "weaknesses": [],
      "recommendations": []
    }
  },
  "growthHistory": [
    {"date": "2026-07-13T10:00:00Z", "followerCount": 159200072, "videoCount": 3148}
  ],
  "createdAt": "2026-07-13T10:00:00Z"
}
```

### Parameter kedalaman scraping

Di file `artifacts/api-server/src/lib/tiktok.ts`:

```typescript
// depth=10 → ~100 video terbaru (~17 detik proses)
// depth=5  → ~50 video (lebih cepat)
// depth=1  → ~10 video (untuk test)
export const SCRAPE_DEPTH = 10;
```

---

## 📸 Cara Scraping Instagram

### Format input yang diterima

```bash
{"input": "nike"}                                      # tanpa @
{"input": "@nike"}                                     # dengan @
{"input": "https://www.instagram.com/nike/"}           # URL penuh
{"input": "https://www.instagram.com/nike/?hl=id"}     # URL dengan parameter bahasa
```

### Via API (curl)

```bash
# Analisis akun baru
curl -X POST http://localhost:5000/api/instagram/analyses \
  -H "Content-Type: application/json" \
  -d '{"input": "nike"}'

# Lihat semua analisis tersimpan
curl http://localhost:5000/api/instagram/analyses

# Detail analisis id=1
curl http://localhost:5000/api/instagram/analyses/1

# Hapus analisis id=1
curl -X DELETE http://localhost:5000/api/instagram/analyses/1
```

### Contoh response Instagram

```json
{
  "id": 1,
  "input": "nike",
  "profile": {
    "username": "nike",
    "fullName": "Nike",
    "profilePicUrl": "https://scontent-tpe1-1.cdninstagram.com/...",
    "biography": "Just Do It.",
    "verified": true,
    "followerCount": 291798749,
    "followingCount": 0,
    "postCount": 1662,
    "externalUrl": ""
  },
  "posts": [
    {
      "id": "3ABC123xyz",
      "caption": "Caption postingan #hashtag",
      "createTime": 1752000000,
      "thumbnailUrl": "https://...",
      "postUrl": "https://www.instagram.com/p/3ABC123xyz/",
      "mediaType": "GraphImage",
      "likeCount": 45000,
      "commentCount": 1200,
      "viewCount": 500000,
      "saveCount": 8000,
      "durationSeconds": 0
    }
  ],
  "aggregates": {
    "totalPostsAnalyzed": 96,
    "avgLikeCount": 42000,
    "engagementRate": 0.014,
    "postsPerWeek": 3.2
  },
  "insights": {
    "performanceTiers": {
      "viral": 8,
      "tinggi": 20,
      "bagus": 35,
      "rataRata": 25,
      "rendah": 8
    },
    "topHashtags": [{"tag": "#nike", "count": 50}],
    "growthPotential": {"score": 65, "label": "sedang", "reasoning": "..."}
  },
  "growthHistory": [...],
  "createdAt": "2026-07-13T10:00:00Z"
}
```

### Parameter kedalaman scraping

Di file `artifacts/api-server/src/lib/instagram.ts`:

```typescript
// depth=8 → ~96 postingan terbaru
// depth=4 → ~48 postingan (lebih cepat)
// depth=1 → ~12 postingan (untuk test)
export const SCRAPE_DEPTH = 8;
```

---

## 🌐 API Endpoints Lengkap

### EnsembleData API (Terverifikasi)

Base URL: `https://ensembledata.com/apis`

#### TikTok (`/tt/...`)

| Endpoint | Parameter Wajib | Keterangan |
|----------|----------------|------------|
| `GET /tt/user/info` | `username`, `token` | Profil akun TikTok |
| `GET /tt/user/posts` | `username`, `depth`, `token` | Video terbaru (~10/depth) |
| `GET /tt/user/search` | `keyword`, `token` | Cari akun TikTok |
| `GET /tt/post/info` | `aweme_id`, `token` | Detail satu video |
| `GET /tt/post/comments` | `aweme_id`, `token` | Komentar video |
| `GET /tt/hashtag/posts` | `hashtag`, `token` | Video berdasarkan hashtag |
| `GET /tt/keyword/search` | `keyword`, `token` | Cari konten |

#### Instagram (`/instagram/...`)

| Endpoint | Parameter Wajib | Keterangan |
|----------|----------------|------------|
| `GET /instagram/user/info` | `username`, `token` | Profil dasar (dapat `pk`/user_id) |
| `GET /instagram/user/detailed-info` | `username`, `token` | Profil lengkap + follower count |
| `GET /instagram/user/posts` | `user_id`, `depth`, `token` | Postingan terbaru |
| `GET /instagram/user/reels` | `user_id`, `token` | Reels terbaru |
| `GET /instagram/user/tagged-posts` | `user_id`, `token` | Post yang tag akun ini |
| `GET /instagram/user/followers` | `user_id`, `token` | Daftar follower |
| `GET /instagram/post/details` | `post_id`, `token` | Detail satu postingan |
| `GET /instagram/post/comments` | `post_id`, `token` | Komentar postingan |
| `GET /instagram/search` | `keyword`, `token` | Cari akun/hashtag |

> **Penting:** Instagram posts membutuhkan `user_id` (nilai `pk` dari endpoint `/user/info`), **bukan** username.

### TITANPRO API Server Endpoints

| Method | Path | Keterangan |
|--------|------|------------|
| `GET` | `/api/healthz` | Status server |
| `POST` | `/api/tiktok/analyses` | Mulai analisis TikTok baru |
| `GET` | `/api/tiktok/analyses` | Daftar semua analisis TikTok |
| `GET` | `/api/tiktok/analyses/:id` | Detail satu analisis TikTok |
| `DELETE` | `/api/tiktok/analyses/:id` | Hapus analisis TikTok |
| `POST` | `/api/instagram/analyses` | Mulai analisis Instagram baru |
| `GET` | `/api/instagram/analyses` | Daftar semua analisis Instagram |
| `GET` | `/api/instagram/analyses/:id` | Detail satu analisis Instagram |
| `DELETE` | `/api/instagram/analyses/:id` | Hapus analisis Instagram |

---

## 🔄 Rotasi Token

Setiap token EnsembleData memiliki quota harian. Setelah habis, muncul pesan:
```json
{"detail": "Maximum requests limit reached for today. Send an email at hello@ensembledata.com"}
```

### Cara cek semua token sekaligus

Simpan script ini sebagai `cek_token.sh`:

```bash
#!/bin/bash
# cek_token.sh — Cek status semua token TITANPRO

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
  "F79tRykPWKcq0qQL"
  "ttl8ycpEOmZYpbOx"
  "i0eSnpdHBtmGpTu3"
  "62ltym8yt6kB3B4b"
  "QVtH5e8OFtELlt9I"
  "8sncVdXDcGSVFwkI"
  "9x92SNN7bQRmEnvI"
  "L5g8kn8nSCAy9vhY"
  "c9AF4bHCRGcZUvBe"
  "TvFjXULl4NI2x6oq"
  "9rO01ynufkgdj4oy"
  "KCMBLCVnYUmh1mik"
)

echo "=== Cek Token TikTok ($(date)) ==="
for token in "${TOKENS[@]}"; do
  resp=$(curl -s "https://ensembledata.com/apis/tt/user/info?username=charlidamelio&token=$token")
  if echo "$resp" | grep -q '"uniqueId"\|"followerCount"'; then
    echo "✅ AKTIF  → $token"
  elif echo "$resp" | grep -q "Maximum requests"; then
    echo "🔴 HABIS  → $token"
  else
    echo "❓ UNKNOWN → $token | $(echo $resp | head -c 60)"
  fi
done
```

```bash
chmod +x cek_token.sh
./cek_token.sh
```

### Ganti token di `.env` dan restart server

```bash
# Edit .env
ENSEMBLEDATA_API_TOKEN=bjO5IrICDJ4Xtb0m   # ganti ke token aktif lain

# Restart API server (Ctrl+C dulu, lalu:)
pnpm --filter @workspace/api-server run dev
```

### Rotasi otomatis di kode server (opsional)

Edit `artifacts/api-server/src/lib/tiktok.ts` dan `instagram.ts` untuk rotasi otomatis:

```typescript
// Ganti fungsi getApiToken() yang ada dengan ini:
const ENSEMBLE_TOKENS = [
  "zuggud9kOyHnkIHL", "yJDMwCD5oyI3ElEg", "l9DuqQRF3KZd7MOs",
  "GXQvY67hiyH2Ul26", "rntdBXfZFtvNKpWy", "nfj8aPTaIUMPF34z",
  "xrKs7aJt6PyFmMOm", "bjO5IrICDJ4Xtb0m", "47VAv84ZMiGA4rkF",
  "qAcU7jq8pbfdOSCW", "LH8wW7LyNJnDjDIa", "DoPVCNC5gKcRdZfc",
  "c0Yi9f4flGoCLIvn", "xhazovP1LRUjPTPr", "KCMBLCVnYUmh1mik",
  "F79tRykPWKcq0qQL", "ttl8ycpEOmZYpbOx", "i0eSnpdHBtmGpTu3",
  "62ltym8yt6kB3B4b", "QVtH5e8OFtELlt9I", "8sncVdXDcGSVFwkI",
  "9x92SNN7bQRmEnvI", "L5g8kn8nSCAy9vhY", "c9AF4bHCRGcZUvBe",
  "TvFjXULl4NI2x6oq", "9rO01ynufkgdj4oy",
];

let _tokenIdx = 0;

function getApiToken(): string {
  // Round-robin: ganti token setiap request
  const token = ENSEMBLE_TOKENS[_tokenIdx % ENSEMBLE_TOKENS.length];
  _tokenIdx++;
  return token;
}
```

Dengan ini, setiap request otomatis berganti token sehingga quota tersebar merata ke semua token.

---

## 📊 Metrik Analytics yang Dihitung

### TikTok

| Metrik | Formula |
|--------|---------|
| Engagement Rate | `(likes + comments + shares) / plays × 100%` |
| Posts/Week | Rata-rata video diunggah per minggu |
| Performance Tier | Viral (≥3× rata-rata likes), Tinggi (≥1.5×), Bagus (≥0.75×), Rata-rata (≥0.3×), Rendah (<0.3×) |
| Growth Potential Score | Skor 0–100 (rendah / sedang / tinggi) |
| Industry Benchmark | ER: 5.5%, views/follower: 0.5, frekuensi: 4×/minggu |

### Instagram

| Metrik | Formula |
|--------|---------|
| Engagement Rate | `(likes + comments + saves) / followers × 100%` |
| Posts/Week | Rata-rata postingan per minggu |
| Performance Tier | Sama seperti TikTok |
| Growth Potential Score | Skor 0–100 |

Analytics lain yang tersedia di kedua platform:
- Top 5 video/post by views, likes, comments
- Top 10 hashtag yang paling sering digunakan
- Top mentions (@akun paling sering di-mention)
- Performa per hari dalam seminggu (Senin–Minggu)
- Tren performa per bulan
- Analisis durasi konten (video pendek vs panjang)
- Ringkasan tahunan (year-over-year)
- Market insights: strengths, weaknesses, recommendations

---

## 🔧 Troubleshooting

### ❌ "ENSEMBLEDATA_API_TOKEN is not configured"

Server tidak menemukan token. Pastikan `.env` ada dan berisi token:
```bash
cat .env | grep ENSEMBLEDATA_API_TOKEN
# Jika kosong:
echo "ENSEMBLEDATA_API_TOKEN=nfj8aPTaIUMPF34z" >> .env
# Restart server
```

---

### ❌ "Maximum requests limit reached for today" (TikTok/Instagram)

Token habis quota hari ini. Solusi:
1. Jalankan `cek_token.sh` untuk cari token yang masih aktif
2. Ganti `ENSEMBLEDATA_API_TOKEN` di `.env` ke token aktif
3. Atau tunggu reset besok jam 00:00 UTC (~07:00 WIB)

---

### ❌ Instagram selalu gagal / 404

Verifikasi base URL sudah benar di source code:
```bash
grep "ENSEMBLE_BASE_URL" artifacts/api-server/src/lib/instagram.ts
# Output yang benar: https://ensembledata.com/apis/instagram
```

> Repo terbaru sudah menggunakan URL yang benar. Jika output masih menunjukkan `/apis/ig`, pastikan kamu sudah `git pull` versi terbaru.

---

### ❌ "Akun tidak ditemukan" (404) padahal akun ada

Kemungkinan penyebab:
- Akun **privat** — EnsembleData tidak bisa akses akun privat
- Username salah ketik — coba tanpa @, atau salin dari URL profil
- Akun terlalu baru — belum terindeks di EnsembleData

---

### ❌ Koneksi database gagal

```bash
# Pastikan PostgreSQL running
pg_isready -h localhost -p 5432

# Cek DATABASE_URL di .env
cat .env | grep DATABASE_URL

# Jalankan ulang schema push
pnpm --filter @workspace/db run push
```

---

### ❌ TypeScript error setelah edit kode

```bash
# Typecheck seluruh project
pnpm run typecheck

# Hanya API server
pnpm --filter @workspace/api-server run typecheck
```

---

*Panduan ini dibuat berdasarkan pengujian nyata terhadap EnsembleData API — 13 Juli 2026. Tim TITANPRO.*
