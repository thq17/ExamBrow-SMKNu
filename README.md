# ExamBrow SMKNU 1.0

Project Android Studio untuk aplikasi exam browser berbasis WebView.

## Fitur
- Menu Setting untuk menyimpan URL ujian.
- PIN admin untuk membuka Setting dan keluar dari ujian.
- WebView dengan JavaScript dan DOM storage untuk kompatibilitas situs ujian.
- Layar imersif dan pencegahan tombol Back saat ujian.
- Memanggil Android Lock Task Mode. Kiosk penuh membutuhkan provisioning Device Owner oleh administrator.
- URL dan PIN disimpan lokal di SharedPreferences.

**PIN awal:** `123456`. Segera ubah dari menu Setting saat pertama kali digunakan.

## Struktur
- `app/src/main/java/id/sch/smknu/exambrow/MainActivity.kt` — UI, WebView, setting, PIN, mode ujian.
- `app/src/main/java/id/sch/smknu/exambrow/AdminReceiver.kt` — receiver administrasi perangkat.
- `app/src/main/AndroidManifest.xml` — deklarasi aplikasi dan receiver.
- `app/src/main/res/xml/device_admin_policies.xml` — kebijakan device admin.
- `app/build.gradle` — konfigurasi build.

## Build APK
1. Instal Android Studio versi stabil dan Android SDK Platform 35.
2. Ekstrak ZIP ini.
3. Android Studio → **Open** → pilih folder `ExamBrow-SMKNu`.
4. Tunggu Gradle Sync selesai. Project menggunakan Android Gradle Plugin 8.7.3 dan Kotlin 2.0.21; koneksi internet diperlukan untuk mengunduh komponen build.
5. Pilih **Build → Build APK(s)**.
6. APK debug biasanya tersedia di `app/build/outputs/apk/debug/app-debug.apk`.
7. Salin APK ke perangkat Android, izinkan instalasi dari sumber tersebut bila diminta, lalu pasang.

Build membutuhkan koneksi internet untuk mengunduh Gradle/plugin Android jika belum tersedia di komputer.

## Konfigurasi kiosk penuh (opsional, admin sekolah)
Kiosk penuh Android membutuhkan aplikasi diizinkan dalam Lock Task Mode dan perangkat disiapkan sebagai Device Owner. Cara ADB di bawah biasanya hanya dapat dipakai pada perangkat baru di-reset/tanpa akun dan kebijakan yang bertentangan. **Perintah ini dapat mengubah pengelolaan perangkat; cadangkan data dan gunakan hanya perangkat milik sekolah.**

1. Aktifkan Developer options dan USB debugging.
2. Hubungkan perangkat ke komputer yang sudah memasang Android Platform Tools.
3. Instal APK lebih dulu, lalu jalankan:

```bash
adb shell dpm set-device-owner id.sch.smknu.exambrow/.AdminReceiver
```

Jika berhasil, buka aplikasi dan tekan **Mulai Ujian**. Aplikasi akan meminta Lock Task Mode; sebagai Device Owner, aplikasi mengizinkan dirinya sendiri ke daftar kiosk sebelum memulai.

Untuk perangkat yang sudah dikelola sekolah, gunakan EMM/Android Management API atau proses provisioning resmi sekolah. Jika perintah gagal, jangan mencoba mengakali kebijakan perangkat; gunakan perangkat khusus yang sesuai.

## Catatan keamanan dan batasan
- PIN default `123456` harus segera diganti. PIN disimpan lokal untuk prototipe; bukan penyimpanan kredensial tingkat tinggi.
- Aplikasi tidak mengumpulkan atau mengirim data siswa.
- Ini bukan sistem anti-kecurangan sempurna. Kiosk tidak otomatis mencegah kamera eksternal, perangkat kedua, atau semua metode capture pada semua perangkat.
- Situs ujian harus dapat dibuka melalui HTTP/HTTPS. Beberapa login pihak ketiga atau alur pop-up dapat memerlukan penyesuaian WebView.
- `usesCleartextTraffic` di manifest mengizinkan HTTP untuk kompatibilitas situs lama. Sebaiknya gunakan URL HTTPS; hapus atribut tersebut jika sekolah hanya memakai HTTPS.
- Untuk distribusi produksi, sebaiknya tambahkan logo sekolah, signing key rilis, kebijakan update, pengujian di model Android yang digunakan, serta pengelolaan kiosk via Device Owner/EMM.


## Build APK tanpa komputer (cloud build)
Folder `.github/workflows/build-apk.yml` disiapkan untuk GitHub Actions. Setelah seluruh isi project dimasukkan ke repository GitHub dengan branch `main`, buka tab **Actions**, pilih **Build ExamBrow APK**, tekan **Run workflow**, lalu buka hasil run yang berhasil dan unduh artifact `ExamBrow-SMKNu-debug-apk`. Artifact berbentuk ZIP yang berisi `app-debug.apk`.
