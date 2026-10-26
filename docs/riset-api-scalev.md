# Riset API Scalev

Dicek: 2026-10-26 · Akun: bisnis **Waroengslide** (`waroeng`) lewat Scalev MCP (OAuth) · API v3 (260 endpoint)

## Kesimpulan

Scalev **cukup buat R1 dan R4**, termasuk atribusi per campaign. Data UTM dari iklan Meta udah kesimpen di tiap
order, jadi laporan "campaign A spend sekian, lunas sekian" bisa dibangun dari data real.

Satu hal yang perlu diputusin: **WhatsApp masuk (inbound) nggak dikirim lewat webhook Scalev**, jadi bot WA real-time
harus nerima webhook langsung dari Meta (lihat *Risiko*).

## Status per kebutuhan

| Kebutuhan | Paket | Status | Endpoint / cara |
| --- | --- | --- | --- |
| Daftar order + filter tanggal, status, bayar | R1, R4 | Bisa | `GET /v3/orders` (maks 25/halaman, cursor), filter `confirmed_time_since/until`, `payment_status`, `status`, `utm_source`, `page_id`, `product_id` |
| Atribusi iklan per order | R1 | **Bisa, terbukti di data** | `metadata.utm_campaign`, `utm_content`, `utm_medium`, `utm_term`, `utm_source`, plus `fbc`/`fbp` |
| Uang yang beneran masuk | R1 | Bisa | `payment_status` (`paid`/`settled`), `gross_revenue`, `net_revenue`, `net_payment_revenue`, `payment_fee`, `transfer_time`, `transferproof_url` |
| Statistik agregat | R1 | Bisa | `GET /v3/orders/statistics` (`tz=Asia/Jakarta`, `is_breakdown_status`, `breakdown_date`) |
| Daftar nilai UTM | R1 | Bisa | `GET /v3/orders/utm-campaigns`, `/utm-contents`, `/utm-sources`, `/utm-mediums`, `/utm-terms` |
| Revenue per sumber trafik | R1 | Bisa | `GET /v3/web-analytics/source-revenue` (last-touch, maks 180 hari, limit 600 req/jam) |
| Event real-time order & pembayaran | R1, R4 | Bisa (webhook) | `order.created`, `order.status_changed`, `payment.received`, `payment.failed`, `checkout_intent.abandoned`; signature `X-Scalev-Hmac-Sha256`; **butuh plan Basic ke atas** |
| Bikin order dari chat | R4 | Bisa | `POST /v3/orders` (wajib `customer_name`, `store_unique_id`; digital cukup `ordervariants` + `payment_method: payment_link`) |
| Cek status order | R4 | Bisa | `GET /v3/orders/{id}` atau `GET /v3/orders?search=...` |
| Kirim akses produk digital | R4 | Bisa | `POST /v3/orders/{id}/send-product-digital`, `POST /v3/orders/{id}/send-lms-access` |
| Cek stok | R4 | Kemungkinan bisa **(cek ulang)** | `GET /v3/products?qty_type=available_qty&is_inventory=true`, `GET /v3/variants/{id}`; belum dites di produk fisik |
| Knowledge produk buat jawaban CS | R4 | Bisa | `GET /v3/products/{id}/knowledge-items` (Q&A per produk) |
| Kirim WA dari nomor WABA | R1, R4 | Bisa | `POST /v3/waba-accounts/{id}/customers/{wa_user_id}/messages`, template: `.../message-templates`, `send-messages` |
| Terima WA masuk real-time | R1, R4 | **Tidak lewat Scalev** | Nggak ada event webhook buat pesan WA masuk. Cuma bisa polling `listWabaCustomers` / `listWabaMessages` |

## Bukti dari data real

10 order terakhir (25–26 Okt 2026): 9 dari 10 order bawa UTM lengkap dari iklan Meta. Contoh pola yang kesimpen:

| Field | Contoh nilai | Artinya |
| --- | --- | --- |
| `utm_source` | `facebook` | Sumber |
| `utm_medium` | `paid` | Iklan berbayar |
| `utm_campaign` | `KMK_SALES_20261025`, `LMK_SALES_20261023` | Nama campaign |
| `utm_content` | `KMK_C1_Ganti1SelRAB`, `LMK_B2_DanaTitipan` | Nama iklan/creative |
| `utm_term` | `120249256397820280` | ID Meta (campaign atau adset). **Perlu dicocokkan ke Meta Ads API** |

Statistik 30 hari terakhir (25 Sep–25 Okt 2026, non-spam) dari `/v3/orders/statistics`:

| Status | Order | Gross revenue |
| --- | --- | --- |
| completed | 27 | Rp3.600.532 |
| pending (belum bayar) | 65 | Rp9.157.981 |
| canceled | 13 | Rp1.445.333 |

Insight buat jualan R1: dari 105 order, cuma **27 (26%) yang lunas**. Sisanya 65 order (Rp9,16jt) masih nggantung
belum bayar. Pixel Meta bisa aja ngitung semua checkout itu sebagai konversi, padahal duit yang masuk cuma
Rp3,6jt. Itu persis masalah yang diselesaiin R1. Selisih pending ini juga peluang buat R4 (follow up otomatis).

Catatan: `custom_breakdown_key=utm_campaign` di endpoint statistik **nggak** ngasih pecahan per campaign, cuma per status.
Jadi agregat per campaign dihitung sendiri dari `GET /v3/orders` (loop cursor, 25 per halaman).

## Risiko & keputusan yang perlu diambil

1. **Inbound WA.** Nomor WABA `+62 896-1111-3320` sekarang terhubung ke Scalev (`runtime_owner: nexus_internal`,
   webhook disubscribe Scalev). Bot WA kita butuh pesan masuk real-time, dan ada 2 opsi:
   - **A. App Meta sendiri ikut subscribe ke WABA yang sama.** Meta ngizinin lebih dari 1 app subscribe, tapi
     harus diatur biar Scalev dan bot nggak sama-sama ngebales. Ini perlu dites.
   - **B. Nomor terpisah khusus bot** (misalnya nomor owner-report buat R1). Ini paling aman buat R1, karena yang nanya
     cuma owner, bukan customer.
   - Polling Scalev tiap beberapa menit bisa jadi cadangan, tapi bukan real-time.
2. **Webhook Scalev butuh plan Basic ke atas.** Peserta dengan plan gratis cuma bisa polling, jadi ini masuk prasyarat.
3. **Data customer (nama, HP, alamat) = data pribadi.** Laporan ke WA cukup agregat. Jangan simpan PII di repo atau log
   mentah.
4. **Rate limit:** web analytics 600 req/jam, order list 25 per halaman. Buat bisnis dengan ribuan order/bulan, data
   disinkron ke Supabase (dari webhook) daripada narik ulang tiap ada pertanyaan.

## Langkah berikutnya

- [ ] Cocokkan `utm_term` (`120249256397820280`) ke Meta Ads API: itu campaign id atau adset id? Kalau ketemu, join
      spend Meta ↔ order Scalev bisa pakai ID, nggak cuma nama.
- [ ] Putuskan opsi inbound WA (A atau B) buat demo R1.
- [ ] Cek field stok di produk fisik (`GET /v3/variants/{id}`) buat R4.
- [ ] Tes webhook Scalev ke Supabase Edge Function (event `payment.received`).
