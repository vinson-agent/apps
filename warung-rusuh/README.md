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
| Mangga atau Petasan | 12 piring, penanda berputar. Tekan tombol apa saja untuk berhenti. Mangga +1 dan diganti petasan, kecepatan naik. Petasan = strike. Online: bergiliran, strike dipakai bersama. Giliran > 10 dtk = strike. | 14 poin tim, 3 strike |
| Sinyal Lemot | Pencet tombol apa saja untuk mengunggah 3 foto. Progres turun kalau berhenti. Waktu sampai 1/100 dtk. | 100% dalam 6,00 dtk |
| Pesanan Kilat | Pesanan sate (merah), kerupuk (hijau), es teh (biru). Tekan sesuai jumlah. Kelebihan = hangus, tanpa poin. Pesanan makin besar. | 15 poin dalam 40 dtk |

**Latihan Solo → Maraton Arcade**: 3 nyawa. Gagal norma = -1 nyawa, skor ≥ 150% norma = +1 nyawa (maks 5). Tiap putaran urutan diacak dan norma naik 10%.

### Penyesuaian terhadap GDD (perlu diketahui)

- **Mangga atau Petasan, dua panen.** GDD meminta 12 piring, 1 mangga diganti petasan tiap sukses, selesai saat mangga habis, dan norma 14. Dengan 12 piring, skor maksimal hanya 11, jadi norma 14 mustahil. Solusinya: saat mangga habis di panen pertama, meja diisi ulang sekali ("PANEN KEDUA!", kecepatan tetap naik). Game selesai saat 3 strike atau saat panen kedua habis. Skor maksimal 22.
- **Poin pribadi Mangga (online):** skor tim + mangga milikmu − 2 per petasan yang kamu ledakkan. Lulus/gagal tetap dihitung dari skor tim.
- **Poin Sinyal Lemot** (untuk Maraton dan peringkat): selesai = 10 + 5 × (6,00 − waktu); belum selesai = progres/10.
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
  - Balapan (Sinyal, Pesanan): host mengirim `seed` + `startAt` (jam host). Klien mengoreksi selisih jam dengan ping. Semua main lokal dengan konten identik dari seed. Progres dikirim tiap 250 ms, host menyiarkan peringkat langsung tiap 300 ms dan hasil akhir.
  - Mangga: host memegang papan, giliran, strike, dan skor. Pemain yang sedang giliran mengirim piring yang ia hentikan. Host memvalidasi (giliran & turnId cocok) lalu menyiarkan hasilnya.
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

## Versi

- **v1.0 (2026-11-02):** MVP. Tiga game, Maraton, Latihan Solo, multiplayer room PeerJS (maks 6).
