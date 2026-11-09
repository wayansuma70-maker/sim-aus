# Panduan Instalasi SIM-AUS (GAS REST API + GitHub Pages + PWA)

## A. Backend (Google Apps Script)
1. Buka https://script.google.com → **Proyek baru** → ganti isi `Code.gs` dengan isi berkas **Kode.gs**.
2. Pilih fungsi `setupAppEnvironment` → ▶ **Run** → izinkan akses. **Jalankan sekali saja** (aman diulang, tidak menimpa rahasia).
3. Buka **Execution log**. Catat baris `🔐 AKSES DARURAT → darurat / <sandi>` (hanya tampil sekali).
4. Cek Google Drive: folder `📁 SIM-AUS` (berisi database dan folder `🔒 Aset Sekolah`). Email Anda otomatis terdaftar sebagai **admin** di sheet `Pengguna`.
5. **Deploy → New deployment → Web app** — *Execute as*: **Me**, *Who has access*: **Anyone** → Deploy → salin URL `/exec`.
6. Setiap mengubah Kode.gs: **Deploy → Manage deployments → ✏️ → New version** (bukan New deployment).

## B. Frontend (GitHub Pages)
1. Ekstrak ZIP. Folder hasil ekstraksi **`sim-aus-frontend`** langsung berisi `index.html` — **inilah folder yang di-`git init`**.
2. Edit `js/config.js`: tempel URL `/exec` pada `GAS_URL`.
3. Buat repository **Public** di github.com (jangan centang README). Lalu di terminal:
```
cd sim-aus-frontend
dir            # pastikan index.html tampil di daftar teratas (Mac/Linux: ls)
git init
git add .
git commit -m "SIM-AUS pertama"
git branch -M main
git remote add origin https://github.com/USERNAME/NAMA-REPO.git
git push -u origin main
```
4. Repo → **Settings → Pages** → Branch `main` / `/ (root)` → Save. Situs: `https://USERNAME.github.io/NAMA-REPO/`.
5. Update berikutnya: `git add .` → `git commit -m "..."` → `git push`, lalu Ctrl+Shift+R.

## C. Login Google (disarankan)
1. Google Cloud Console → *APIs & Services → Credentials → OAuth client ID (Web)*. Authorized JavaScript origins: `https://USERNAME.github.io`.
2. Masuk SIM-AUS dengan **akses darurat** → *Pengaturan Sekolah → Integrasi* → isi **Google Client ID** → Simpan.
3. Tambahkan email pengguna (admin/tu/kepsek/guru) di menu **Pengguna**. Email Guru harus sama dengan kolom `email` di Data Guru agar jadwal pengawas pribadinya tampil.
4. **Segera** setelah login pertama, ganti sandi darurat: jalankan `resetAksesDarurat()` dari editor dan catat sandi baru.

## D. WhatsApp (Fonnte) & CRM
Daftar di fonnte.com → Device → scan QR → salin token → *Pengaturan Sekolah → Integrasi → Token Fonnte*. Di menu **CRM & WhatsApp**: centang kontak → tulis pesan (`{nama}`) → Kirim. Server mengirim 20 pesan/menit lewat trigger yang dipasang otomatis; kontak *opt-out* tidak pernah dikirimi. Mulai dari batch kecil dan hindari tautan sama ke ratusan orang sekaligus (risiko banned).

## E. Pasang di HP (PWA)
Buka situs di Chrome Android → tombol **Pasang di HP** (atau menu ⋮ → *Install app*). iPhone: Safari → Bagikan → *Add to Home Screen*.

## F. Impor data lama
*Pengaturan Sekolah → Impor*: tempel URL spreadsheet lama (akun Google yang sama) → **Pindai (dry-run)** → **Jalankan impor**. Sumber hanya dibaca; aman diulang (kunci: NISN/NIP/nomor ruang/nama mapel). Jalankan ulang tepat sebelum pindah (delta sync). Data kecil juga bisa lewat **Import CSV** di tiap tabel Data Master (header = nama kolom).

## G. Keamanan & batas
- Rahasia (token Fonnte, Client ID, sesi, hash sandi) hanya di **Script Properties**; tidak ada di kode, sheet, atau browser. Jangan menambah editor proyek Apps Script sembarangan dan jangan membagikan spreadsheet database.
- Ttd & cap disimpan di folder Drive terbatas; hanya admin/TU/kepsek yang dapat memuatnya.
- Sesi berlaku 12 jam. Rotasi paksa semua sesi: jalankan `rotasiSecretSesi()`.
- Kuota trigger akun gratis ±90 menit/hari; trigger antrean hanya bekerja bila ada pesan menunggu.
