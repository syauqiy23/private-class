# Log Keputusan

Terbaru di atas. Format: tanggal — keputusan — alasan.

## 2026-10-27 — Harga flat Rp1.490.000 per topik + angle "buat yang nggak sabar"

- **Keputusan (owner):** semua topik private dihargai sama, Rp1.490.000. Belum ada materi rekaman, jadi privat dijual
  ke yang nggak sabar. Harga diposisikan sebagai tuker waktu, pengalaman, dan ilmu owner (owner bukan pengangguran dan nggak
  cari nafkah dari jualan kelas).
- **Asumsi di copy (belum dikonfirmasi):** 2 sesi live @ ±2,5 jam online + support WA 14 hari. Kalau dibikin 3–4 sesi,
  harga per sesi jatuh di bawah pasar dan bentrok sama positioning.
- **Output:** `copy-private.md`.

## 2026-10-26 — Harga private direvisi ke v2 (usulan, belum dikunci)

- **Keputusan:** draf harga v1 diganti v2 yang berbasis riset pasar: founding Rp2,5–5jt per paket, normal Rp3,5–7,5jt,
  Full System Rp15jt/Rp20jt.
- **Alasan:** v1 cuma diturunin dari plan lama tanpa pembanding pasar, hasilnya kemahalan sekitar 20–30% buat yang belum punya
  testimoni. Pembandingnya private Meta Ads (Rp2–7,5jt), kelas AI (Rp250rb–2,5jt), dan jasa bot (Rp5–60jt).
- **Belum final:** dites dulu ke 5 calon peserta. Detail di `riset-pricing.md`.
- **Catatan:** fokus diskusi digeser dari riset teknis ke persiapan jualan (offer, konten, LP, script DM). Riset teknis
  lanjutan (cocokin `utm_term`, opsi inbound WA) ditunda sampai ada peserta.

## 2026-10-26 — Scalev dipakai sebagai sumber data order buat R1 & R4

- **Temuan:** order Scalev nyimpen UTM lengkap dari iklan Meta, jadi atribusi per campaign/iklan bisa. Bikin order,
  cek status, kirim akses digital, dan webhook pembayaran juga tersedia.
- **Belum diputuskan:** jalur WA masuk (Scalev nggak nge-forward pesan WA masuk lewat webhook).
- **Detail:** `riset-api-scalev.md`.

## 2026-10-26 — Fokus private dulu, ecourse/LMS ditunda

- **Keputusan:** jualan private dulu. Ecourse dan LMS (rekaman) ditunda.
- **Alasan:** belum ada waktu buat rekaman.
- **Dampak:** plan v1 (ecourse + private) dipindah ke `arsip-kurikulum-v1.md`.

## 2026-10-26 — Private dijual per 5 result

- **Keputusan:** private terdiri dari 5 paket result: R1 Ads Intelligence (Meta Ads × WA × Scalev),
  R2 AI Agent Komen & DM, R3 Content Automation, R4 Order Automation (WABA), dan R5 Ads Automation.
- **Alasan:** dijual per hasil yang jalan di akun peserta, dengan data real (Meta + Scalev), bukan tebakan. Target utamanya
  advertiser, IMers, dan pemain Meta Ads.
- **Detail:** `plan-private.md`.

## 2026-10-26 — Arsitektur tanpa VPS

- **Keputusan:** kerja terjadwal jalan di scheduled task Claude (cloud), sedangkan kerja real-time (WA, komen, DM)
  jalan di serverless (Supabase Edge Functions / Cloudflare Workers, free tier). Laptop nggak perlu nyala.
- **Catatan:** tetap ada biaya pemakaian (Claude, Anthropic API, template WABA). Ini wajib dijelasin jujur ke peserta.

## 2026-10-26 — Repo ini jadi sumber kebenaran

- **Keputusan:** semua plan dan hasil diskusi disimpan sebagai Markdown di repo `private-class`.
- **Alasan:** biar siapa pun yang punya akses repo bisa lanjut tanpa ngulang chat.

## 2026-10-23 — Plan v1: kurikulum & pricing ecourse + private

- Disusun di doc Claude "Kurikulum & Pricing Kelas AI" (https://claude.ai/artifact/MXG1FnNtYCn5ARb2wZQ3Jt).
- Salinannya ada di `arsip-kurikulum-v1.md`. Status sekarang: ditunda.
