# i-CSR TaskTrack 2026

Sistem pemantauan tugasan bersama untuk Jawatankuasa i-CSR2026. Antara muka kekal menggunakan HTML, CSS dan JavaScript tanpa framework. Google Sheets ialah pangkalan data berpusat; localStorage tidak lagi menjadi sumber data tugasan.

## Kandungan

- `index.html` — aplikasi GitHub Pages responsif.
- `Code.gs` — backend Google Apps Script, setup pangkalan data, log aktiviti, kunci tulis dan kawalan versi.
- `seed-data.json` — salinan data asal untuk semakan migrasi.

## 1. Google Sheets

Spreadsheet bernama **i-CSR 2026 – Pemantauan Tugasan AJK** mengandungi tiga tab: `Portfolios`, `Tasks` dan `Activity_Log`. Data asal ialah 13 portfolio dan 150 tugasan. Portfolio Bahasa dan Penyuntingan Kandungan tidak mempunyai nama AJK dalam aplikasi asal dan sengaja tidak direka-reka.

## 2. Pasang Apps Script

1. Buka spreadsheet, pilih **Extensions → Apps Script**.
2. Gantikan kandungan editor dengan keseluruhan fail `Code.gs` dan simpan.
3. Di **Project Settings → Script properties**, tambah `SPREADSHEET_ID`: `1XEIv25onS0202NcJSS5SdZZiYqpYTBYlbIHksbYuSf4` (pilihan; nilai ini juga telah dipasang sebagai lalai dalam `Code.gs`).
4. Jalankan `setupICSRDatabase()` sekali. Benarkan akses Google apabila diminta. Fungsi ini idempoten: rekod sedia ada dikenal pasti melalui ID dan tidak digandakan.

## 3. Deploy Web App

1. Dalam Apps Script pilih **Deploy → New deployment**.
2. Jenis: **Web app**.
3. Execute as: **Me**.
4. Who has access: **Anyone**. Sistem ini tidak memerlukan log masuk.
5. Klik **Deploy**, beri kebenaran, kemudian salin URL yang berakhir dengan `/exec`.
6. Jika `Code.gs` diubah kemudian, pilih **Deploy → Manage deployments → Edit → New version → Deploy**.

## 4. Sambungkan frontend

Frontend telah dikonfigurasi dengan URL deployment `/exec` produksi. Muat naik `index.html` ke branch GitHub Pages dan buka https://faiz307.github.io/i-CSR_TaskTrack/.

## 5. Migrasi data lama

Data statik dalam aplikasi asal sudah dimasukkan ke dalam setup. Status atau catatan yang hanya berada dalam localStorage peranti pengguna tidak boleh dibaca dari jauh. Untuk rekod itu, buat sandaran daripada aplikasi lama, semak secara manual, kemudian kemas kini baris yang sepadan dalam tab `Tasks` tanpa menukar `task_id`. Jangan jalankan import tanpa semakan kerana Google Sheets kini sumber berautoriti.

## 6. Ujian

1. Jalankan `setupICSRDatabase()` dua kali dan sahkan kekal 13 portfolio/150 tugasan.
2. Buka aplikasi pada dua pelayar berasingan tanpa log masuk.
3. Pelayar A tanda tugasan selesai; pelayar B tekan **Segar Semula** dan sahkan perubahan.
4. Tambah tugasan dan edit catatan, kemudian semak baris pada `Tasks` dan `Activity_Log`.
5. Buka tugasan yang sama pada dua pelayar. Simpan pada A, kemudian simpan versi lama pada B. B mesti menerima konflik versi dan diminta menyegar semula.
6. Uji offline/URL salah dan pastikan indikator bertukar oren/merah serta tiada mesej kejayaan palsu.

## 7. Penyelesaian masalah

- **URL backend belum dikonfigurasi** — gantikan placeholder dalam `index.html`.
- **NOT_CONFIGURED** — semak Script Properties dan jalankan setup.
- **VERSION_CONFLICT** — pengguna lain telah menyimpan perubahan; segar semula.
- **Respons backend tidak sah** — pastikan URL berakhir `/exec`, deployment aktif, dan versi terkini telah dideploy.
- **CORS/redirect** — frontend menghantar POST `text/plain` untuk mengelakkan preflight dan membenarkan redirect Apps Script. Jangan tukar kepada header JSON tersuai tanpa menguji semula.

## 8. Keselamatan

GitHub Pages dan endpoint Apps Script ialah awam. Tiada log masuk: sesiapa yang mengetahui pautan boleh membaca, menambah dan mengemas kini tugasan. Gunakan hanya jika tahap akses terbuka ini memang dikehendaki. Untuk identiti per pengguna, MFA, peranan atau audit pengguna, gunakan Firebase Authentication/Firestore atau aplikasi dalaman beridentiti UiTM.

## 9. Penyelenggaraan

Jangan padam baris tugasan atau portfolio. Tambah tugasan melalui aplikasi. Sandarkan spreadsheet secara berkala melalui **File → Make a copy**. Sebelum mengubah skema, uji pada salinan spreadsheet dan deployment Apps Script berasingan.
