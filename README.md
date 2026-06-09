# 🥊 Battle Ring — TikTok Live Arena Battle

Game pertarungan 3D real-time untuk TikTok Live. Viewer menjadi karakter dan bertarung di arena. Gift, like, chat, dan follow dari viewer memicu efek visual di game.

## ✨ Fitur

- **Karakter 3D** — DJT, NEO, VP, Soldier (GLB models dengan animasi)
- **Arena Dinamis** — 5 tema (Langit Biru, Sunset, Malam, Hutan, Gurun)
- **Bot System** — 2-7 bot otomatis, nama Indonesia bervariasi
- **Gift Effects** — Ledakan, tornado, meteor, petir sesuai tier gift
- **TTS Announcer** — Pengumuman gaya pembaca tinju Indonesia
- **Auto Camera** — 5 mode kamera (dramatic, orbit, follow, cinematic, low_orbit)
- **Rate Limiting** — Token bucket per event type (stabil di stream viral)
- **Memory Management** — Three.js object disposal otomatis (stabil berjam-jam)

## 🚀 Instalasi

```bash
# Clone repository
git clone https://github.com/kiozhu/battle-ring.git
cd battle-ring

# Install dependencies
npm install

# Buat file .env
cp .env.example .env
```

### Konfigurasi `.env`

```env
PORT=5051
TIKTOK_USERNAME=username_tiktok_kamu
EULER_API_KEY=kunci_api_euler_kamu
```

### Jalankan

```bash
# Development (auto-restart)
npm run dev

# Production
npm start

# Dengan PM2
pm2 start server.js --name battle-ring
```

## 🎮 Cara Pakai

### 1. Buka Game
```
http://localhost:5051/battle-ring
```

### 2. Hubungkan ke TikTok Live
- Klik tombol 🔗 **Connect TikTok** di pojok kanan atas
- Masukkan username TikTok yang sedang live
- Klik **Connect**

Atau langsung via URL:
```
http://localhost:5051/battle-ring?room=username_tiktok
```

### 3. Interaksi Viewer
| Aksi Viewer | Efek di Game |
|-------------|--------------|
| Join | Karakter masuk arena |
| Chat | Chat bubble di atas karakter |
| Like | Efek hati, push musuh |
| Gift kecil | Power Up |
| Gift sedang | Ground Slam |
| Gift besar | Shockwave Slam |
| Gift VIP | Meteor Spin |
| Follow | Efek bintang |

## 🏗️ Arsitektur

```
TikTok Live → tiktok-live-connector → Node.js Server → Socket.IO → Browser (Three.js)
```

### Komponen Utama

| Komponen | Teknologi | Fungsi |
|----------|-----------|--------|
| Server | Node.js + Express | HTTP server, API endpoints |
| Real-time | Socket.IO | Komunikasi server ↔ browser |
| 3D Engine | Three.js | Render karakter, arena, efek |
| TikTok | tiktok-live-connector | Koneksi ke TikTok Live |
| Signing | EulerStream API | WebSocket URL signing |
| TTS | Web Speech API | Suara announcer |

### Struktur File

```
├── server.js              # Server utama (Express + Socket.IO)
├── public/
│   ├── battle-ring.html   # Game client (Three.js)
│   ├── index.html         # Dashboard
│   ├── overlay.html       # OBS overlay
│   ├── voice.html         # AI voice avatar
│   └── assets/
│       ├── base.glb       # Model arena
│       └── characters/
│           ├── DJT/       # Karakter DJT
│           ├── NEO/       # Karakter NEO
│           ├── VP/        # Karakter VP
│           └── Soldier/   # Karakter Soldier
├── .env                   # Konfigurasi
└── package.json           # Dependencies
```

## 🛡️ Stabilitas

### Fitur Anti-Crash

| Fitur | Fungsi |
|-------|--------|
| Token Bucket Rate Limiting | Batasi events per tipe (prevent overwhelming browser) |
| Three.js Memory Dispose | Bersihkan GPU memory otomatis (prevent memory leak) |
| Cap Viewers Map | Batasi 500 viewers per session (prevent OOM) |
| Ocean Optimization | Update gelombang tiap 3 frame (hemat CPU 66%) |
| Cache Alive Players | 0 array allocations per detik (hilangkan micro-stutter) |
| Exponential Backoff | Reconnect otomatis 2s→4s→8s→16s→30s (max 5 attempts) |
| Graceful Shutdown | Bersih saat SIGINT/SIGTERM |

### Rate Limits

| Event | Burst | Sustain | Drop? |
|-------|-------|---------|-------|
| member | 8 | 4/detik | Ya |
| chat | 20 | 12/detik | Ya |
| like | 4 | 2/detik | Ya |
| gift | 30 | 15/detik | **Tidak** |
| follow | 10 | 5/detik | Ya |
| share | 6 | 3/detik | Ya |

## 🎨 Arena Themes

| Tema | Warna |
|------|-------|
| Langit Biru | Biru muda, laut biru |
| Sunset | Ungu-pink, laut gelap |
| Malam Hari | Biru tua, laut hitam |
| Hutan | Hijau, laut hijau tua |
| Gurun | Oranye, laut coklat |

## 🤖 Bot System

- **Auto-start** — Bot spawn otomatis setelah 5 detik jika tidak ada viewer
- **Jumlah bervariasi** — 2-7 bot (random)
- **Nama Indonesia** — 100+ nama bervariasi (Budi, Andi, Sari, dll)
- **Karakter random** — DJT, NEO, VP, atau Soldier
- **Auto-respawn** — Jika semua bot mati, spawn 1-4 bot baru (max 5× per round)

## 🔧 API Endpoints

| Method | Endpoint | Fungsi |
|--------|----------|--------|
| GET | `/battle-ring` | Game client |
| GET | `/` | Dashboard |
| GET | `/overlay` | OBS overlay |
| POST | `/api/connect` | Hubungkan ke TikTok Live |
| GET | `/api/config` | Konfigurasi server |

## 📦 Dependencies

| Package | Versi | Fungsi |
|---------|-------|--------|
| express | ^5.2.1 | HTTP server |
| socket.io | ^4.8.3 | Real-time communication |
| tiktok-live-connector | ^2.1.1-beta1 | TikTok Live connection |
| dotenv | ^16.4.7 | Environment variables |

## 🐛 Troubleshooting

### Game tidak bisa connect ke TikTok
- Pastikan `EULER_API_KEY` di `.env` sudah benar
- Pastikan username TikTok sedang live
- Cek server logs: `pm2 logs battle-ring`

### Game lag/stutter
- Pastikan browser tidak banyak tab
- Cek FPS di Chrome DevTools → Performance
- Kurangi jumlah bot jika terlalu banyak

### Server crash
- Cek logs: `pm2 logs battle-ring --lines 50`
- Restart: `pm2 restart battle-ring`

## 📄 License

MIT

## 🙏 Credits

- [tiktok-live-connector](https://github.com/zerodytrash/TikTok-Live-Connector) — TikTok Live connection library
- [Three.js](https://threejs.org/) — 3D rendering engine
- [KayKit](https://kaylousberg.itch.io/) — Character models (DJT, NEO, VP)
