# INSTALL.md — Ringkasan Instalasi SIMGOS v2

Dokumen ringkasan ini dibuat dari dokumentasi resmi SIMGOS v2 dan halaman produk. Untuk petunjuk lengkap, langkah demi langkah, dan file contoh (docker-compose.yml, skrip backup) selalu rujuk ke dokumentasi resmi:

- Halaman produk/pengantar SIMGOS: https://keslan.kemkes.go.id/simgos
- Dokumentasi teknis & instalasi SIMGOS v2: https://docs.simgos2.simpel.web.id/

Catatan: ini adalah ringkasan yang ditujukan untuk mempersingkat proses persiapan dan deployment; gunakan dokumen resmi di atas sebagai sumber otoritatif.

1. Persyaratan Sistem (ringkasan)
- Sistem operasi yang direkomendasikan: RockyLinux 9 (x86_64 minimal). (Sumber: docs.simgos2 - Sistem Operasi)
  - Referensi: https://docs.simgos2.simpel.web.id/docs/instalasi/sistem-operasi/
- Docker & Docker Compose (jika akan menggunakan metode containerized).
- Database: ikuti rekomendasi pada dokumentasi instalasi (DB engine dan versi tercantum di docs instalasi).
- Spesifikasi hardware: cek dokumentasi resmi untuk rekomendasi CPU, RAM, dan storage untuk lingkungan uji dan produksi.
  - Dokumentasi instalasi: https://docs.simgos2.simpel.web.id/docs/instalasi/

2. Metode Deployment yang didukung (ringkasan)
- Docker / Docker Compose (direkomendasikan untuk instalasi cepat & konsisten)
  - Siapkan Docker Engine dan Docker Compose sesuai versi yang direkomendasikan oleh dokumentasi sistem operasi/distro.
  - Tempatkan file `docker-compose.yml` (contoh disediakan pada dokumentasi) dan jalankan `docker compose up -d`.
- Manual (instalasi langsung pada OS tanpa container)
  - Ikuti panduan instalasi dependensi, database, konfigurasi environment, dan service management di halaman instalasi.
- Hybrid: instalasi OS + menjalankan container adalah pendekatan umum yang didokumentasikan.

Referensi deployment: https://docs.simgos2.simpel.web.id/docs/instalasi/

3. Langkah Persiapan (singkat)
- Siapkan server fisik/VM dengan RockyLinux 9 (atau distro Linux yang kompatibel).
- Perbarui sistem: `dnf update -y`.
- Pasang Docker Engine dan Docker Compose (lihat petunjuk resmi Docker untuk distro Anda).
- Pastikan firewall/port yang diperlukan (contoh: 80/443 untuk web, port DB sesuai konfigurasi) terbuka sesuai kebijakan jaringan.

4. Contoh langkah cepat (Docker Compose)
1. Clone repo atau ambil file `docker-compose.yml` dari dokumentasi resmi.
2. Sesuaikan file `.env` atau konfigurasi environment dengan kredensial database, domain, dan sertifikat TLS jika ada.
3. Jalankan: `docker compose pull` lalu `docker compose up -d`.
4. Periksa log: `docker compose logs -f` untuk validasi service.

(Langkah detail dan contoh `docker-compose.yml` serta variable environment silakan ambil dari dokumentasi resmi: https://docs.simgos2.simpel.web.id/)

5. Backup & Restore (ringkasan)
- Backup database: gunakan tool yang sesuai (mysqldump / pg_dump sesuai DB engine yang dipakai). Simpan dump di lokasi yang aman. Enkripsi backup bila diperlukan.
- Backup file/volume aplikasi: untuk deployment Docker, backup volume atau data direktori yang menyimpan file uploaded, konfigurasi, atau storage persistent.
- Restore: kembalikan file aplikasi dan import dump database, lalu jalankan migrasi jika diperlukan.
- Dokumentasi backup/restore (detail perintah dan path volume): lihat halaman backup di dokumentasi resmi.
  - Rujukan: https://docs.simgos2.simpel.web.id/

6. Integrasi & Identitas Pasien
- SIMGOS mendukung pemetaan identifier nasional (NIK) dan integrasi konsep ke SatuSehat/BPJS; untuk integrasi teknis (payload, endpoint, auth) rujuk dokumentasi integrasi/naskah teknis atau tim integrasi nasional.
  - Produk (overview): https://keslan.kemkes.go.id/simgos#integrasi
  - Dokumentasi teknis: https://docs.simgos2.simpel.web.id/

7. Dukungan & Kanal Komunitas
- Dokumentasi resmi: https://docs.simgos2.simpel.web.id/
- Halaman produk & FAQ: https://keslan.kemkes.go.id/simgos
- Kanal komunitas (mis. Telegram) dan kontak dukungan dicantumkan pada halaman produk: https://keslan.kemkes.go.id/simgos#dukungan

8. Keamanan & Praktik Baik
- Selalu gunakan TLS/HTTPS untuk akses produksi.
- Enkripsi backups, batasi akses ke kredensial DB, dan gunakan RBAC untuk aplikasi.
- Jalankan security scan pada dependencies dan ikuti rekomendasi dokumentasi.

9. Referensi & tautan penting
- Halaman produk SIMGOS (overview, fitur, FAQ, dukungan): https://keslan.kemkes.go.id/simgos
- Dokumentasi teknis & instalasi SIMGOS v2: https://docs.simgos2.simpel.web.id/

--

Jika Anda ingin, saya dapat mengekstrak langkah instalasi baris-per-baris dari sub-halaman spesifik di docs.simgos2 (misal: file docker-compose.yml contoh, skrip instal, perintah backup/restore) dan menaruhnya secara lengkap di /docs/INSTALL.md serta membuat PR terpisah. Beri tahu sub-halaman mana yang ingin Anda sertakan (contoh: sistem-operasi, docker-compose, backup-restore).