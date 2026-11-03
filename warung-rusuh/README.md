# Warung Rusuh

Party arcade bertema pasar malam: tiga tombol (MERAH / HIJAU / BIRU), mini-game singkat, dan bisa main bareng online pakai kode room 4 huruf (maks 6 pemain). Satu file `index.html` tanpa build step. Desain lengkap ada di `design/gdd.md`.

## Cara main

- Buka `index.html` (atau `https://vinson-agent.github.io/apps/warung-rusuh/` kalau sudah dipublikasikan).
- **Menu Besar**: pilih kartu game (Mangga atau Petasan, Siapa Pembeli Ini?, Sinyal Lemot, Turun Tangga Pasar, Pesanan Kilat), **Maraton** (kelima game berurutan, skor dijumlah, pangkat di akhir), atau **Latihan Solo** (tanpa internet).
- Setelah memilih game: **Buat Room**, **Gabung dengan Kode**, atau **Main Solo**.
- Kontrol: keyboard `A` / `S` / `D` (merah/hijau/biru) dan `Spasi` (tombol apa saja). Di HP: tiga zona warna besar di bawah layar (multitouch). Mengetuk area game juga dihitung "tombol apa saja" di Mangga, Sinyal, dan Turun Tangga Pasar.
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
| Siapa Pembeli Ini? | Balapan reaksi 15 dtk. Pelanggan lewat satu per satu (jadwal sama dari seed). Kalau itu pelanggan target: tekan MERAH cepat. Kalau bukan: tekan HIJAU cepat. Lebih cepat = lebih banyak poin; telat/salah tombol = 0 untuk pelanggan itu, tanpa strike. | 80 poin |
| Sinyal Lemot | Balapan tetap 10,00 dtk untuk semua. Pencet tombol apa saja: tiap bar 100% = 1 foto terkirim, bar balik ke 0 (sisa tidak dibawa), main terus sampai waktu habis. Progres turun kalau berhenti. Pemenang = foto terbanyak; seri → progres foto berjalan lebih tinggi → foto terakhir lebih awal. | 3 foto dalam 10 dtk |
| Turun Tangga Pasar | Balapan hindar 18 dtk, 3 jalur (MERAH/HIJAU/BIRU). Barang jatuh (jadwal sama dari seed) kelihatan ±1 dtk sebelum sampai di garis bawah — pindah ke jalur kosong sebelum kena. Kena di jalurmu = strike; 3 strike = berhenti duluan. Pemenang = langkah selamat terbanyak; seri → strike lebih sedikit → bertahan lebih lama. | 12 langkah selamat |
| Pesanan Kilat | Pesanan sate (merah), kerupuk (hijau), es teh (biru). Tekan sesuai jumlah. Kelebihan = hangus, tanpa poin. Pesanan makin besar. | 15 poin dalam 40 dtk |

**Gaya visual (v1.3):** wajah-wajah chibi bulat bergaris tebal, warna cerah, pipi merona, mata besar — gaya "pesta warna-warni" generik, semuanya digambar dari nol lewat canvas primitives (`drawChibiFace`, `drawCrate`, `drawBasket`, `drawPuddle`), bukan aset atau karakter dari game manapun.

**Latihan Solo → Maraton Arcade**: 3 nyawa. Gagal norma = -1 nyawa, skor ≥ 150% norma = +1 nyawa (maks 5). Tiap putaran urutan diacak dan norma naik 10%.

### Penyesuaian terhadap GDD (perlu diketahui)

- **Mangga atau Petasan, dua panen.** GDD meminta 12 piring, 1 mangga diganti petasan tiap sukses, selesai saat mangga habis, dan norma 14. Dengan 12 piring, skor maksimal hanya 11, jadi norma 14 mustahil. Solusinya: saat mangga habis di panen pertama, meja diisi ulang sekali ("PANEN KEDUA!", kecepatan tetap naik). Skor maksimal 22.
- **Mangga atau Petasan (v1.2): jadi balapan 20 dtk individual**, bukan lagi giliran di satu papan bersama. GDD aslinya giliran + skor tim; diubah supaya kompetitif seperti Sinyal Lemot dan Pesanan Kilat — tiap pemain dapat papan sendiri (seed sama), main simultan, 3 strike cuma menghentikan pemain itu sendiri (bukan seluruh ronde). Poin = skor mangga pribadi. Seri dipecah: strike lebih sedikit menang, lalu yang selesai (panen kedua habis) lebih dulu menang.
- **Sinyal Lemot (v1.1): lomba jumlah foto 10 dtk**, bukan lagi "sekali tembus 100%". Konstanta di `SINYAL` (`wr-core`): `PER_PRESS` 6,67 (≈15 tekan per foto), `IDLE` 0,25 dtk (jeda lebih lama = progres turun; mash terus tidak pernah turun), `DECAY` 30 %/dtk, `NORMA` 3. Uji simulasi: 6 tekan/dtk ≈ 4 foto, 10 tekan/dtk ≈ 6 foto.
- **Poin Sinyal Lemot** (untuk Maraton dan peringkat): foto × 5 + progres foto berjalan/20. Lulus = foto ≥ norma. Bonus nyawa = foto ≥ 150% norma (mis. 5 dari 3).
- **Rekor Sinyal** = foto terbanyak dalam 10 dtk (`wr_hs.sinyalFoto`). Rekor lama v1.0 (waktu tercepat, `wr_hs.sinyal`) dibuang otomatis karena satuannya beda.
- **Poin Pesanan Kilat:** 1 poin per pesanan, +1 untuk pesanan berisi ≥6 item, +2 untuk ≥12.
- Belum dibuat (Tahap 2/stretch di GDD): Kucing Kabur, Gerobak Seimbang, Kombo Rusuh, Kartu Kacau, mode hot-seat.
- **Siapa Pembeli Ini? dan Turun Tangga Pasar (v1.3), game baru di luar GDD asli.** Dirancang dari nol (lihat `design/gdd-candidates-2026-11-03.md`) untuk menambah variasi skill di Maraton: Siapa Pembeli Ini? menguji atensi/diskriminasi reaksi (bukan sekadar refleks atau mengetik), Turun Tangga Pasar menguji keputusan spasial 3-jalur (satu-satunya game yang tombolnya berarti "pindah ke sini", bukan "tombol apa saja" atau "cocokkan warna"). Keduanya pakai pola "balapan" yang sama (state sendiri-sendiri dari seed yang sama, lapor progres tiap 250ms, rangking pakai `sortKey`) seperti tiga game lainnya — tidak ada arsitektur baru.

## Teknis

- Satu file `index.html`: HTML + CSS + JS inline, canvas, pixel art dan suara (WebAudio) dibuat lewat kode. Tidak ada aset eksternal kecuali PeerJS.
- **Transport:** PeerJS **1.5.5** (dipin, SRI sha384) dari jsDelivr, cadangan unpkg. Dimuat hanya saat main online, jadi Latihan Solo jalan tanpa internet. Peer ID host = `warungrusuh-<KODE>`. Kalau ID bentrok, kode baru dicoba otomatis. Broker sinyal: server publik PeerJS (`0.peerjs.com`). PeerJS 1.5.5 juga membawa konfigurasi STUN Google + TURN publik PeerJS secara bawaan.
- **Struktur kode:**
  - `<script id="wr-core">`: logika murni tanpa DOM, bisa diuji di Node. Isinya PRNG `mulberry32`, aturan lima game (`WR.mods.mangga|pembeli|sinyal|tangga|pesanan`), `RoomHost` (pemegang state, topologi bintang), `RoomClient` (dipakai semua pemain, termasuk host), transport tiruan in-memory `MemNet`, dan `muxHost`.
  - `<script id="wr-ui">`: layar DOM, gambar canvas, input, suara, transport PeerJS (`PeerNet.host/join`).
  - Abstraksi transport. Host: `onConnect / onMessage / onDisconnect / send / broadcast / drop / close`. Klien: `send / onMessage / onClose / close`.
  - Host juga pemain: klien lokalnya tersambung lewat `MemNet`, digabung dengan koneksi PeerJS lewat `muxHost`. **Latihan Solo memakai `RoomHost` + `RoomClient` yang sama** lewat `MemNet`, jadi logika solo dan online identik.
- **Sinkronisasi:**
  - Balapan (Mangga, Siapa Pembeli Ini?, Sinyal, Turun Tangga Pasar, Pesanan): host mengirim `seed` + `startAt` (jam host). Klien mengoreksi selisih jam dengan ping. Semua main lokal dengan konten identik dari seed. Progres dikirim tiap 250 ms (Mangga & Turun Tangga Pasar: `score`, `strikes`, `finishT`; Sinyal: `photos`, `prog`, `lastPhotoT`; Siapa Pembeli Ini? & Pesanan: `score`, `prog`; host membatasi semua nilainya), host menyiarkan peringkat langsung tiap 300 ms dan hasil akhir.
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

## Pengujian v1.3 (2026-11-03)

- Node + jam virtual + `MemNet`: regresi inti Siapa Pembeli Ini? (pemain sempurna yang melihat jadwal slot vs idle vs selalu-salah — skor 0 di kedua kasus gagal) dan Turun Tangga Pasar (pemain sempurna yang menghindar vs pemain statis yang kena 3 strike persis lalu berhenti, dikonfirmasi sama persis lewat panggilan `update()` sekali lompat besar dan lewat tik 250ms berturut-turut — menemukan dan memperbaiki bug nyata: loop resolusi gelombang tidak berhenti begitu game berakhir pertengahan-loop, menyebabkan strike lebih dari 3 dan `finishT` salah).
- Alur multiplayer penuh (host + 1 tamu, `RoomHost`/`RoomClient` virtual-clock) untuk kedua game baru: hasil akhir, rangking, dan detail cocok dengan simulasi lokal masing-masing klien.
- Maraton 5-game: urutan `WR.MARATHON` (`mangga, pembeli, sinyal, tangga, pesanan`) dikonfirmasi diputar lengkap dan berurutan baik di mode tetap (`final` pas di game ke-5) maupun Maraton Arcade Solo (nyawa berkurang/bertambah benar untuk game baru, berhenti lebih awal kalau nyawa habis — perilaku yang diharapkan, bukan bug).
- Pemindaian otomatis untuk bug kebocoran-scope (identifier modul inti dipakai telanjang di skrip UI, seperti bug Mangga sebelumnya): ditemukan dan diperbaiki satu kasus baru (`TANGGA.LEAD` di `drawTangga`) sebelum push.
- Kedua fungsi gambar baru (`drawPembeli`, `drawTangga`) dieksekusi berdiri sendiri lewat ekstraksi + `new Function` terhadap canvas tiruan — tidak melempar galat, tapi ini TIDAK memverifikasi tampilan piksel sungguhan karena tidak ada browser nyata di host ini (Lightpanda tidak ada build Windows).
- Belum diuji untuk v1.3: WebRTC nyata (PeerJS), Edge/Playwright UI penuh, HP sungguhan, dan yang terpenting — **tampilan visual sungguhan dari kedua game baru belum pernah dilihat** (hanya diverifikasi lewat eksekusi fungsi tanpa galat). Mohon screenshot setelah refresh untuk konfirmasi tampilan.

## Versi

- **v1.3 (2026-11-03):** Dua game baru — **Siapa Pembeli Ini?** (balapan reaksi/atensi 15 dtk, kenali pelanggan target) dan **Turun Tangga Pasar** (balapan hindar 3-jalur 18 dtk). Maraton sekarang 5 game. Gaya visual baru: wajah-wajah chibi bulat bergaris tebal warna-warni (`drawChibiFace`), dipakai juga untuk karakter pemain di Turun Tangga Pasar. Lihat GDD di `design/gdd-candidates-2026-11-03.md` untuk rancangan lengkap (termasuk satu ide yang sengaja tidak dibuat karena tumpang tindih dengan Pesanan Kilat).
- **v1.2 (2026-11-03):** Mangga atau Petasan jadi balapan individual 20 dtk (bukan giliran di papan bersama) — setiap pemain dapat papan sendiri dari seed yang sama, main simultan seperti Sinyal Lemot & Pesanan Kilat. 3 strike cuma menghentikan pemain itu sendiri. Ranking berdasar skor mangga pribadi, seri dipecah oleh jumlah strike lalu kecepatan selesai. Netcode giliran (turn/press/turnResult) lama masih ada di kode tapi sudah tidak dipakai game manapun — kandidat dibersihkan di versi berikut.

- **v1.1 (2026-11-03):** Sinyal Lemot jadi lomba jumlah foto 10 dtk (tiap 100% = 1 foto, bar reset, pemenang = foto terbanyak). Norma 3 foto (+10% per putaran Maraton), poin foto × 5, rekor = foto terbanyak, hitung mundur, penghitung "FOTO TERKIRIM", efek + suara tiap foto.
- **v1.0 (2026-11-02):** MVP. Tiga game, Maraton, Latihan Solo, multiplayer room PeerJS (maks 6).
