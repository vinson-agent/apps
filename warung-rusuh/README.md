# Warung Rusuh

Party arcade bertema pasar malam: tiga tombol (MERAH / HIJAU / BIRU), mini-game singkat, dan bisa main bareng online pakai kode room 4 huruf (maks 6 pemain). Satu file `index.html` tanpa build step. Desain lengkap ada di `design/gdd.md`.

## Cara main

- Buka `index.html` (atau `https://vinson-agent.github.io/apps/warung-rusuh/` kalau sudah dipublikasikan).
- **Menu Besar**: pilih kartu game (Mangga atau Petasan, Sinyal Lemot, Pesanan Kilat), **Maraton** (ketiganya berurutan, skor dijumlah, pangkat di akhir), atau **Latihan Solo** (tanpa internet).
- Setelah memilih game: **Buat Room**, **Gabung dengan Kode**, atau **Main Solo**.
- Kontrol: keyboard `A` / `S` / `D` (merah/hijau/biru) dan `Spasi` (tombol apa saja). Di HP: tiga zona warna besar di bawah layar (multitouch). Mengetuk area game juga dihitung "tombol apa saja" di Mangga dan Sinyal.
- Tombol 🔊 untuk mute. Nama panggilan, mute, dan rekor tersimpan di `localStorage`.

### Main bareng (online)

1. Host: pilih game, isi nama, tekan **Buat Room**. Muncul kode seperti `KMPR`.
2. Bagikan lewat **Salin Kode**, **Salin Link** (`.../warung-rusuh/?room=KMPR`), atau **Bagikan** (Web Share API di HP).
3. Pemain lain membuka link (layar gabung terbuka dengan kode terisi) atau mengetik kode, lalu isi nama.
4. Di lobi host memilih game lalu menekan **MULAI!**. Semua pemain mendapat hitung mundur yang sama.
5. Selesai: papan skor, lalu host memilih **Lanjut** (Maraton), **Main Lagi**, atau **Ke Lobi**.

Pemain yang keluar tidak menghentikan room. Kalau host keluar, room ditutup dan pemain lain melihat pesan "Host keluar, room ditutup."

## Game

| Game | Aturan | Norma |
|---|---|---|
| Mangga atau Petasan | Balapan 20 dtk, piring sendiri-sendiri (seed sama tiap putaran). Tekan tombol apa saja untuk memetik di posisi penanda saat ini. Mangga +1 & diganti petasan, kecepatan naik, 2 panen. Petasan = strike milikmu sendiri; 3 strike = kamu berhenti duluan, pemain lain lanjut. Pemenang = skor terbanyak; seri → strike lebih sedikit → selesai (panen habis) lebih awal. | 14 mangga |
| Sinyal Lemot | Balapan tetap 10,00 dtk untuk semua. Pencet tombol apa saja: tiap bar 100% = 1 foto terkirim, bar balik ke 0 (sisa tidak dibawa), main terus sampai waktu habis. Progres turun kalau berhenti. Pemenang = foto terbanyak; seri → progres foto berjalan lebih tinggi → foto terakhir lebih awal. | 3 foto dalam 10 dtk |
| Pesanan Kilat | Pesanan sate (merah), kerupuk (hijau), es teh (biru). Tekan sesuai jumlah. Kelebihan = hangus, tanpa poin. Pesanan makin besar. | 15 poin dalam 40 dtk |

**Latihan Solo → Maraton Arcade**: 3 nyawa. Gagal norma = -1 nyawa, skor ≥ 150% norma = +1 nyawa (maks 5). Tiap putaran urutan diacak dan norma naik 10%.

### Penyesuaian terhadap GDD (perlu diketahui)

- **Mangga atau Petasan, dua panen.** GDD meminta 12 piring, 1 mangga diganti petasan tiap sukses, selesai saat mangga habis, dan norma 14. Dengan 12 piring, skor maksimal hanya 11, jadi norma 14 mustahil. Solusinya: saat mangga habis di panen pertama, meja diisi ulang sekali ("PANEN KEDUA!", kecepatan tetap naik). Skor maksimal 22.
- **Mangga atau Petasan (v1.2): jadi balapan 20 dtk individual**, bukan lagi giliran di satu papan bersama. GDD aslinya giliran + skor tim; diubah supaya kompetitif seperti Sinyal Lemot dan Pesanan Kilat — tiap pemain dapat papan sendiri (seed sama), main simultan, 3 strike cuma menghentikan pemain itu sendiri (bukan seluruh ronde). Poin = skor mangga pribadi. Seri dipecah: strike lebih sedikit menang, lalu yang selesai (panen kedua habis) lebih dulu menang.
- **Sinyal Lemot (v1.1): lomba jumlah foto 10 dtk**, bukan lagi "sekali tembus 100%". Konstanta di `SINYAL` (`wr-core`): `PER_PRESS` 6,67 (≈15 tekan per foto), `IDLE` 0,25 dtk (jeda lebih lama = progres turun; mash terus tidak pernah turun), `DECAY` 30 %/dtk, `NORMA` 3. Uji simulasi: 6 tekan/dtk ≈ 4 foto, 10 tekan/dtk ≈ 6 foto.
- **Poin Sinyal Lemot** (untuk Maraton dan peringkat): foto × 5 + progres foto berjalan/20. Lulus = foto ≥ norma. Bonus nyawa = foto ≥ 150% norma (mis. 5 dari 3).
- **Rekor Sinyal** = foto terbanyak dalam 10 dtk (`wr_hs.sinyalFoto`). Rekor lama v1.0 (waktu tercepat, `wr_hs.sinyal`) dibuang otomatis karena satuannya beda.
- **Poin Pesanan Kilat:** 1 poin per pesanan, +1 untuk pesanan berisi ≥6 item, +2 untuk ≥12.
- Belum dibuat (Tahap 2/stretch di GDD): Kucing Kabur, Gerobak Seimbang, Kombo Rusuh, Kartu Kacau, mode hot-seat.

## Teknis

- Satu file `index.html`: HTML + CSS + JS inline, canvas, pixel art dan suara (WebAudio) dibuat lewat kode. Tidak ada aset eksternal kecuali PeerJS.
- **Transport:** PeerJS **1.5.5** (dipin, SRI sha384) dari jsDelivr, cadangan unpkg. Dimuat hanya saat main online, jadi Latihan Solo jalan tanpa internet. Peer ID host = `warungrusuh-<KODE>`. Kalau ID bentrok, kode baru dicoba otomatis. Broker sinyal: server publik PeerJS (`0.peerjs.com`). PeerJS 1.5.5 juga membawa konfigurasi STUN Google + TURN publik PeerJS secara bawaan.
- **Struktur kode:**
  - `<script id="wr-core">`: logika murni tanpa DOM, bisa diuji di Node. Isinya PRNG `mulberry32`, aturan tiga game (`WR.mods.mangga|sinyal|pesanan`), `RoomHost` (pemegang state, topologi bintang), `RoomClient` (dipakai semua pemain, termasuk host), transport tiruan in-memory `MemNet`, dan `muxHost`.
  - `<script id="wr-ui">`: layar DOM, gambar canvas, input, suara, transport PeerJS (`PeerNet.host/join`).
  - Abstraksi transport. Host: `onConnect / onMessage / onDisconnect / send / broadcast / drop / close`. Klien: `send / onMessage / onClose / close`.
  - Host juga pemain: klien lokalnya tersambung lewat `MemNet`, digabung dengan koneksi PeerJS lewat `muxHost`. **Latihan Solo memakai `RoomHost` + `RoomClient` yang sama** lewat `MemNet`, jadi logika solo dan online identik.
- **Sinkronisasi:**
  - Balapan (Mangga, Sinyal, Pesanan): host mengirim `seed` + `startAt` (jam host). Klien mengoreksi selisih jam dengan ping. Semua main lokal dengan konten identik dari seed. Progres dikirim tiap 250 ms (Mangga: `score`, `strikes`, `finishT`; Sinyal: `photos`, `prog`, `lastPhotoT`; host membatasi semua nilainya), host menyiarkan peringkat langsung tiap 300 ms dan hasil akhir.
  - Heartbeat 2 dtk. Diam 9 dtk = dianggap putus (di dua sisi).

## Batasan yang diketahui

- Tanpa server TURN sendiri, sebagian jaringan seluler/kantor/kampus yang ketat bisa gagal tersambung. Aplikasi menampilkan saran: Wi-Fi yang sama atau hotspot. TURN publik bawaan PeerJS mungkin membantu, tapi tidak dijamin.
- Bergantung pada broker publik PeerJS (gratis, tanpa SLA). Kalau broker mati, mode online tidak bisa dipakai. Solo tetap bisa.
- Kalau tab host di HP dipindah ke latar belakang terlalu lama, browser bisa memperlambat timer. Pemain lain bisa terputus setelah 9 dtk.
- Tidak ada anti-curang (sesuai GDD, ini game santai). Waktu "tekan" Mangga dihitung di perangkat pemain.
- Pemain yang bergabung saat ronde berjalan menunggu sampai ronde berikutnya.

## Pengujian (2026-11-02)

- Node + jam virtual + transport in-memory (1 host + 3–6 klien, latensi 35 ms, jam klien digeser ±1,5–3,6 dtk): 126 cek lulus. Mencakup lobi, room penuh, kode tidak ada, ID bentrok, balapan, giliran, validasi giliran, timeout, pemain keluar (lobi/balapan/saat giliran), putus diam-diam (heartbeat), host keluar, pemain telat, Maraton, dan Solo arcade (nyawa).
- Edge headless (Playwright): alur solo ketiga game (menang, kalah, Main Lagi), tap sentuh di HP, `?room=`, rekor, mute, layout HP 375 px dan desktop 1280 px, tanpa error JS.
- **WebRTC nyata** lewat broker publik PeerJS: 6 browser (1 host + 5 tamu) di **satu mesin**. Lobi, pemain ke-7 ditolak, kode salah, balapan Sinyal, Mangga bergiliran, tamu keluar, tab ditutup, host keluar.
- Belum diuji: HP sungguhan dan jaringan berbeda (NAT seluler, Wi-Fi lain), Safari/iOS, serta rasa/keseimbangan permainan dengan manusia.

## Pengujian v1.1 (2026-11-03)

- Node + jam virtual + `MemNet` (latensi 35 ms, jam klien digeser ±0,5–3,6 dtk): 168 cek lulus. Mencakup hitung foto, bar reset tanpa sisa, decay (termasuk tidak tergantung frame rate), akhir ronde 10 dtk, tie-breaker, norma/bonus, room 1 host + 3/4/5 klien balapan Sinyal (seed/startAt sama, peringkat langsung, papan skor akhir sama di semua klien), 6 pemain dengan seri dan pemain keluar di tengah balapan, pemain ke-7 ditolak, sanitasi laporan progres, Solo Sinyal, Maraton Arcade (nyawa), Maraton online 3 pemain, regresi Mangga (22 poin sempurna, 3 strike) dan Pesanan Kilat.
- Edge headless (Playwright): Solo Sinyal di HP 375 px (tap tombol + tap canvas) dan desktop 1280 px (A/S/D/Spasi, klik), rekor lama diabaikan, rekor baru "Rekor: N foto", hasil/norma tampil benar, Pesanan & Mangga masih jalan, tanpa error JS, tanpa overflow horizontal.
- Belum diuji untuk v1.1: WebRTC nyata (PeerJS) dan HP sungguhan. Logika jaringan tidak berubah, hanya isi pesan progres.

## Pengujian v1.2 (2026-11-03)

- Node + jam virtual + `MemNet`: regresi inti Mangga (pemain agresif vs idle, pemain "sempurna" membersihkan 22 mangga dalam ~4 dtk dari 20 dtk, pemain acak kena 3 strike), tie-break (skor sama → strike lebih sedikit → selesai lebih awal), norma/bonus pakai skor bukan tim. Alur room penuh (host + 1 tamu) sampai hasil akhir: Mangga sekarang balapan simultan, bukan giliran — dua pemain menerima `start` yang sama dan main independen, hasil akhir cocok dengan simulasi lokal masing-masing.
- Belum diuji untuk v1.2: WebRTC nyata (PeerJS), Edge/Playwright UI penuh, dan HP sungguhan untuk papan Mangga individual yang baru.

## Versi

- **v1.2 (2026-11-03):** Mangga atau Petasan jadi balapan individual 20 dtk (bukan giliran di papan bersama) — setiap pemain dapat papan sendiri dari seed yang sama, main simultan seperti Sinyal Lemot & Pesanan Kilat. 3 strike cuma menghentikan pemain itu sendiri. Ranking berdasar skor mangga pribadi, seri dipecah oleh jumlah strike lalu kecepatan selesai. Netcode giliran (turn/press/turnResult) lama masih ada di kode tapi sudah tidak dipakai game manapun — kandidat dibersihkan di versi berikut.

- **v1.1 (2026-11-03):** Sinyal Lemot jadi lomba jumlah foto 10 dtk (tiap 100% = 1 foto, bar reset, pemenang = foto terbanyak). Norma 3 foto (+10% per putaran Maraton), poin foto × 5, rekor = foto terbanyak, hitung mundur, penghitung "FOTO TERKIRIM", efek + suara tiap foto.
- **v1.0 (2026-11-02):** MVP. Tiga game, Maraton, Latihan Solo, multiplayer room PeerJS (maks 6).
