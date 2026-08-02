# Profil Diri kicauwalet.com — untuk CMS Terpusat app.markaswalet.id

> Dokumen ini adalah **deskripsi jujur kondisi aktual repo kicauwalet.com** apa adanya
> per hari ini. Tujuannya: supaya CMS terpusat di app.markaswalet.id bisa **menyesuaikan
> diri ke kenyataan situs ini**, bukan mengasumsikan sesuatu yang tidak ada.
> Semua angka di bawah diambil langsung dari isi repo, bukan perkiraan.

---

## 1. Identitas hosting — "aku dideploy pakai apa?"

**Jujur: aku sendiri tidak tahu siapa yang meng-host-ku.** Di dalam repo **tidak ada satu pun
konfigurasi deploy**:

- Tidak ada `.github/workflows` (tidak ada CI/CD).
- Tidak ada `netlify.toml`, `vercel.json`, `wrangler.toml`, `_redirects`, `_headers`.
- Tidak ada `CNAME`, `.htaccess`, `nginx.conf`, `Dockerfile`.
- Tidak ada `package.json` / `composer.json` / build tool apa pun.
- `README.md` isinya cuma satu baris: `# KicauWalet.com`.

Artinya: **repo ini = kumpulan file statis mentah.** Kemungkinan besar file-file HTML ini
disalin langsung ke suatu web server (nginx/Apache/cPanel) atau static host, secara manual.
**CMS tidak boleh berasumsi ada pipeline build.** Kalau mau publish otomatis, pipeline itu
**harus dibuat dulu** — belum ada.

---

## 2. Aku ini sebenarnya apa

**Aku adalah hasil _export statis_ dari sebuah situs WordPress — bukan WordPress yang hidup.**

- Sumber asli: **WordPress 6.0**, tema **Astra**, dibangun dengan **Oxygen Builder**
  **dan** **Elementor** (jejak keduanya ada di `wp-content/uploads`), SEO pakai **Rank Math**,
  form pakai **WPForms**.
- **Tidak ada PHP. Tidak ada database. Tidak ada `wp-admin`.** Yang tersisa hanya:
  - **57 file `index.html`** (satu per URL, pola permalink folder).
  - `wp-content/` (uploads + sisa CSS/JS tema & plugin) dan `wp-includes/` (aset statis).
  - 3 sitemap XML + feed.
- Google Tag Manager sudah tertanam: **`GTM-TNJNZPL`**.

**Konsekuensi penting:** konten dan tampilan **menyatu** di dalam tiap HTML. Tidak ada
"field judul", "field body", "field menu" yang terpisah — semuanya sudah jadi HTML jadi.
Ini kondisi paling tidak ramah CMS, dan **harus dipisah dulu** (lihat bagian 8).

---

## 3. Keterbatasan yang HARUS diterima CMS

1. **Aku tidak punya API dan tidak bisa "ditulis" saat runtime.** Aku file statis. Satu-satunya
   cara mengubahku adalah **mengubah file di repo ini lalu men-deploy ulang.**
2. **Tidak ada database untuk di-query.** Kalau CMS butuh daftar artikel, sumbernya adalah
   file, bukan tabel.
3. **Belum ada build step.** Perubahan file `.md`/`.json` tidak otomatis jadi HTML — perlu
   generator + pipeline yang belum ada.
4. **Header, menu, footer diduplikasi di 57 file** (lihat bagian 5). Selama belum di-refactor,
   "ubah 1 menu" = "tulis ulang 57 file". CMS jangan menyentuh 57 file; tunggu sampai chrome
   dijadikan 1 sumber.
5. **Ada kontaminasi sisa staging yang harus dibersihkan** sebelum go-live SEO:
   - Domain sandbox `oxygen-qpwj6xj8qwa8.oxygen-demo.qsandbox.me` masih tertanam di
     **55 dari 57 file** (canonical, `og:url`, JSON-LD schema).
   - Homepage masih `<meta name="robots" content="noindex, nofollow">` → menyuruh Google
     **tidak** mengindeks.
   - Locale salah: `en-US` padahal konten Bahasa Indonesia.
   - Brand lama masih nempel: footer tertulis "Suara Walet Handal, Jernih & Ori" &
     "Copyright © 2022 ... Powered by Lusmo Digital".
6. **Model konten harus multi-situs.** Aku hanya salah satu situs jaringan. Setiap record
   konten dari CMS wajib punya penanda `site` (mis. `kicauwalet`) supaya tidak tercampur
   dengan situs lain.

---

## 4. Peta URL — inilah struktur URL yang aku janjikan ke Google

Berdasarkan sitemap resmi: **16 halaman (pages), 30 artikel (posts), 1 kategori.**
URL relatif (permalink folder, tanpa `.html`):

### Pages (16) — dari `page-sitemap.xml`
```
/                          (homepage)
/berita/                   (index/arsip artikel)
/tentang/
/kontak/
/sample-page/              (bawaan WP — kandidat hapus)
# halaman "produk suara":
/suara-panggil-hexagonal/
/suara-panggil-lmb/
/suara-tarik-roving-room/
/suara-tarik-lubang-terjun/
/suara-inap/
# halaman "atribut/taksonomi" (dipakai sebagai page):
/durasi/  /format/  /frekuensi/
/lokasi/  /musim/   /waktu/
```

### Posts (30) — dari `post-sitemap.xml`, semua di kategori `berita`, author `syamsul-alam`
```
# Manfaat kesehatan/kecantikan (walet-untuk-*):
/walet-untuk-kesehatan/   /walet-untuk-kecantikan/   /walet-untuk-kecantikan-wajah/
/walet-untuk-kanker/      /walet-untuk-jerawat/       /walet-untuk-kehamilan/
/walet-untuk-kehamilan-2/ /walet-untuk-bayi/          /walet-untuk-batuk/
/walet-untuk-asma/        /walet-untuk-asam-lambung/  /walet-untuk-anak/
/walet-untuk-paru/        /walet-untuk-rambut/        /walet-untuk-vitalitas/
/walet-untuk-sesak-nafas/ /walet-untuk-gerd/          /walet-untuk-diabetes/
/walet-untuk-dbd/
# Jenis rumah walet (rumah-walet-*):
/rumah-walet-kayu/  /rumah-walet-beton/  /rumah-walet-baja-ringan/
/rumah-walet-asbes/ /rumah-walet-grc/
# Hama (hama-*):
/hama-cicak-walet/  /hama-kelelawar-walet/  /hama-tikus-walet/  /hama-sriti-walet/
# Lainnya:
/konsultan-walet/  /panen-sarang-walet-putih/
```

### Kategori (1)
```
/category/berita/   (+ /page/2/, /page/3/ untuk paginasi)
```

### Cruft URL bawaan WP (kandidat dibersihkan, JANGAN dianggap konten)
```
/hello-world/                 (post contoh WP)
/sample-page/                 (page contoh WP)
/category/uncategorized/
/author/admin/  /author/syamsul-alam/ (+ paginasi)
```

> **Catatan untuk CMS:** slug = sumber kebenaran SEO. Kalau CMS mengubah slug, **wajib**
> menyediakan redirect 301 dari slug lama. Struktur permalink saat ini: root-level
> (`/slug/`), **bukan** `/blog/slug/` atau `/berita/slug/`.

---

## 5. Chrome bersama (header / menu / footer) yang sekarang terduplikasi

**Footer: identik byte-per-byte di seluruh file** (hash sama persis di semua halaman). Isi
teksnya hardcoded: `Copyright © 2022 Suara Walet Handal, Jernih & Ori | Powered by Lusmo Digital`.

**Header/menu: struktur navigasi sama di semua file, hanya beda penanda halaman-aktif**
(`aria-current="page"`) dan kelas transparan di homepage. Jadi secara logis ini **SATU menu**
yang harus jadi **satu sumber data**; template yang menghitung status aktif per halaman.

**Struktur menu aktual (dengan dropdown):**
```
Home
Jenis            (menu induk, href="#")
  ├ Suara Panggil (href="#")
  │   ├ Hexagonal            → /suara-panggil-hexagonal/
  │   └ LMB (Lubang Masuk Burung) → /suara-panggil-lmb/
  ├ Suara Tarik   (href="#")
  │   ├ Roving Room          → /suara-tarik-roving-room/
  │   └ Lubang Terjun        → /suara-tarik-lubang-terjun/
  └ Suara Inap               → /suara-inap/
Kualitas         (href="#")
  ├ Durasi   → /durasi/
  ├ Format   → /format/
  └ Frekuensi→ /frekuensi/
Penerapan        (href="#")
  ├ Lokasi → /lokasi/
  ├ Musim  → /musim/
  └ Waktu  → /waktu/
Tentang → /tentang/
Kontak  → /kontak/
Berita  → /berita/
```

> **Yang CMS perlu kelola sebagai data (bukan HTML):**
> - `menu.json` — pohon menu di atas (label, url, anak, penanda induk non-link `#`).
> - `footer.json` — teks copyright, brand, kredit, (opsional) kolom & sosial.
> - `site.json` — logo, GTM ID (`GTM-TNJNZPL`), meta global, locale (`id-ID`), marquee
>   link antar-situs jaringan.
> - Logo saat ini: `/wp-content/uploads/2022/06/cropped-logo-kicau-walet-170x98.png`.

---

## 6. Tipe konten yang BENAR-BENAR aku punya

Hanya dua tipe nyata, plus konfigurasi:

### A. `Article` (post) — 30 buah
Field yang benar-benar ada di HTML sekarang (jadikan ini skema minimum):
| Field           | Contoh nyata                                                        |
|-----------------|---------------------------------------------------------------------|
| `title`         | "Mengobati Batuk Secara Alami dengan Sarang Walet"                  |
| `slug`          | `walet-untuk-batuk`                                                  |
| `meta_desc`     | "Saat musim hujan tiba, selain menyediakan payung untuk m…"         |
| `published_at`  | `2022-06-17T10:32:34+00:00`                                          |
| `modified_at`   | `2022-06-21T06:02:08+00:00`                                          |
| `category`      | `berita` (saat ini semua artikel hanya 1 kategori)                  |
| `author`        | `syamsul-alam` (juga ada `admin`)                                   |
| `cover_image`   | mis. `/wp-content/uploads/2022/06/Batuk-1.jpeg`                     |
| `body_html`     | isi artikel (HTML kaya: heading, paragraf, gambar)                 |
| `breadcrumb`    | Home › Berita › {judul}                                              |

Secara tema, artikel berkelompok: **walet-untuk-*** (manfaat), **rumah-walet-*** (jenis
bangunan), **hama-*** (hama). Ini kandidat **kategori/tag** yang lebih baik daripada semua
ditumpuk di `berita`.

### B. `Page` (halaman statis) — 16 buah
Homepage, Tentang, Kontak, halaman produk suara (Hexagonal/LMB/Roving Room/dll), dan
halaman atribut (Durasi/Format/Frekuensi/Lokasi/Musim/Waktu). Sebagian di antaranya
punya layout khusus (Oxygen/Elementor), jadi model `Page` sebaiknya mendukung
**body HTML/rich section**, bukan sekadar teks polos.

### C. Config (bukan konten, tapi wajib dikelola): `menu`, `footer`, `site` (lihat bagian 5).

> **Tidak ada** tipe lain (produk e-commerce, video, seminar, dll) di situs ini saat ini —
> meskipun CMS pusat punya banyak tipe. Untuk kicauwalet, mulai dari **Article + Page +
> Config** saja.

---

## 7. Kondisi media

- **Total `wp-content/uploads`: 1.966 file, 113 MB.**
- **Editorial (folder `2021/` + `2022/`): 1.870 file, ± 110 MB.** TAPI ini termasuk
  **turunan ukuran otomatis WordPress** (mis. `Batuk-1-300x200.jpeg`, `-768x512`, `-170x98`),
  jadi jumlah gambar **asli unik jauh lebih sedikit** dari 1.870.
- Komposisi: `jpg` 921, `jpeg` 791, `png` 164, `webp` 5, `svg` 9 — sisanya css/js/xml/json
  (aset plugin).
- **Sisa cruft plugin** (bukan media editorial, kandidat hapus): elementor 48, oxygen 24,
  rank-math 11, essential-addons 8, wpforms 4, wp-file-manager 1.
- **Kabar baik:** gambar dirujuk dengan **path relatif absolut** `"/wp-content/uploads/..."`,
  **bukan** domain sandbox. Jadi gambar tetap tampil setelah pindah domain — **tidak perlu
  rewrite URL gambar** (beda dengan canonical/og yang masih kotor).

> **Keputusan media untuk CMS:** apakah media tetap tinggal di repo (`wp-content/uploads`,
> path relatif dipertahankan) **atau** pindah ke media library terpusat (S3/Cloudinary) dengan
> URL absolut. Rekomendasi awal: **pertahankan path relatif `/wp-content/uploads/...`** dulu
> supaya 30 artikel yang ada tidak rusak; media baru dari CMS boleh pakai skema URL baru.

---

## 8. Intinya untuk disampaikan ke app.markaswalet.id

1. **Aku situs STATIS, bukan aplikasi.** Tidak ada API/DB/`wp-admin`. Satu-satunya cara
   mengubahku: **ubah file di repo GitHub `website-markas-walet/kicauwalet.com` lalu deploy.**
   → Model integrasi yang paling cocok: **CMS meng-commit DATA (bukan HTML) ke repo, lalu
   build statis regenerasi situs.**
2. **Pisahkan konten dari tampilan dulu.** Sekarang semuanya HTML baked & chrome diduplikasi
   57×. Sebelum CMS bisa "menyetir", repo ini harus di-refactor jadi
   **`content/ (Article, Page) + data/ (menu, footer, site) + templates/`** lewat static site
   generator (Astro/Eleventy). Ini pekerjaan sisi-ku, tapi CMS harus tahu bentuk akhirnya.
3. **Skema minimum yang aku butuhkan CMS hormati:**
   - `Article`: title, slug, meta_desc, published_at, modified_at, category[], author,
     cover_image, body_html, `site`.
   - `Page`: title, slug, meta_desc, body_html/sections, `site`.
   - `Menu` / `Footer` / `Site`: seperti bagian 5. Setiap record wajib bertanda `site`.
4. **Hormati kontrak URL & SEO.** Permalink root-level `/slug/`. Ganti slug ⇒ wajib redirect
   301. Bersihkan warisan staging (noindex, domain sandbox, locale en-US, brand lama).
5. **Media:** pertahankan `/wp-content/uploads/...` untuk konten lama; sepakati skema URL untuk
   media baru dari CMS.
6. **Yang BELUM ada dan perlu dibangun bersama:** (a) pipeline build+deploy, (b) mekanisme auth
   CMS→repo (GitHub App/token), (c) definisi field final ("content contract").

**Ringkasnya untuk CMS:** *"Perlakukan kicauwalet.com sebagai target publish statis berbasis
Git dengan skema Article + Page + Config bertanda `site`. Jangan asumsikan ada API atau
database. Kirim aku data terstruktur, biar repo-ku yang merender HTML."*
