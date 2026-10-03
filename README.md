# Solitaire

Klondike solitaire (patience) dengan drag-and-drop, hint, undo, auto-finish, dan efek suara sintetis. Dibuat sebagai app statis tanpa build step — cukup sebuah HTTP server.

## Fitur

- **Klondike penuh** — 52 kartu, 7 kolom tableau, 4 foundation, stock + waste.
- **Draw 1 / Draw 3** — ganti mode draw kapan saja (memulai ulang permainan).
- **Drag & drop dan tap-to-move** — seret kartu ke tujuan, atau klik/tap kartu untuk langsung dipindahkan ke tujuan terbaik.
- **Hint** — menyorot kartu dan tujuan yang disarankan, dilengkapi toast penjelasan.
- **Undo** — snapshot penuh state permainan, sampai 500 langkah.
- **Auto finish** — tombol muncul otomatis saat stock/waste habis dan semua tableau terbuka.
- **Skor standar** — kartu ke foundation `+10`, waste ke tableau `+5`, membuka kartu tertutup `+5`, foundation ke tableau `-15`, recycle pada mode Draw 1 `-100`.
- **Suara sintetis** — shuffle, deal, flip, place, foundation, invalid, recycle, dan win. Dibuat dengan Web Audio API, tanpa file audio.
- **Animasi kemenangan** — kartu melompat keluar dari tableau lalu memantul di canvas (bouncing-card cascade), diikuti modal statistik.
- **Responsif** — ukuran kartu dihitung ulang mengikuti viewport; orientasi landscape maupun portrait.
- **PWA** — manifest + ikon, bisa di-install ke home screen.
- **Tema** — warna dan ukuran kartu diatur lewat CSS variable di `styles.css`.

## Cara menjalankan

### Lokal

Aplikasi **harus** disajikan lewat HTTP. Membuka `index.html` langsung dengan double-click (`file://`) **tidak akan jalan**: `game.js` dimuat sebagai ES module (`<script type="module">`) yang meng-`import` modul lokal lain, dan browser memblokir ES module dari protokol `file://` karena CORS.

```bash
cd solitaire-puter

# opsi 1 — Python (biasanya sudah terpasang)
python -m http.server 5173

# opsi 2 — Node.js
npx serve -l 5173
```

Lalu buka <http://localhost:5173/>.

### Preview di Puter

Buka <https://builder.puter.com>, unggah folder project ini, lalu preview. Builder men-deploy sebagai app web dan menyertakan metadata PWA secara otomatis.

### Kebutuhan

- Browser modern (Chrome, Edge, Firefox, Safari).
- **Koneksi internet** — semua dependensi dimuat dari CDN, tidak di-bundle lokal:
  - `https://cdn.tailwindcss.com` — utilitas CSS (`index.html`)
  - `https://cdn.jsdelivr.net/npm/lucide@latest/+esm` — ikon (`game.js`)
  - `https://fonts.googleapis.com` — font Inter (`index.html`)
  - `https://builder.puter.com/runtime.js` — runtime Puter (`index.html`)

  Untuk pemakaian sepenuhnya offline, salin dependensi tersebut ke folder lokal dan ubah referensinya di `index.html` serta `game.js`.

## Kontrol

| Aksi | Mouse / Touch | Keyboard |
| --- | --- | --- |
| Ambil kartu dari stock | Klik kartu stock | `Space` atau `D` |
| Pindahkan kartu | Drag, atau klik/tap kartu | — |
| Undo | Tombol **Undo** | `Ctrl+Z` / `U` |
| Hint | Tombol **Hint** | `H` |
| Permainan baru | Tombol **New game** | `N` |
| Matikan / nyalakan suara | Tombol speaker | — |

## Aturan & skor

Permainan mengikuti aturan Klondike standar:

- **Tableau** — kartu harus berbeda warna dan berurutan turun, atau King ke kolom kosong.
- **Foundation** — suit harus sama dan rank naik mulai dari Ace.
- Hanya kartu terbuka (face-up) yang bisa dipindahkan; kartu tertutup yang tersingkap dibalik otomatis.
- **Stock** — klik kartu stock untuk mengambil, klik lagi saat stock habis untuk recycle waste.

## Struktur project

```
.
├── index.html       # markup, muat Tailwind & runtime Puter
├── game.js          # logika permainan, layout, drag & drop, hint, HUD
├── sound.js         # efek suara sintetis (Web Audio API)
├── win.js           # path glyph suit + animasi kartu jatuh ke canvas
├── styles.css       # tema & animasi (CSS variable di :root)
├── manifest.json    # metadata PWA
├── icon.svg         # favicon
└── icons/           # ikon PWA 192 / 512 / maskable / apple-touch
```

Semua modul browser-side memakai native ES modules — tidak ada `npm install`, tidak ada bundler, dan tidak ada dependensi runtime yang perlu di-build.