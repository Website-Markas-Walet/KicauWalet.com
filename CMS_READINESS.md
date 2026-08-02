---
site: kicauwalet.com
deploy: { host: unknown, cf_account: null, branch: main, deploy_hook: unknown }
url_patterns: { artikel: "/{slug}/", kota: "", page: "/{slug}/", desain: "" }
chrome: { header_lines: "169-254", footer_lines: "1077-1099", byte_identical: "footer:true; header:variants(active-state-only)" }
analytics: { gtm: "GTM-TNJNZPL", ga4_property_id: "", tiktok: "", fb_verify: "", adsense: "" }
brand: { primary: "", font: "Open Sans, Josefin Sans", logo: "/wp-content/uploads/2022/06/cropped-logo-kicau-walet.png", favicon: "/wp-content/uploads/2022/06/cropped-favicon-kicau-walet-32x32.png" }
ecosystem_menu: present
contact: { wa: "", email: "" }
media: { local_mb: 113, hotlink: "blogspot:124" }
content: { articles: 30, kota_unique: 0, kota_admin_level: kota }
cleanup:
  - "domain sandbox oxygen-qpwj6xj8qwa8.oxygen-demo.qsandbox.me di 55/57 file (canonical/og/schema) -> ganti ke https://kicauwalet.com"
  - "homepage meta robots noindex,nofollow -> cabut saat go-live"
  - "locale en-US -> id-ID"
  - "footer brand lama 'Suara Walet Handal, Jernih & Ori | Powered by Lusmo Digital' -> KicauWalet/MarkasWalet"
  - "cruft WP bawaan: /hello-world/ /sample-page/ /category/uncategorized/ /author/admin/ /author/syamsul-alam/ (hapus + 301)"
  - "124 gambar hotlink blogspot/blogger -> rehome ke R2 cdn.markaswalet.id"
blockers:
  - "deploy: mekanisme repo->live tidak diketahui dari repo (nol CI/CD, bukan gh-pages, probe live diblokir egress); owner harus konfirmasi host + deploy hook"
  - "write access: session/integrasi read-only (git push 403, GitHub MCP 403 'Resource not accessible by integration'); butuh Contents:Read&Write utk repo ini"
  - "kontak: nomor WA & email tidak tertulis sebagai teks di repo (halaman /kontak/ pakai form WPForms); owner harus kirim"
  - "brand.primary: warna utama belum ada di meta (perlu diambil dari CSS)"
---

# Catatan CMS Readiness — kicauwalet.com

## Ringkasan
Situs = **export statis WordPress 6.0** (tema Astra, dibangun Oxygen+Elementor, SEO Rank Math,
form WPForms). Tidak ada PHP/DB/wp-admin/API. Isi = 57 `index.html` (folder permalink) + aset.
**Siap secara data**; yang mengganjal murni 2 blocker eksternal (deploy & write access).

## Chrome (jangan diubah — hanya ditandai)
- **Header/masthead:** `<header id="masthead">` … `</header><!-- #masthead -->` = **baris 169–254** (`index.html`).
- **Slot konten:** `<div id="content">` mulai **baris 259** → titik sisip `<!-- CONTENT -->`.
- **Footer/colophon:** `<footer id="colophon">` … `</footer><!-- #colophon -->` = **baris 1077–1099**.
- **Footer byte-identik** di 57 file. **Header sama secara struktur**, beda hanya penanda
  `aria-current="page"` + kelas transparan homepage → perlakukan 1 layout, active-state dihitung template.
- `layout.html` (head + header + `<!-- CONTENT -->` + footer) bisa diproduksi begitu di-greenlight.
- **Tidak ada perubahan lain di-commit ke file situs** (sesuai instruksi).

## URL / tipe konten
- Permalink **root-level `/{slug}/` + trailing slash** (folder + `index.html`), **bukan** `.html`,
  **bukan** `/berita/{slug}/`. Konsisten, tanpa keanehan dobel-kata.
- `Article` 30 → `/{slug}/` · `Page` 16 → `/{slug}/` · `Category` → `/category/berita/` (+ `/page/N/`).
- **Tidak ada tipe kota maupun desain.**

## Media
- Lokal `wp-content/uploads` = **113 MB / 1.966 file** (editorial 2021+2022; banyak turunan ukuran
  otomatis WP → gambar asli unik jauh lebih sedikit). Path lokal **relatif** (aman saat pindah domain).
- **124 hotlink eksternal**: `blogger.googleusercontent.com` 62, `4.bp.blogspot.com` 31,
  `3.bp.blogspot.com` 31 → **rehome ke R2**. Daftar URL lengkap bisa diekspor on-demand.

## Blocker (butuh owner)
1. **Deploy** — `main` → live `kicauwalet.com` lewat apa? (manual / server pull / Cloudflare Pages;
   jika CF: akun #1/#2 + Deploy Hook?). Konfirmasi branch produksi = `main`.
2. **Write access** — buka GitHub App **Contents: Read & Write** utk `Website-Markas-Walet/KicauWalet.com`
   di https://claude.ai/admin-settings (jangan kirim token via chat).
3. **Kontak** — kirim nomor WA + email + alamat resmi untuk `site.json`.

## Referensi
Detail lengkap: `PROFIL-SITUS-UNTUK-CMS.md` (profil situs) & `BALASAN-BRIEF-CMS.md` (jawaban brief).
