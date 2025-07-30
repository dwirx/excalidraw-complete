# Panduan Deploy Excalidraw Cloudflare Worker (Versi Dashboard)

Dokumen ini akan memandu Anda untuk melakukan deploy layanan backend Excalidraw menggunakan Cloudflare Dashboard.

Worker ini menggunakan Cloudflare Durable Objects untuk menyimpan data kanvas secara persisten, sehingga Anda mendapatkan backend Excalidraw yang cepat dan skalabel.

## Ringkasan Metode Deploy

Kami menyediakan dua cara deploy melalui Cloudflare Dashboard:

1. **Deploy via Git Repository (Direkomendasikan):** Setelah setup, setiap update di repo Git Anda akan otomatis dideploy oleh Cloudflare. Ini adalah best practice untuk CI/CD.
2. **Copy-Paste Kode Manual:** Cara cepat untuk testing atau jika tidak ingin repo Git terhubung. Namun, setiap update harus dilakukan manual.

---

### Metode 1: Deploy via Git Repository (Direkomendasikan)

#### Langkah 1: Fork Repository

1. Login ke akun GitHub Anda.
2. Kunjungi repo `excalidraw-complete`, klik **Fork** di kanan atas untuk menyalin repo ke akun Anda.

#### Langkah 2: Buat & Hubungkan Aplikasi di Cloudflare

1. Login ke [Cloudflare Dashboard](https://dash.cloudflare.com/).
2. Pilih **Workers & Pages** di sidebar kiri.
3. Klik **Create application**, lalu pilih tab **Pages** dan klik **Connect to Git**.
4. Pilih repo hasil fork Anda, lalu klik **Begin setup**.

#### Langkah 3: Konfigurasi Build & Deploy

1. **Project name:** Bebas, sesuai keinginan.
2. **Production branch:** Pilih `main` atau branch utama repo Anda.
3. **Build settings:**
   - **Framework preset:** Pilih `None`.
   - **Build command:** Kosongkan.
   - **Build output directory:** Kosongkan.
   - **Root Directory:** Klik `(Advanced)`, isi dengan `cloudflare-worker`. **Penting!** Ini agar Cloudflare hanya deploy folder worker.
4. Klik **Save and Deploy** untuk mulai deploy pertama.

#### Langkah 4: Atur Environment Variable & Storage Worker

1. Di halaman project, buka tab **Settings**.
2. **Set API Key:**
   - Pilih submenu **Variables**.
   - Di bagian **Secret Variables**, klik **Add secret variable**.
   - **Variable name:** `API_TOKEN`
   - **Value:** Masukkan token rahasia yang Anda buat sendiri. Simpan baik-baik, nanti dipakai di frontend.
   - Klik **Save**.

#### Langkah 5: Redeploy untuk Terapkan Konfigurasi

1. Kembali ke tab **Deployments**.
2. Cari deploy terbaru, klik **...** di kanan, pilih **Retry deployment**.

Setelah sukses, Anda akan mendapatkan URL Worker di halaman utama project.

---

### Metode 2: Copy-Paste Kode Manual

#### Langkah 1: Ambil Kode Worker

1. Clone repo ini ke komputer Anda:
   ```bash
   git clone https://github.com/your-username/excalidraw-complete.git
   # Ganti your-username sesuai repo Anda
   ```
2. Buka file `cloudflare-worker/index.js`, copy seluruh isinya.

#### Langkah 2: Buat Worker di Cloudflare Dashboard

1. Login ke [Cloudflare Dashboard](https://dash.cloudflare.com/).
2. Pilih **Workers & Pages**.
3. Klik **Create application**, lalu pilih **Create Worker**.
4. Beri nama unik (misal: `my-excalidraw-backend`), klik **Deploy**.

#### Langkah 3: Paste & Deploy Kode

1. Setelah Worker dibuat, klik **Edit code**.
2. Hapus kode default "Hello World".
3. Paste kode dari `index.js` yang sudah Anda copy.
4. Klik **Save and Deploy**.

#### Langkah 4: Set API Key (Secret Variable)

1. Kembali ke halaman Worker, buka tab **Settings** > **Variables**.
2. Di **Secret Variables**, klik **Add secret variable**.
3. Isi:
   - **Variable name:** `API_TOKEN`
   - **Value:** Token rahasia buatan Anda (bisa pakai password generator). Simpan baik-baik untuk frontend.
4. Klik **Save**.

#### Langkah 5: Konfigurasi Penyimpanan (Durable Objects)

1. Di tab **Settings**, pilih **Durable Objects**.
2. Klik **Add binding**:
   - **Binding name:** `CANVAS_OBJECT`
   - **Durable Object class name:** `CanvasObject`
3. Tambah lagi:
   - **Binding name:** `INDEX_OBJECT`
   - **Durable Object class name:** `IndexObject`
4. Pastikan ada dua binding, pengaturan akan tersimpan otomatis.

#### Langkah 6: Finalisasi

Setelah semua diatur, kembali ke editor kode dan klik **Save and Deploy** lagi untuk memastikan semua konfigurasi aktif.

Setelah sukses, Anda akan mendapatkan URL Worker dengan format `https://<nama-worker>.<subdomain-anda>.workers.dev`.

---

## Konfigurasi Frontend Excalidraw

1. Buka aplikasi Excalidraw.
2. Masuk ke pengaturan/data source.
3. Pilih "Cloudflare KV" sebagai tipe penyimpanan.
4. **API Address:** Masukkan URL Worker yang didapat di langkah sebelumnya.
5. **API Token:** Masukkan `API_TOKEN` yang sudah Anda buat.
6. Simpan pengaturan.

---

Selamat! Excalidraw Anda kini menggunakan backend Cloudflare milik Anda sendiri.