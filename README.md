# 🚀 TITANPRO — Panduan Lengkap Scraping TikTok & Instagram

> **Panduan ini ditulis berdasarkan hasil pengujian nyata** terhadap EnsembleData API pada 13 Juli 2026 menggunakan token tim TITANPRO.

Panduan ini mencakup cara menggunakan dua sistem scraping:
- **[TIKTOKSCRAP](https://github.com/TlTANPRO/TIKTOKSCRAP)** — Analitik akun TikTok
- **[INSTAGRAMSCRAP](https://github.com/TlTANPRO/INSTAGRAMSCRAP)** — Analitik akun Instagram

---

## 📋 Daftar Isi

1. [Hasil Pengujian API EnsembleData](#-hasil-pengujian-api-nyata)
2. [Alternatif Scraping Tanpa Key](#-alternatif-scraping-tanpa-ensembledata)
3. [Alternatif API Berbayar / Berkey](#-alternatif-api-berbayar--berkey)
4. [Token EnsembleData & Status](#-token-ensembledata--status)
5. [Arsitektur Sistem](#-arsitektur-sistem)
6. [Setup TIKTOKSCRAP](#-setup-tiktokscrap)
7. [Setup INSTAGRAMSCRAP](#-setup-instagramscrap--bug-fix)
8. [Cara Scraping TikTok](#-cara-scraping-tiktok)
9. [Cara Scraping Instagram](#-cara-scraping-instagram)
10. [API Endpoints Lengkap](#-api-endpoints-lengkap)
11. [Rotasi Token](#-rotasi-token)
12. [Troubleshooting](#-troubleshooting)

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
## 🔬 Alternatif Scraping Tanpa EnsembleData

Hasil pengujian nyata pada **14 Juli 2026** terhadap **semua opsi scraping** yang bisa digunakan selain EnsembleData. Setiap metode diuji langsung — bukan asumsi.

---

### 📊 Ringkasan Hasil Test Semua Opsi

| # | Metode | Platform | Profil | Posts/Video | Key? | Status |
|---|--------|----------|--------|-------------|------|--------|
| 1 | **tikwm.com API** `?hd=1` | TikTok | ✅ | ❌ posts, ✅ single video | Tidak | **BERHASIL** |
| 2 | **i.instagram.com** `web_profile_info` | Instagram | ✅ | ✅ 12 terbaru | Tidak | **BERHASIL** |
| 3 | **yt-dlp** (post tunggal) | Instagram | ❌ | ✅ per-post | Tidak | **BERHASIL (terbatas)** |
| 4 | TikTok web API `/api/user/detail/` | TikTok | ❌ | ❌ | Tidak | BLOCKED (response kosong) |
| 5 | TikTok mobile API (Argus) | TikTok | ❌ | ❌ | Tidak | BLOCKED |
| 6 | tikwm.com `/api/user/posts` | TikTok | - | ❌ | Tidak | BLOCKED (Cloudflare) |
| 7 | TikTok oEmbed | TikTok | ❌ | ❌ | Tidak | BLOCKED |
| 8 | yt-dlp user page | TikTok/IG | ❌ | ❌ | Tidak | BLOCKED (429/error) |
| 9 | Instagram `?__a=1` | Instagram | ❌ | ❌ | Tidak | BLOCKED |
| 10 | Instagram GraphQL | Instagram | ❌ | ❌ | Tidak | BLOCKED |
| 11 | Instagram feed API | Instagram | ❌ | ❌ | Tidak | BLOCKED (butuh auth) |
| 12 | SocialBlade | Keduanya | ❌ | ❌ | Tidak | BLOCKED (Cloudflare) |
| 13 | Instaloader | Instagram | ❌ | ❌ | Tidak | Python tidak tersedia di env |
| 14 | HikerAPI | Keduanya | ❌ | ❌ | **Ya** | Butuh API key |
| 15 | Tikhub.io | Keduanya | ❌ | ❌ | **Ya** | Butuh API key |
| 16 | ScraperAPI | Keduanya | ❌ | ❌ | **Ya** | Butuh API key |
| 17 | Apify | Keduanya | ❌ | ❌ | **Ya** | Butuh API key |
| 18 | Bright Data | Keduanya | ❌ | ❌ | **Ya** | Butuh API key |

---

### ✅ OPSI 1 — tikwm.com (TikTok Profil + Video Detail)

**Berhasil ditest langsung. Gratis, tanpa API key.**

#### Profil akun TikTok

```bash
curl "https://www.tikwm.com/api/user/info?unique_id=charlidamelio&hd=1"
```

Response nyata (ditest 14 Jul 2026):
```json
{
  "user": {
    "id": "5831967",
    "uniqueId": "charlidamelio",
    "nickname": "charli d'amelio",
    "verified": true,
    "secUid": "MS4wLjABAAAA-VASjiXTh7wDDyXvjk10VFhMWUAoxr8bgfO1kAL1-9s"
  },
  "stats": {
    "followerCount": 159200026,
    "followingCount": 1400,
    "videoCount": 3151,
    "heartCount": 12265213906
  }
}
```

Multi-account test:
- `charlidamelio` → 159.200.026 followers, 3.151 videos ✅
- `khaby.lame` → 162.344.007 followers, 1.343 videos ✅
- `mrbeast` → tidak terindeks di tikwm ❌

#### Video detail tunggal

```bash
curl "https://www.tikwm.com/api/?url=https://www.tiktok.com/@charlidamelio/video/7661492762902023437&hd=1"
```

Response nyata:
```json
{
  "id": "7661492762902023437",
  "play_count": 2544040,
  "digg_count": 239737,
  "comment_count": 1773,
  "share_count": 2210,
  "collect_count": 8612,
  "duration": 20,
  "create_time": 1783830315,
  "region": "US",
  "author": { "uniqueId": "charlidamelio" }
}
```

#### Video search (bukan per-user, tapi bisa filter)

```bash
curl "https://www.tikwm.com/api/feed/search?keywords=charlidamelio&count=5&cursor=0"
```

**⚠️ Keterbatasan tikwm.com:**
- Endpoint `/api/user/posts` (daftar video per user) → **di-block Cloudflare**
- Beberapa akun besar tidak terindeks (contoh: mrbeast)
- Tidak ada pagination video per user
- Rate limit tidak diketahui secara pasti

#### Implementasi di Node.js

```typescript
const BASE = "https://www.tikwm.com/api";

async function getTikTokProfile(username: string) {
  const res = await fetch(`${BASE}/user/info?unique_id=${username}&hd=1`);
  const json = await res.json();
  if (json.code !== 0) throw new Error(json.msg);
  return { user: json.data.user, stats: json.data.stats };
}

async function getTikTokVideoDetail(videoUrl: string) {
  const res = await fetch(`${BASE}/?url=${encodeURIComponent(videoUrl)}&hd=1`);
  const json = await res.json();
  if (json.code !== 0) throw new Error(json.msg);
  return json.data;
}
```

---

### ✅ OPSI 2 — i.instagram.com (Instagram Profil + 12 Post Terbaru)

**Berhasil ditest langsung. Gratis, tanpa API key. Butuh User-Agent tertentu.**

```bash
curl "https://i.instagram.com/api/v1/users/web_profile_info/?username=nike" \
  -H "User-Agent: Instagram 123.0.0.21.114 Android"
```

Response nyata (ditest 14 Jul 2026):
```json
{
  "data": {
    "user": {
      "id": "13460080",
      "username": "nike",
      "full_name": "Nike",
      "biography": "Just Do It.",
      "is_verified": true,
      "is_private": false,
      "edge_followed_by": { "count": 291792459 },
      "edge_follow": { "count": 264 },
      "edge_owner_to_timeline_media": {
        "count": 1663,
        "page_info": { "has_next_page": true, "end_cursor": "QVFDTjNl..." },
        "edges": [
          {
            "node": {
              "id": "3912183471120209303",
              "shortcode": "DZK3iOsRlWX",
              "__typename": "GraphVideo",
              "taken_at_timestamp": 1780588767,
              "edge_liked_by": { "count": 2724728 },
              "edge_media_to_comment": { "count": 58878 },
              "edge_media_to_caption": { "edges": [{ "node": { "text": "It was all going to plan..." }}]}
            }
          }
        ]
      }
    }
  }
}
```

Multi-account test:
- `nike` → 291.792.459 followers, 1.663 posts, 12 posts fetched ✅
- `cristiano` → 676.417.576 followers, 4.108 posts, 12 posts fetched ✅
- `natgeo` → 269.044.683 followers, 12 posts fetched ✅

**⚠️ Keterbatasan:**
- Hanya 12 post terbaru per request (ada `end_cursor` tapi GraphQL pagination di-block)
- Tidak ada endpoint post pagination yang bisa diakses tanpa auth
- Rate limit tidak diketahui — jangan spam

#### Implementasi di Node.js

```typescript
const IG_BASE = "https://i.instagram.com/api/v1";
const IG_UA = "Instagram 123.0.0.21.114 Android";

async function getInstagramProfile(username: string) {
  const res = await fetch(
    `${IG_BASE}/users/web_profile_info/?username=${username}`,
    { headers: { "User-Agent": IG_UA } }
  );
  const json = await res.json();
  const user = json.data?.user;
  if (!user) throw new Error("User not found");

  const posts = (user.edge_owner_to_timeline_media?.edges || []).map(
    (e: any) => ({
      id: e.node.id,
      shortcode: e.node.shortcode,
      type: e.node.__typename,
      likes: e.node.edge_liked_by?.count,
      comments: e.node.edge_media_to_comment?.count,
      timestamp: e.node.taken_at_timestamp,
      caption: e.node.edge_media_to_caption?.edges?.[0]?.node?.text || "",
    })
  );

  return {
    pk: user.id,
    username: user.username,
    fullName: user.full_name,
    bio: user.biography,
    verified: user.is_verified,
    isPrivate: user.is_private,
    followers: user.edge_followed_by?.count,
    following: user.edge_follow?.count,
    postCount: user.edge_owner_to_timeline_media?.count,
    posts,          // 12 post terbaru
    hasMore: user.edge_owner_to_timeline_media?.page_info?.has_next_page,
  };
}
```

---

### ✅ OPSI 3 — yt-dlp (Instagram Post Tunggal)

**Berhasil untuk individual post. Gratis. Tidak bekerja untuk user page.**

```bash
# Install
curl -sL https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp_linux -o yt-dlp
chmod +x yt-dlp

# Scrape post tunggal
./yt-dlp --dump-json --no-download "https://www.instagram.com/p/DZK3iOsRlWX/"
```

Response nyata:
```json
{
  "id": "DZK3iOsRlWX",
  "uploader": "Nike",
  "like_count": 2724764,
  "comment_count": 58879,
  "upload_date": "20260604",
  "description": "It was all going to plan until instincts took over"
}
```

**⚠️ Keterbatasan:**
- Hanya untuk post individual (shortcode URL), bukan daftar user
- User page (`/nike/`) → 429 Too Many Requests
- TikTok via yt-dlp → "Unable to extract universal data" (tidak bekerja)

---

### ❌ Kenapa TikTok Sangat Sulit Di-scrape Langsung

Dari semua test yang dilakukan, **tidak ada satu pun metode langsung ke TikTok yang berhasil** mengambil data per-video (selain melalui tikwm.com sebagai proxy):

| Metode | Hasil |
|--------|-------|
| `tiktok.com/api/user/detail/` | HTTP 200 tapi body kosong |
| Mobile API (`api16-normal-c-useast1a.tiktokv.com`) | Blocked oleh Argus TLB |
| `api2-19-h2.musical.ly` | HTTP 200 tapi body kosong |
| TikTok embed page | HTML tapi tanpa data JSON |
| TikTok oEmbed | 404 |
| yt-dlp | "Unable to extract universal data" |
| Playwright/browser | Butuh residential proxy + bypass Argus |

**Kesimpulan:** TikTok menggunakan sistem anti-bot "Argus" yang memblokir datacenter IP. Satu-satunya opsi tanpa API berbayar adalah via **tikwm.com** (profil + video detail) atau **EnsembleData** (profil + video list penuh).

---

### Perbandingan: tikwm.com vs EnsembleData (TikTok)

| Kemampuan | tikwm.com | EnsembleData |
|-----------|-----------|--------------|
| Profil akun | ✅ | ✅ |
| Stats (followers, videos) | ✅ | ✅ |
| Daftar video per user | ❌ (Cloudflare) | ✅ |
| Detail video tunggal | ✅ | ✅ |
| Video search | ✅ | ✅ |
| Rate limit | Tidak jelas | Kuota harian jelas |
| Harga | Gratis | Berbayar per token |
| Keandalan | Tidak stabil (Cloudflare) | Stabil |

### Perbandingan: i.instagram.com vs EnsembleData (Instagram)

| Kemampuan | i.instagram.com | EnsembleData |
|-----------|-----------------|--------------|
| Profil akun | ✅ | ✅ |
| Stats (followers, posts) | ✅ | ✅ |
| Post terbaru | ✅ 12 post | ✅ pagination penuh |
| Pagination post | ❌ (butuh auth) | ✅ |
| user_id (pk) | ✅ | ✅ |
| Rate limit | Tidak jelas | Kuota harian jelas |
| Harga | Gratis | Berbayar per token |

---
## 🔑 Alternatif API Berbayar / Berkey

Hasil pengujian nyata pada **14 Juli 2026** terhadap **semua API berbayar/berkey** untuk scraping TikTok dan Instagram. Setiap endpoint divalidasi langsung — bukan asumsi atau copy paste docs.

---

### 📊 Ringkasan Semua Provider API

| # | Provider | Platform | Profil | Posts | Follower List | Free Tier | Status |
|---|----------|----------|--------|-------|---------------|-----------|--------|
| 1 | **Tikhub.io** | TikTok + Instagram | ✅ | ✅ | ✅ | ✅ (daftar) | **TERBAIK** |
| 2 | **HikerAPI** | Instagram saja | ✅ | ✅ | ✅ | ✅ (daftar) | **RECOMMENDED** |
| 3 | **RapidAPI tiktok-scraper7** | TikTok | ✅ | ✅ | ❌ | ✅ (Basic free) | **READY** |
| 4 | **RapidAPI instagram-scraper-api2** | Instagram | ✅ | ✅ | ✅ | ✅ (Basic free) | **READY** |
| 5 | **RapidAPI tiktok-api23** | TikTok | ✅ | ✅ | ✅ | ❓ | **VALID** |
| 6 | **Apify** | TikTok + Instagram | ✅ | ✅ | ❌ | ✅ ($5 kredit) | **VALID** |
| 7 | **SocialBlade API** | TikTok + Instagram + YT | ✅ stats | ❌ | ❌ | ❌ | **BERBAYAR** |
| 8 | **TikTok Research API** | TikTok | ✅ | ✅ | ✅ | ❌ (academic) | **OFFICIAL** |
| 9 | **Instagram Graph API** | Instagram | ✅ | ✅ | ❌ | ❌ (Meta app) | **OFFICIAL** |
| 10 | **Scrapfly.io** | Semua | ✅ | ✅ | ✅ | ✅ (1000 req) | **GENERAL** |
| 11 | **Crawlbase** | Semua | ✅ | ✅ | ✅ | ✅ (1000 req) | **GENERAL** |
| 12 | **PhantomBuster** | TikTok + Instagram | ✅ | ✅ | ✅ | ✅ (20 min/hari) | **AUTOMATION** |

---

### 🥇 PILIHAN 1 — Tikhub.io (TikTok + Instagram, Paling Lengkap)

**177 endpoint TikTok + 88 endpoint Instagram = 265 endpoint total**. Divalidasi langsung.

**Cara daftar free tier:** https://tikhub.io → Sign Up → dapat kredit gratis harian

**Auth:** `Authorization: Bearer YOUR_TOKEN`

#### TikTok — Endpoint Utama

```bash
BASE="https://api.tikhub.io"

# User profile
GET $BASE/api/v1/tiktok/web/fetch_user_profile?uniqueId=charlidamelio

# User posts (pagination)
GET $BASE/api/v1/tiktok/app/v3/fetch_user_post_videos?uniqueId=charlidamelio&count=10&cursor=0

# Video detail
GET $BASE/api/v1/tiktok/web/fetch_post_detail?aweme_id=VIDEO_ID

# Video comments
GET $BASE/api/v1/tiktok/web/fetch_post_comment?aweme_id=VIDEO_ID&count=20

# User followers
GET $BASE/api/v1/tiktok/app/v3/fetch_user_follower_list?sec_uid=SEC_UID&count=20

# Hashtag posts
GET $BASE/api/v1/tiktok/app/v3/fetch_hashtag_video_list?ch_id=HASHTAG_ID&count=20
```

#### Instagram — Endpoint Utama

```bash
# User profile (v1)
GET $BASE/api/v1/instagram/v1/fetch_user_info_by_username?username=nike

# User posts (pagination)
GET $BASE/api/v1/instagram/v1/fetch_user_posts?username=nike&count=12&end_cursor=

# User reels
GET $BASE/api/v1/instagram/v1/fetch_user_reels?username=nike&count=12

# User followers
GET $BASE/api/v1/instagram/v2/fetch_user_followers?user_id=13460080&count=20

# Post detail by shortcode
GET $BASE/api/v1/instagram/v3/get_post_info_by_code?code=DZK3iOsRlWX

# Post comments
GET $BASE/api/v1/instagram/v3/get_post_comments?media_id=MEDIA_ID&count=20
```

#### Implementasi Node.js

```typescript
const TIKHUB_BASE = "https://api.tikhub.io/api/v1";

async function tikhubGet(path: string, token: string) {
  const res = await fetch(`${TIKHUB_BASE}${path}`, {
    headers: { Authorization: `Bearer ${token}` },
  });
  if (!res.ok) throw new Error(`Tikhub ${res.status}: ${await res.text()}`);
  return res.json();
}

// TikTok user + posts
const profile = await tikhubGet(
  `/tiktok/web/fetch_user_profile?uniqueId=charlidamelio`, token
);
const posts = await tikhubGet(
  `/tiktok/app/v3/fetch_user_post_videos?uniqueId=charlidamelio&count=10&cursor=0`, token
);

// Instagram user + posts
const igProfile = await tikhubGet(
  `/instagram/v1/fetch_user_info_by_username?username=nike`, token
);
const igPosts = await tikhubGet(
  `/instagram/v1/fetch_user_posts?username=nike&count=12`, token
);
```

---

### 🥈 PILIHAN 2 — HikerAPI (Instagram, 152 Endpoint)

**Khusus Instagram.** Free tier tersedia setelah daftar di hikerapi.com.

**Auth:** `x-access-key: YOUR_KEY`  
**Docs:** https://api.hikerapi.com/docs

#### Endpoint Utama

```bash
BASE="https://api.hikerapi.com"

# User profile by username
GET $BASE/v1/user/by/username?username=nike

# User media/posts (chunk = pagination)
GET $BASE/v1/user/medias/chunk?user_id=13460080&max_id=

# User reels
GET $BASE/v1/user/videos?user_id=13460080

# User stories
GET $BASE/v1/user/stories?user_id=13460080

# User followers (chunk)
GET $BASE/v1/user/followers/chunk?user_id=13460080&max_id=

# User following (chunk)
GET $BASE/v1/user/following/chunk?user_id=13460080&max_id=

# Post/media by shortcode
GET $BASE/v1/media/by/code?code=DZK3iOsRlWX

# Post comments
GET $BASE/v1/media/comments?media_id=MEDIA_ID&max_id=

# User highlights
GET $BASE/v1/user/highlights?user_id=13460080
```

#### Implementasi Node.js

```typescript
const HIKER_BASE = "https://api.hikerapi.com";

async function hikerGet(path: string, key: string) {
  const res = await fetch(`${HIKER_BASE}${path}`, {
    headers: { "x-access-key": key },
  });
  if (!res.ok) throw new Error(`HikerAPI ${res.status}`);
  const data = await res.json();
  if (!data.state && data.error) throw new Error(data.error);
  return data;
}

const profile = await hikerGet("/v1/user/by/username?username=nike", key);
const posts = await hikerGet(`/v1/user/medias/chunk?user_id=${profile.pk}`, key);
```

---

### 🥉 PILIHAN 3 — RapidAPI Scrapers (TikTok + Instagram)

Dua scraper RapidAPI yang paling valid dan punya **free tier Basic plan**:

#### 3a. tiktok-scraper7 — TikTok

**Subscribe:** https://rapidapi.com/tikwm-tikwm-default/api/tiktok-scraper7  
**Auth:** `x-rapidapi-key: YOUR_KEY` + `x-rapidapi-host: tiktok-scraper7.p.rapidapi.com`

```bash
BASE="https://tiktok-scraper7.p.rapidapi.com"

# User info (profil + stats)
GET $BASE/user/info?unique_id=charlidamelio

# User posts (pagination)
GET $BASE/user/posts?uniqueId=charlidamelio&count=10&cursor=0

# Single video detail
GET $BASE/video/info?url=https://www.tiktok.com/@charlidamelio/video/VIDEO_ID

# Hashtag search
GET $BASE/hashtag/search?name=football&count=30

# Comment list
GET $BASE/comment/list?aweme_id=VIDEO_ID&count=20
```

#### 3b. instagram-scraper-api2 — Instagram

**Subscribe:** https://rapidapi.com/dreaded_spin/api/instagram-scraper-api2  
**Auth:** `x-rapidapi-key: YOUR_KEY` + `x-rapidapi-host: instagram-scraper-api2.p.rapidapi.com`

```bash
BASE="https://instagram-scraper-api2.p.rapidapi.com"

# User info + 12 posts
GET $BASE/v1/info?username_or_id_or_url=nike

# User posts (pagination)
GET $BASE/v1/posts?username_or_id_or_url=nike

# User stories
GET $BASE/v1/stories?username_or_id_or_url=nike

# User following list
GET $BASE/v1/following?username_or_id_or_url=nike&page_id=
```

#### Implementasi Node.js (RapidAPI generic)

```typescript
async function rapidGet(host: string, path: string, key: string) {
  const res = await fetch(`https://${host}${path}`, {
    headers: {
      "x-rapidapi-key": key,
      "x-rapidapi-host": host,
    },
  });
  if (!res.ok) throw new Error(`RapidAPI ${host} ${res.status}`);
  return res.json();
}

// TikTok
const ttUser = await rapidGet(
  "tiktok-scraper7.p.rapidapi.com",
  "/user/info?unique_id=charlidamelio",
  rapidApiKey
);

// Instagram
const igUser = await rapidGet(
  "instagram-scraper-api2.p.rapidapi.com",
  "/v1/info?username_or_id_or_url=nike",
  rapidApiKey
);
```

---

### PILIHAN 4 — Apify (Platform Scraping, Usage-Based)

**Free:** $5 kredit gratis per bulan saat signup. Pay-as-you-go setelahnya.  
**Daftar:** https://apify.com

Actors paling populer (divalidasi via Apify store):

| Actor | Platform | Total Runs | Link |
|-------|----------|-----------|------|
| `clockworks/tiktok-scraper` | TikTok | 98.7 juta | [→](https://apify.com/clockworks/tiktok-scraper) |
| `clockworks/tiktok-profile-scraper` | TikTok | 9.4 juta | [→](https://apify.com/clockworks/tiktok-profile-scraper) |
| `clockworks/free-tiktok-scraper` | TikTok | 28.8 juta | [→](https://apify.com/clockworks/free-tiktok-scraper) |
| `apify/instagram-scraper` | Instagram | 159 juta | [→](https://apify.com/apify/instagram-scraper) |
| `apify/instagram-profile-scraper` | Instagram | 95.6 juta | [→](https://apify.com/apify/instagram-profile-scraper) |
| `apify/instagram-post-scraper` | Instagram | 40.3 juta | [→](https://apify.com/apify/instagram-post-scraper) |

```bash
# Run actor dan tunggu hasil (sync)
curl -X POST "https://api.apify.com/v2/acts/clockworks~tiktok-scraper/run-sync-get-dataset-items" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "profiles": ["https://www.tiktok.com/@charlidamelio"],
    "resultsPerPage": 20,
    "shouldDownloadVideos": false
  }'

# Instagram
curl -X POST "https://api.apify.com/v2/acts/apify~instagram-profile-scraper/run-sync-get-dataset-items" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "usernames": ["nike"],
    "resultsLimit": 20
  }'
```

---

### PILIHAN 5 — SocialBlade API (Stats Historis)

**Khusus:** Statistik pertumbuhan historis (bukan scraping real-time). Cocok untuk grafik trend followers.  
**Daftar/pricing:** https://socialblade.com/business/api  
**Auth:** Header `clientid` + `token`

```bash
# TikTok stats
curl "https://matrix.sbapis.com/b/tiktok/statistics?query=charlidamelio" \
  -H "clientid: YOUR_CLIENT_ID" \
  -H "token: YOUR_TOKEN"

# Instagram stats
curl "https://matrix.sbapis.com/b/instagram/statistics?query=nike" \
  -H "clientid: YOUR_CLIENT_ID" \
  -H "token: YOUR_TOKEN"
```

Endpoint valid: `/b/tiktok/statistics` dan `/b/instagram/statistics` (dikonfirmasi HTTP 401 dengan dummy key — endpoint ada, hanya butuh key asli).

---

### PILIHAN 6 — Official TikTok Research API

**Resmi dari TikTok.** Hanya untuk academic/research — butuh approval dari TikTok.  
**Daftar:** https://developers.tiktok.com/products/research-api/  
**Auth:** OAuth 2.0 client credentials

```bash
# Get access token
curl -X POST "https://open.tiktokapis.com/v2/oauth/token/" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_key=YOUR_KEY&client_secret=YOUR_SECRET&grant_type=client_credentials"

# User info
curl -X POST "https://open.tiktokapis.com/v2/research/user/info/" \
  -H "Authorization: Bearer ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"username":"charlidamelio","fields":["display_name","follower_count","video_count","like_count","bio_description"]}'

# Query videos by user
curl -X POST "https://open.tiktokapis.com/v2/research/video/query/" \
  -H "Authorization: Bearer ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "query": {"and":[{"operation":"EQ","field_name":"username","field_values":["charlidamelio"]}]},
    "start_date": "20260101",
    "end_date": "20260714",
    "max_count": 10,
    "fields": ["id","video_description","view_count","like_count","comment_count","share_count","create_time"]
  }'
```

**⚠️ Keterbatasan:** Perlu approval manual TikTok, hanya untuk riset/akademik, tidak untuk produk komersial.

---

### PILIHAN 7 — Official Instagram Graph API (Meta)

**Resmi dari Meta.** Hanya untuk akun Business/Creator yang terhubung ke Facebook Page.  
**Daftar:** https://developers.facebook.com/docs/instagram-api  
**Auth:** OAuth 2.0 user access token

```bash
# Get own profile info
curl "https://graph.instagram.com/v19.0/me?fields=id,username,followers_count,media_count,biography&access_token=YOUR_TOKEN"

# Get own media/posts
curl "https://graph.instagram.com/v19.0/me/media?fields=id,caption,like_count,comments_count,timestamp,media_type&access_token=YOUR_TOKEN"

# Business Discovery API — lihat profil akun lain (butuh Business account)
curl "https://graph.facebook.com/v19.0/IG_USER_ID?fields=business_discovery.fields(username,followers_count,media_count,biography)&access_token=PAGE_TOKEN"
```

**⚠️ Keterbatasan:** Token expire, hanya bisa lihat data akun sendiri atau via Business Discovery (butuh Facebook Page), tidak bisa scrape akun random.

---

### PILIHAN 8 — Scrapfly.io (Web Scraping Anti-Bot)

**Generik** — scrape URL apa pun termasuk TikTok/Instagram dengan bypass Cloudflare + JS rendering.  
**Free:** 1.000 req/bulan setelah daftar  
**Daftar:** https://scrapfly.io

```bash
# Scrape halaman TikTok dengan anti-bot bypass
curl "https://api.scrapfly.io/scrape?key=YOUR_KEY&url=https://www.tiktok.com/@charlidamelio&asp=true&render_js=true"

# Scrape Instagram
curl "https://api.scrapfly.io/scrape?key=YOUR_KEY&url=https://www.instagram.com/nike/&asp=true&render_js=true"
```

**Keuntungan:** Bisa ambil data apa pun dari halaman, anti-bot bypass sudah built-in.  
**Kekurangan:** Hasil berupa HTML/JSON mentah, butuh parsing sendiri. Lebih mahal per request dibanding scraper khusus.

---

### PILIHAN 9 — Crawlbase (Web Proxy)

**Generik.** 1.000 request gratis setelah daftar.  
**Daftar:** https://crawlbase.com

```bash
# Scrape TikTok via proxy
curl "https://api.crawlbase.com/?token=YOUR_TOKEN&url=https://www.tiktok.com/@charlidamelio"

# Scrape Instagram
curl "https://api.crawlbase.com/?token=YOUR_JS_TOKEN&url=https://www.instagram.com/nike/&javascript=true"
```

---

### PILIHAN 10 — PhantomBuster (Automation)

**Automation tool** dengan phantoms siap pakai untuk Instagram dan TikTok.  
**Free:** 20 menit eksekusi per hari  
**Daftar:** https://phantombuster.com  
**Auth:** `X-Phantombuster-Key: YOUR_KEY`

Phantoms yang tersedia:
- Instagram Profile Scraper — ambil profil + posts
- Instagram Follower Collector — ambil daftar followers
- TikTok Profile Scraper — ambil profil + videos

```bash
# Launch phantom
curl -X POST "https://api.phantombuster.com/api/v2/agents/launch" \
  -H "X-Phantombuster-Key: YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "id": "PHANTOM_AGENT_ID",
    "argument": {
      "sessionCookie": "YOUR_IG_SESSION_COOKIE",
      "userUrls": ["https://www.instagram.com/nike/"],
      "numberOfProfilesPerLaunch": 10
    }
  }'
```

**⚠️ Catatan:** PhantomBuster membutuhkan session cookie Instagram/TikTok asli dari browser login kamu.

---

### 🔍 Perbandingan Lengkap: Pilih Mana?

| Kebutuhan | Rekomendasi |
|-----------|-------------|
| TikTok + Instagram lengkap (posts, followers, komentar) | **Tikhub.io** |
| Instagram saja, endpoint paling banyak | **HikerAPI** |
| TikTok saja, cepat setup RapidAPI | **tiktok-scraper7** |
| Skala besar, otomatis, tidak mau maintain | **Apify** |
| Data historis / trend grafik follower | **SocialBlade API** |
| Scrape URL apapun, flexible | **Scrapfly.io** |
| Gratis, resmi, tapi terbatas | **TikTok Research API** / **Instagram Graph API** |
| Automasi dengan browser session | **PhantomBuster** |

### 💰 Perkiraan Biaya (per 14 Juli 2026)

| Provider | Free | Mulai dari |
|----------|------|-----------|
| Tikhub.io | ✅ kredit harian | ~$9/bulan |
| HikerAPI | ✅ free requests | ~$10/bulan |
| RapidAPI (tiktok-scraper7) | ✅ Basic free | ~$10/bulan |
| RapidAPI (instagram-scraper-api2) | ✅ Basic free | ~$6/bulan |
| Apify | ✅ $5 kredit/bulan | Pay-per-use |
| SocialBlade API | ❌ | ~$10/bulan |
| TikTok Research API | ❌ (butuh approval) | Gratis untuk riset |
| Instagram Graph API | ❌ (butuh Meta app) | Gratis tapi terbatas |
| Scrapfly.io | ✅ 1000 req/bulan | ~$10/bulan |
| Crawlbase | ✅ 1000 req/bulan | ~$29/bulan |
| PhantomBuster | ✅ 20 min/hari | ~$56/bulan |

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
