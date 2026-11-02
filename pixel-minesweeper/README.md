# Pixel Minesweeper

Minesweeper bergaya pixel art. Satu file `index.html`, tanpa build dan tanpa server. Sprite sheet sudah tertanam di dalam file, jadi cukup double-click.

## Cara main
- Klik kiri: buka kotak. Klik kanan atau tahan lama (HP): bendera, lalu tanda tanya, lalu kosong.
- Klik angka yang bendera di sekitarnya sudah pas: buka semua tetangganya (chord).
- Klik pertama selalu aman, dan kotak di sekitarnya bebas ranjau.
- Tombol "Mode bendera" untuk HP: tap = bendera.
- Klik smiley untuk main lagi.
- Level: Pemula 9x9/10, Menengah 16x16/40, Ahli 30x16/99. Rekor waktu tersimpan di `localStorage`.

## Grafik
Sprite 16x16 dibuat dengan script `scripts/gen_minesweeper_sprites.py` (workspace Gordon), hasilnya ada di `../pixel-minesweeper-sprites/`. Nano Banana sempat dicoba tapi kuota Gemini habis (HTTP 429). `assets/PROMPTS.md` berisi prompt untuk generate ulang kalau kuota sudah pulih.

## Versi
- v2.0 (2026-11-02): ditulis ulang memakai sprite sheet pixel baru (tertanam base64). Logika diuji otomatis di Node (200 permainan menang, kalah, bendera). Tampilan di browser belum diuji.
- v1.0 (2026-11-02): versi awal dengan sprite digambar dari kode. Ditimpa oleh v2.0.
