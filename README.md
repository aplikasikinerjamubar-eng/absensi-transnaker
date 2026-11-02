# APEL ASN — Presensi (GitHub Pages + Google Apps Script)

Frontend (HTML/CSS/JS) di-hosting di **GitHub Pages**. Data tetap di **Google Spreadsheet**
lewat backend **Google Apps Script** (`backend/Code.gs`). Keduanya terhubung lewat `fetch()`.

```
index.html          halaman aplikasi
css/style.css       gaya
js/config.js        ← SATU-SATUNYA file yang perlu Anda edit (isi API_URL)
js/app.js           logika aplikasi
assets/logo.png     logo
backend/Code.gs     ← ditempel ke Google Apps Script (BUKAN dijalankan GitHub)
```

## Langkah 1 — Pasang backend di Google Apps Script
1. Buka Spreadsheet Anda → **Extensions → Apps Script**.
2. Ganti seluruh isi `Code.gs` dengan isi `backend/Code.gs` dari repo ini → **Save**.
3. (Opsional) Hapus file `Index.html` / `Publik.html` lama di proyek Apps Script; sudah tidak dipakai.
4. **Deploy → New deployment → ⚙ Web app**
   - Execute as: **Me**
   - Who has access: **Anyone**
5. Klik **Deploy**, setujui izin (Spreadsheet & Drive), lalu salin **Web app URL** (berakhiran `/exec`).
6. Tes: buka URL itu di browser → harus muncul JSON `{"success":true,"app":"APEL ASN",...}`.

> Jika nanti mengubah `Code.gs`: **Deploy → Manage deployments → ✏ Edit → Version: New version → Deploy**.
> Tanpa langkah ini, URL lama tetap menjalankan kode lama.

## Langkah 2 — Hubungkan frontend
Buka `js/config.js`, isi:
```js
window.APEL_CONFIG = {
  API_URL: 'https://script.google.com/macros/s/XXXXXXXX/exec',
  TIMEOUT_MS: 90000
};
```
Kosong = **MODE DEMO** (data contoh; muncul lencana kuning di bawah layar).

## Langkah 3 — Upload ke GitHub & aktifkan Pages
1. Buat repository baru di GitHub.
2. **Add file → Upload files** → seret **seluruh isi folder ini** (index.html, css, js, assets, backend, README.md) → **Commit**.
3. **Settings → Pages → Build and deployment → Source: Deploy from a branch → Branch: `main` / `(root)` → Save**.
4. Tunggu ±1 menit, alamat muncul: `https://<username>.github.io/<nama-repo>/`.

## Catatan keamanan
- Repo **publik** berarti `backend/Code.gs` ikut terlihat (berisi `SPREADSHEET_ID`, `FOLDER_ID`, `SALT`).
  Pilih salah satu: jadikan repo **Private** (Pages privat butuh GitHub Pro/Team), atau **jangan upload folder `backend/`**
  (cukup simpan di Apps Script).
- Password default akun ASN baru adalah `munabaratkab` — minta ASN menggantinya setelah login pertama.
- Hanya 35 fungsi di daftar putih `API_FUNCS` yang bisa dipanggil dari luar; fungsi admin tetap divalidasi token sesi.

## Pemecahan masalah
| Gejala | Penyebab / solusi |
|---|---|
| "Balasan server bukan JSON…" | Deploy belum **Anyone**, atau URL bukan `/exec`. Deploy ulang sesuai Langkah 1. |
| Perubahan `Code.gs` tidak berefek | Belum **New version** saat deploy ulang. |
| Muncul lencana MODE DEMO | `API_URL` di `js/config.js` masih kosong. |
| Login lambat pertama kali | Apps Script "cold start" ±2–5 detik; normal. |
