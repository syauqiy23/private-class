# Plan Kelas Private — 5 Paket Result

Terakhir diupdate: 2026-10-26 (harga v2) · Status: **draf, belum ada peserta**

## Ringkasan

Fokus jualan **private dulu**, ecourse/LMS ditunda. Private dijual per **result** (sistem yang jalan di akun
peserta), bukan per materi. Ada 5 result. Semuanya jalan otomatis **tanpa VPS, tanpa langganan platform
tambahan, dan laptop nggak perlu nyala**.

Diferensiasi utama: **datanya 100% real, bukan tebakan.** Data iklan diambil langsung dari Meta Ads, data order
dan transfer diambil langsung dari Scalev, dan owner cukup nanya lewat WhatsApp.

Target peserta: advertiser, IMers, pemain Meta Ads, dan owner bisnis online yang jualan lewat Scalev.

## 5 paket result

| # | Paket | Hasil di akun peserta | Basis sistem Syauqiy (studi kasus) |
| --- | --- | --- | --- |
| R1 | **Ads Intelligence** — Meta Ads × Bot AI WA × Scalev | Nanya di WA → dapat laporan real: spend, closing, uang yang udah ditransfer, ROAS & CPP real | Agent iklan Meta + laporan Telegram (Wowo), data Scalev |
| R2 | **AI Agent Komen & DM** | Semua komen & DM di IG, FB, Threads dibalas AI 24 jam | Bot Threads, auto-reply komen |
| R3 | **Content Automation** (tim agent) | Riset → content plan → produksi konten → distribusi otomatis ke tiap channel | Sistem IG SantriPreneur (tim Raka, Dira, Fajar, Ilham) |
| R4 | **Order Automation** (CS AI WABA) | CS AI di WhatsApp: cek stok, cek status order, bikin order di Scalev, kirim akses produk | WA CS SantriPreneur, data Scalev |
| R5 | **Ads Automation** (tim agent) | Riset → strategi → ad creative → bikin campaign → report & evaluasi harian | Wiwi (konten ads) + Wowo (Ads Specialist), Gigib (copywriter) |

### R1 — Ads Intelligence (Meta Ads × Bot AI WA × Scalev)

Ini paket unggulan, karena masalahnya paling kerasa buat advertiser: angka di dashboard Meta beda sama duit yang
beneran masuk.

Yang dibangun:

1. Koneksi Meta Marketing API (insights per campaign/adset/ad: spend, impresi, klik, hasil).
2. Koneksi Scalev API (order, status bayar, nominal transfer, produk).
3. Bot WA (nomor WABA peserta). Owner nanya bebas, misalnya *"laporan kemarin"*, *"campaign mana yang boncos minggu ini?"*,
   atau *"ROAS real produk X bulan ini"*.
4. Laporan harian otomatis ke WA setiap pagi: spend, order masuk, order lunas, uang yang udah ditransfer, ROAS real, CPP real, dan
   campaign terbaik/terburuk.
5. Rumus dan definisi yang dikunci: *closing* = order berstatus lunas di Scalev, *ROAS real* = total transfer ÷ spend Meta.

Output: owner bisa nanya performa iklan pakai data real dari WA, plus dapat laporan pagi otomatis.

Catatan teknis:
- **Atribusi per campaign: bisa.** Order Scalev nyimpen `utm_campaign`, `utm_content`, `utm_term` dari iklan Meta
  (terbukti di data real, lihat `riset-api-scalev.md`). Syaratnya, URL iklan peserta wajib pakai parameter UTM yang konsisten.
- Pesan WA masuk **nggak** lewat webhook Scalev, jadi bot butuh webhook Meta sendiri atau nomor terpisah (lihat riset).
- Pesan yang dikirim duluan oleh bot (laporan pagi) di luar jendela 24 jam = **template message WABA, ada biaya per pesan**.

### R2 — AI Agent Komen & DM

1. Persona & rules balasan: sapaan, topik terlarang, CTA, dan kapan harus dioper ke manusia.
2. Webhook Meta buat IG (komen + DM), FB Page (komen + Messenger), dan Threads (reply).
3. Balasan pakai Claude Haiku + prompt caching biar murah.
4. Eskalasi: pertanyaan yang nggak bisa dijawab diteruskan ke WA/Telegram admin.
5. Log semua balasan (Supabase) buat evaluasi.

Output: semua komen & DM dibalas AI 24 jam di akun peserta.

### R3 — Content Automation (tim agent)

1. Agent Riset: tren, konten viral, kompetitor, persona.
2. Agent Planner: content plan 30 hari ke Google Sheets/Docs.
3. Agent Produksi: copy carousel/caption/naskah video faceless, VO AI (Cartesia), render (HyperFrames/Remotion).
4. Agent Distribusi: autopost ke IG/TikTok/FB/Threads (Zernio free tier atau API resmi), anti-dobel, log gagal.
5. Laporan harian ke WA/Telegram.

Output: akun peserta posting otomatis minimal 7 hari berturut-turut.

### R4 — Order Automation (CS AI WABA)

1. WhatsApp Cloud API langsung dari Meta (tanpa BSP berbayar).
2. CS AI jawab pertanyaan produk dari katalog Scalev.
3. Cek stok, cek status order, bikin order baru di Scalev dari chat.
4. Kirim akses produk digital otomatis setelah lunas.
5. Notif bukti transfer ke admin + label customer.

Output: CS WA jalan 24 jam dan bisa closing sampai kirim akses tanpa admin.

### R5 — Ads Automation (tim agent)

1. Agent Riset: Ads Library, angle kompetitor, persona.
2. Agent Strategi: struktur campaign, budget, testing plan.
3. Agent Creative: copy (skill copywriter), visual/video ads.
4. Agent Campaign: bikin campaign via Meta Ads API **dengan status PAUSED**, lalu manusia yang approve sebelum aktif.
5. Report & evaluasi harian (pakai fondasi R1) dengan rekomendasi scale/kill yang dikirim ke WA/Telegram buat di-approve.

Output: tim agent iklan jalan, semua aksi yang ngeluarin duit tetap lewat approval manusia.

Aturan keras: **agent tidak boleh mengaktifkan campaign atau naikin budget tanpa approval.** Ini akses ke duit
peserta.

## Arsitektur: "tanpa VPS, laptop mati"

Kerjaannya dibagi 2 jenis, dan masing-masing jalan di tempat yang beda:

| Jenis kerja | Contoh | Jalan di | Biaya platform |
| --- | --- | --- | --- |
| **Terjadwal** (batch) | Laporan pagi R1, riset & produksi konten R3, evaluasi harian R5 | Scheduled task / routine Claude (cloud), pakai connector (Meta Ads, Scalev, Google, dll) | Termasuk langganan Claude peserta |
| **Real-time** (reaksi ke pesan) | Balas WA R1/R4, balas komen & DM R2 | Serverless function (Supabase Edge Functions atau Cloudflare Workers) yang nerima webhook Meta, lalu manggil Claude API | Free tier cukup buat skala UMKM **(cek ulang limit)** |

Data dan token disimpan di Supabase (free tier). Token API disimpan aman di sana, bukan ditanam di kode.

**Jujur ke peserta soal biaya.** "Tanpa langganan platform lain" itu bener (nggak perlu ManyChat, BSP, Zapier,
atau VPS), tapi tetap ada biaya pemakaian:
- Langganan Claude (Pro/Max), buat scheduled task.
- Anthropic API (Haiku), buat balasan real-time. Bayarnya per pemakaian.
- WABA: template message (pesan duluan, misalnya laporan pagi) berbayar per pesan, sedangkan balasan dalam jendela
  24 jam umumnya gratis **(cek harga Meta terbaru sebelum jualan)**.
- Cartesia (VO) dan budget iklan, sesuai pemakaian.

## Prasyarat peserta (wajib beres sebelum sesi 1)

Ini buat ngurangin risiko sesi nganggur gara-gara nunggu Meta.

| Prasyarat | Dibutuhkan paket | Kenapa |
| --- | --- | --- |
| Business Manager Meta terverifikasi | R1, R2, R4, R5 | Akses API & WABA |
| Meta App (mode live) + izin yang dibutuhkan | R1, R2, R4, R5 | App review bisa berminggu-minggu |
| Nomor WA khusus bisnis (belum dipakai di WA app biasa) | R1, R4 | Syarat WhatsApp Cloud API |
| Akun Scalev aktif + API key, **plan Basic ke atas** | R1, R4 | Sumber data order & transfer; webhook cuma ada di Basic+ |
| URL iklan Meta pakai UTM konsisten (campaign, content, term) | R1, R5 | Biar order bisa diatribusi per campaign/iklan |
| Akun Claude Pro/Max + kunci API Anthropic | Semua | Scheduled task + real-time |
| Akun Supabase/Cloudflare (gratis) | R1, R2, R4 | Webhook & database |
| Ad account dengan riwayat spend | R1, R5 | Data buat dianalisa |

Kalau app review Meta belum lolos, sesi awal dipakai buat bagian yang nggak bergantung ke review itu (Scalev,
persona, scheduled task) atau pakai jalur no-code dulu.

## Format private (lanjutan dari plan v1)

- 1 sesi = 2,5–3 jam (online atau offline). Sebelum sesi 1, peserta isi form intake.
- Di antara sesi: PR + support WhatsApp 30 hari.
- Nggak ada rekaman. Peserta dapat catatan sesi + file skill/prompt.
- Form intake **wajib ada izin**: boleh/tidaknya bisnis peserta dipakai sebagai studi kasus (anonim) buat materi nanti.
- Kapasitas: maksimal 4–6 peserta private aktif per bulan.

## Urutan bangun & bundling

R1 dan R4 sama-sama pakai fondasi **WABA + Scalev**, sedangkan R5 pakai data R1. Jadi urutan yang efisien:

```
Fondasi (WABA + Scalev + Supabase) ──► R1 Ads Intelligence ──► R5 Ads Automation
                                   └──► R4 Order Automation
Meta webhook komen/DM ─────────────────► R2 AI Agent Komen & DM
Scheduled task + connector ────────────► R3 Content Automation
```

Ide bundle:
- **Paket Advertiser** = R1 + R5 (target utama: advertiser/IMers).
- **Paket Jualan Otomatis** = R4 + R2.
- **Full System** = R1–R5.

## Harga v2 (berbasis riset pasar, belum dikunci)

Draf v1 ternyata kemahalan sekitar 20–30% dan belum ada harga perkenalan. Data pembanding dan analisanya ada di
`riset-pricing.md`. Harga di bawah buat **online**; offline +Rp1–1,5jt per paket. **Belum dites ke calon peserta.**

| Paket | Sesi | Founding (5 peserta pertama) | Normal |
| --- | --- | --- | --- |
| Konsultasi / audit | 1 | Rp500rb | Rp750rb |
| R1 Ads Intelligence | 3 | Rp3,5jt | Rp5jt |
| R2 AI Agent Komen & DM | 3 | Rp2,5jt | Rp3,5jt |
| R3 Content Automation | 3 | Rp2,5jt | Rp3,5jt |
| R4 Order Automation | 4 | Rp4jt | Rp6jt |
| R5 Ads Automation | 4 | Rp5jt | Rp7,5jt |
| Paket Advertiser (R1 + R5) | 6 | Rp7,5jt | Rp11jt |
| Full System (R1–R5) | 12 | Rp15jt | Rp20jt |

Opsi buat market sensitif harga: private grup kecil (3–5 orang) Rp1,5–2jt per orang, terutama buat R2/R3.

Aturan harga yang dibawa dari v1:
- Harga konsultasi memotong harga paket kalau peserta lanjut dalam 14 hari.
- Tim 2–3 orang dari 1 bisnis kena tambahan 30%.
- Biaya tools ditanggung peserta.
- Pembayaran: DP 50%, sisanya dilunasi sebelum sesi terakhir.

## Pertanyaan terbuka

- [x] ~~Scalev nyimpen UTM?~~ **Ya**, lengkap di `metadata` order (2026-10-26, `riset-api-scalev.md`).
- [x] ~~Scalev API bisa bikin order?~~ **Ya**, `POST /v3/orders` + kirim akses digital. Stok kemungkinan bisa, belum dites di produk fisik.
- [ ] `utm_term` di order itu campaign id atau adset id Meta? (buat join spend ↔ order)
- [ ] Inbound WA: app Meta sendiri ikut subscribe ke WABA yang udah nyambung Scalev, atau pakai nomor terpisah?
- [ ] Harga final per paket. Draf v2 udah ada (`riset-pricing.md`), tinggal dites ke 5 calon peserta pakai harga founding.
- [ ] Cek ulang langsung harga pembanding utama (Raya School, Insting, Majapahit) sebelum dikunci.
- [ ] Limit free tier Supabase/Cloudflare + estimasi biaya API per bulan buat 1 bisnis (biar bisa dijelasin ke peserta).
- [ ] Harga template message WABA Indonesia terbaru.
- [ ] Mau buka paket mana duluan? (usulan: R1 dulu, karena paling beda dan paling nyambung ke target advertiser)

## Next steps

- [x] Cek API Scalev (UTM, order, stok, webhook, WABA) → `riset-api-scalev.md`.
- [x] Riset & analisa pricing pasar → `riset-pricing.md` (harga v2).
- [ ] Bikin demo R1 di bisnis sendiri: screenshot/video chat WA "laporan kemarin" pakai data real. Ini bahan jualan utama.
- [ ] Susun form intake + checklist prasyarat.
- [ ] Tawarkan ke 5 calon peserta (DM Threads yang udah nanya), terus kunci harga.
- [ ] Bikin LP Scalev buat Konsultasi + paket private.
