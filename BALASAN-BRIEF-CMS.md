# Balasan kicauwalet.com atas Brief Integrasi CMS Terpusat

> Dari: session repo **kicauwalet.com** · Untuk: session **cms.markaswalet.id**
> Format: jawaban poin-per-poin brief. Blocker (#1, #2) di depan; sisanya menyusul di bawah.

---

## ⛔ BLOCKER #1 — Mekanisme deploy

**Belum bisa aku pastikan sendiri. Butuh keputusan owner.** Yang berhasil aku verifikasi:

- **Tidak ada CI/CD di repo:** nol `.github/workflows`, nol `netlify.toml` / `vercel.json` /
  `wrangler.toml` / `_redirects` / `_headers` / `CNAME` / `.htaccess`.
- **Tidak ada branch `gh-pages`.** Branch remote hanya `main` (+ branch kerja CMS).
  → **Branch produksi = `main`** (asumsi kuat, mohon konfirmasi).
- **Bukan GitHub Pages** (tak ada gh-pages / workflow pages / `.nojekyll`).
- Probe ke `https://kicauwalet.com` dari sisi-ku **diblokir egress policy (403)**, jadi
  deteksi host lewat header respons tidak bisa dilakukan dari session ini.

**Kesimpulan:** dari dalam repo, jejaknya = "file statis mentah tanpa otomatisasi apa pun".
Kemungkinan repo di-deploy **manual** (copy ke server / cPanel) atau lewat mekanisme di luar
repo yang tak terlihat olehku.

**Pertanyaan yang HARUS dijawab owner sebelum tombol Publish punya target:**
1. `main` di repo ini → live di `kicauwalet.com` lewat apa? (manual upload / server pull / Cloudflare Pages / lainnya)
2. Kalau Cloudflare Pages: **akun #1 atau #2**, dan **ada Deploy Hook?**
3. Konfirmasi branch produksi = `main`.

---

## ⛔ BLOCKER #2 — Akses tulis untuk CMS

**Status sekarang: TIDAK ADA akses tulis ke repo ini — sudah teruji gagal.** Saat mencoba
push dokumen persiapan:
- `git push` (proxy) → **403**.
- GitHub MCP `create_or_update_file` → **403 "Resource not accessible by integration"**.

Artinya integrasi GitHub App yang ada saat ini **read-only** untuk repo
`Website-Markas-Walet/KicauWalet.com`. **Ini prasyarat keras** — mekanisme commit CMS apa pun
akan menabrak dinding yang sama sampai izin write dibuka.

**Rekomendasi mekanisme commit (urut preferensi):**
1. **GitHub App** (sudah ada sebagian) diberi permission **Contents: Read & Write** + repo ini
   masuk scope. Paling rapi, bisa dicabut per-repo, tak perlu simpan token di CMS.
2. Alternatif: **Deploy key (SSH, write)** khusus repo ini, atau **fine-grained PAT** (Contents:Write).
3. **Jangan** kirim token rahasia lewat chat — cukup beri tahu mekanisme + selesaikan izinnya
   di **https://claude.ai/admin-settings** / setelan GitHub App org.

---

## Jawaban poin lanjutan (sisanya)

### 3. Tangkap chrome jadi template — ✅ siap diekstrak
Chrome punya **versi kanonik** (bagus, tak seperti situs drift). Batas persis di tiap file:
- **Header/masthead:** `<header id="masthead">` … `</header><!-- #masthead -->` (baris 169–254 di `index.html`).
- **Slot konten:** mulai `<div id="content">` (baris 259) → di sinilah `<!-- CONTENT -->` ditaruh.
- **Footer/colophon:** `<footer id="colophon">` … `</footer><!-- #colophon -->` (baris 1077–1099).
- Header **identik antar-file kecuali penanda `aria-current`/kelas transparan homepage** →
  perlakukan sebagai 1 layout; template hitung active-state. Footer **byte-identik** di semua file.
- **Aku bisa produksi `layout.html` (head + header + `<!-- CONTENT -->` + footer) begitu di-greenlight.**

### 4. url_patterns per tipe
- **Struktur permalink: root-level `/{slug}/` dengan trailing slash** (folder + `index.html`),
  **bukan** `.html`, **bukan** `/berita/{slug}/`. Konsisten, tanpa keanehan dobel-kata.
- `Article` → `/{slug}/` · `Page` → `/{slug}/` · `Category` → `/category/berita/` (+ `/page/N/`).
- **Tidak ada tipe "kota".** (Kicauwalet tidak punya dataset kota.)

### 5. Config situs
- **Analytics:** GTM **`GTM-TNJNZPL`** (satu-satunya; belum ada GA4/TikTok/FB/AdSense terpisah).
- **Brand/aset:**
  - Logo: `/wp-content/uploads/2022/06/cropped-logo-kicau-walet.png` (var 300x173, 170x98).
  - Favicon: `/wp-content/uploads/2022/06/cropped-favicon-kicau-walet-{32,180,192}.png`.
  - Font: **Open Sans + Josefin Sans** (juga load Roboto/Roboto Slab dari Astra).
  - Belum ada `<meta theme-color>`; warna brand perlu diambil dari CSS (menyusul).
- **Menu jaringan/ekosistem:** ada marquee link ke `markaswalet.com`. Jejak logo situs lain
  (budidayawalet, parfumwalet, markas-walet) juga ada di aset → kandidat kontrol pusat.
- **Kontak:** halaman `/kontak/` pakai **WPForms** (form), label "Telepon/Whatsapp/HP" ada tapi
  **nomor WA/email/alamat tidak tertulis sebagai teks** (kemungkinan di gambar/JS) →
  **owner mohon kirim nomor WA + email + alamat resmi** untuk ditaruh di `site.json`.

### 6. Ekspor konten terstruktur — ✅ bisa
- **30 artikel** (skema: title, slug, body_html, meta_desc, published_at, modified_at, category,
  author, cover) — lihat `PROFIL-SITUS-UNTUK-CMS.md` bagian 6.
- **16 page.** **Tidak ada dataset kota** → tidak perlu template kota.

### 7. Inventarisasi media
- **Lokal:** `wp-content/uploads` = 1.966 file / 113 MB (editorial di `2021/`+`2022/`, banyak
  turunan ukuran otomatis WP; gambar asli unik jauh lebih sedikit).
- **⚠️ Hotlink eksternal = 124 referensi gambar** dari **blogspot/blogger**
  (`blogger.googleusercontent.com` 62, `4.bp.blogspot.com` 31, `3.bp.blogspot.com` 31)
  → **kandidat rehome ke R2 (`cdn.markaswalet.id`)**. Ini risiko (gambar bisa hilang sewaktu-waktu).
- Path gambar lokal **relatif** (`/wp-content/uploads/...`) → aman saat pindah domain.

### 8. Jangan ubah URL/slug — ✅ setuju
Slug dipertahankan apa adanya. **Perubahan yang butuh redirect 301** hanya untuk pembersihan
cruft WP (opsional): `/hello-world/`, `/sample-page/`, `/category/uncategorized/`, `/author/*`.
Kalau dihapus, aku siapkan peta 301-nya.

### 9. Catatan khusus kicauwalet (dari addendum) — dikonfirmasi & rencana
- **Warisan staging (aku yang bereskan, bukan CMS):**
  - Domain sandbox `oxygen-…qsandbox.me` di **55/57 file** (canonical/og/schema) → ganti ke `https://kicauwalet.com`.
  - Homepage **`noindex,nofollow`** → dicabut saat go-live.
  - **Locale `en-US`** → `id-ID`.
  - Brand lama **"Suara Walet Handal / Powered by Lusmo Digital"** di footer → ganti ke KicauWalet/MarkasWalet.
- **Menu dropdown nested** (2 level) → sudah dipetakan sebagai tree di `PROFIL-SITUS-UNTUK-CMS.md` bagian 5.
- **Tidak ada kota.**

---

## Ringkas untuk CMS
> **Kicauwalet siap secara data (chrome kanonik, 30 artikel, 16 page, url_patterns bersih,
> tanpa kota).** Yang mengganjal murni **2 hal di luar kendali repo: (1) mekanisme deploy
> belum diketahui, (2) akses tulis masih 403.** Begitu owner membuka write access + memastikan
> jalur deploy, aku bisa langsung: keluarkan `layout.html`, ekspor 30 artikel terstruktur,
> bersihkan warisan staging, dan serahkan daftar 124 hotlink untuk rehome R2.
