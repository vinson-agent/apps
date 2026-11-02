# Prompt sprite untuk Nano Banana (gemini-2.5-flash-image)

Generate satu gambar per sprite, simpan sebagai `<nama>.png` di folder ini (square, 1:1).

Gaya bersama (tempel di awal setiap prompt):
> 16x16 pixel art game tile, flat colors, limited palette, hard pixel edges, no anti-aliasing, no text, no border, no shadow outside the tile, centered, square, retro 16-bit minesweeper style, indigo and lavender palette.

| File | Isi |
|---|---|
| `hidden.png` | Ubin belum dibuka: lavender, bevel menonjol (terang di kiri-atas, gelap di kanan-bawah) |
| `empty.png` | Ubin terbuka kosong: indigo gelap datar, garis tipis gelap di kiri-atas |
| `n1.png` ... `n8.png` | Ubin terbuka dengan satu angka pixel di tengah. Warna: 1 cyan, 2 hijau, 3 merah, 4 ungu, 5 oranye, 6 teal, 7 putih, 8 pink |
| `flag.png` | Ubin belum dibuka dengan bendera merah di tiang hitam |
| `mine.png` | Ubin terbuka dengan ranjau hitam berduri dan kilau putih |
| `mineHit.png` | Sama dengan mine, latar merah (ranjau yang diinjak) |
| `wrong.png` | Ranjau dengan tanda silang merah (bendera salah) |
| `faceHappy.png` | Wajah kuning pixel, tersenyum |
| `faceScared.png` | Wajah kuning, mata lebar dan mulut "o" |
| `faceDead.png` | Wajah kuning, mata X |
| `faceCool.png` | Wajah kuning, kacamata hitam, tersenyum |

Tips: kalau hasilnya blur, minta "exactly 16x16 pixel grid upscaled with nearest neighbor". Pastikan semua ubin satu gaya dan satu palet.
