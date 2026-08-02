# PLAN: SIMGOS — Modul Klinik / Rumah Sakit Kelas D Pratama (Rawat Inap)

Dokumen ini adalah single-file specification-driven development (SDD) yang disesuaikan berdasarkan sumber resmi SIMGOS dan dokumentasinya:

- Sumber utama informasi produk: https://keslan.kemkes.go.id/simgos
- Dokumentasi teknis dan user guide: https://docs.simgos2.simpel.web.id/

Tujuan: menyesuaikan rencana pengembangan dan analisis SWOT proyek SIMGOS (modul rawat inap untuk fasilitas Kelas D Pratama) agar selaras dengan kebijakan, fitur, dan pedoman implementasi yang dipublikasikan oleh Kementerian Kesehatan.

> Catatan: bagian yang disesuaikan merujuk pada praktik, integrasi, dan dukungan yang disebutkan di situs kementerian dan dokumentasi resmi. Sebelum rilis ke produksi, lakukan verifikasi lapangan dengan Dinas/Kementerian terkait untuk persetujuan integrasi nasional.

## 1. Ringkasan Singkat (disesuaikan)

SIM GOS (Sistem Informasi Manajemen Generik Open Source) adalah inisiatif Kementerian Kesehatan untuk menyediakan solusi informasi kesehatan berbasis open source bagi fasilitas kesehatan. Modul yang direncanakan di sini menargetkan fasilitas rawat inap skala kecil (Kelas D Pratama) dengan kebutuhan dasar admisi, pengelolaan bed, rekam medis singkat, farmasi sederhana, dan administrasi keuangan. Dokumentasi teknis SIMGOS v2 tersedia di subdomain docs yang memuat panduan instalasi, konfigurasi, dan integrasi.

Sasaran utama:
- Mempermudah adopsi SIMGOS di fasilitas kelas D dengan installer ringan dan dokumentasi yang konsisten dengan pedoman kementerian.
- Menjamin interoperabilitas dasar (pemetaan data pasien dan encounter) menuju integrasi nasional (SatuSehat/BPJS) sesuai roadmap Kemenkes.

## 2. Perubahan & Penyesuaian dari PLAN sebelumnya

Berdasarkan review situs kementerian dan docs resmi, dokumen ini menyesuaikan:
- Menegaskan kompatibilitas dan persyaratan integrasi nasional (SatuSehat/BPJS) sebagai tujuan jangka menengah.
- Menambahkan kebutuhan registrasi identitas nasional (NIK) dan opsi mapping ke registri nasional yang disarankan.
- Memasukkan kontak dukungan operasional dan mekanisme pelaporan kesalahan menurut pola kementerian (mis. channel helpdesk/WhatsApp bila tersedia) sebagai bagian proses implementasi.
- Menyesuaikan rekomendasi deployment agar konsisten dengan panduan instalasi yang ada di docs.simgos2.simpel.web.id (mis. paket instalasi di Docker/Compose atau langkah instal manual yang tercantum).

## 3. Spesifikasi Ringkas (API-first / MVP)

Fitur inti (MVP):
- Registrasi pasien (NIK optional tapi direkomendasikan), validasi, de-duplikasi.
- Admission rawat inap: assign ward/room/bed, track status bed.
- Catatan klinis singkat (SOAP) per admission.
- Farmasi: resep sederhana, pengurangan stok, label/print drug dispensing.
- Billing dasar: per-hari inap, obat, tindakan, pembayaran.
- Laporan & monitoring: okupansi, lama rawat, penggunaan obat, status integrasi nasional.

API endpoints (ringkas):
- POST /api/patients
- GET /api/patients?query=...
- POST /api/admissions
- PATCH /api/admissions/:id/discharge
- CRUD /api/wards, /api/rooms, /api/beds
- POST /api/admissions/:id/notes
- POST /api/admissions/:id/prescriptions
- GET /api/reports/occupancy

Gunakan OpenAPI/Swagger untuk menyelaraskan API dengan dokumentasi eksternal agar mempermudah integrasi.

## 4. Verifikasi Teknis yang Disarankan (berdasarkan docs)

Sebelum deployment pilot:
1. Verifikasi requirement sistem (OS, DB, PHP/Node/Python runtime) sesuai `docs.simgos2.simpel.web.id`.
2. Pastikan konfigurasi integrasi (jika diaktifkan) menggunakan format dan endpoint yang direkomendasikan oleh Kemenkes — catat mapping identifier (NIK, BPJS, local_patient_id).
3. Uji kompatibilitas modul backup/restore seperti yang didokumentasikan: backup DB, enkripsi backup, dan prosedur restore di staging.
4. Validasi alur pelaporan dan dukungan: dokumen kontak bantuan, template laporan bug/insiden, dan SLA awal untuk pilot.

## 5. Comprehensive SWOT Analysis (disesuaikan dengan sumber)

Strengths (Kekuatan):
- Legitimasi dan dukungan kementerian: SIMGOS adalah inisiatif resmi Kemenkes sehingga memudahkan adopsi di fasilitas publik dan sinergi dengan program nasional.
- Open-source dan dokumentasi resmi: docs.simgos2 menyediakan panduan instalasi dan operasional yang dapat dipakai langsung oleh tim implementasi.
- Fokus pada kebutuhan dasar: modul dirancang untuk lingkungan kelas D dengan fitur esensial, mengurangi kompleksitas implementasi.
- Integrasi nasional berpotensi: disain untuk kompatibilitas SatuSehat/BPJS mempermudah akses klaim/standarisasi data.

Weaknesses (Kelemahan):
- Keterbatasan fungsional dibanding solusi komersial penuh; beberapa kebutuhan klinis kompleks tidak tersedia di MVP.
- Bergantung pada kapasitas TI lokal: instalasi dan pemeliharaan butuh tenaga IT yang mungkin terbatas di fasilitas kecil.
- Dokumentasi kadang teknis: meski tersedia, beberapa panduan deployment memerlukan pengetahuan sysadmin/DevOps untuk diikuti.

Opportunities (Peluang):
- Dukungan program pemerintah untuk digitalisasi kesehatan membuka peluang pendanaan dan adopsi luas.
- Komunitas pengembang lokal dapat berkontribusi pada modul tambahan (laporan lokal, integrasi lab sederhana).
- Fase integrasi FHIR/SatuSehat dapat membuka interoperabilitas dan layanan tambahan (klaim otomatis, statistik nasional).

Threats (Ancaman):
- Risiko keamanan dan kebocoran data sensitif; fasilitas kecil mungkin kurang proteksi dan mengundang insiden.
- Pergeseran kebijakan/regulasi yang menuntut sertifikasi/standar tambahan (mis. aturan keamanan atau penyimpanan data) dapat menambah beban.
- Kompetisi dari vendor yang menawarkan solusi turnkey, dukungan komersial, dan UX lebih matang.

Rekomendasi berbasis SWOT:
- Prioritaskan proses onboarding (installer Docker-compose, checklist instalasi) dan materi pelatihan singkat untuk mengurangi hambatan teknis.
- Terapkan kebijakan keamanan dasar (HTTPS, hashing, role-based access, backup terenkripsi) sebagai default instalasi.
- Rencanakan roadmap integrasi bertahap: tahap 1 = operasional lokal; tahap 2 = interoperability SatuSehat minimal (Patient, Encounter); tahap 3 = klaim/automasi BPJS.

## 6. Review & Verifikasi Konten (Checklist)

- [ ] Cross-check field mandatory pasien (NIK, nama, DOB) dengan format yang disarankan di situs Kemenkes.
- [ ] Pastikan instruksi instalasi sesuai contoh di docs.simgos2 (install scripts, dependency list).
- [ ] Validasi semua API yang akan dipakai untuk integrasi (payload schema, auth flow).
- [ ] Jalankan security baseline: TLS, CSP, rate-limit, dependency scan.
- [ ] Lakukan user acceptance test (UAT) bersama tim klinis di pilot site.

## 7. Rencana Tindak Lanjut (Actionable)

1. Sinkronisasi dengan dokumentasi resmi: ambil checklist instalasi & requirements dari https://docs.simgos2.simpel.web.id dan simpan ke /docs/INSTALL.md di repo.
2. Tambahkan contoh OpenAPI skeleton yang mencerminkan endpoint utama dan field yang diperlukan untuk integrasi nasional.
3. Siapkan migration DB awal yang menyertakan kolom penting sesuai mapping nasional (mis. field untuk NIK, bpjs_no, ext_ids).
4. Buat panduan operator singkat (1 halaman per peran) dan template laporan insiden/permintaan dukungan.
5. Lakukan pilot deployment di satu fasilitas kelas D dengan observability untuk 4 minggu, lalu kumpulkan feedback.

## 8. Catatan Akhir

Dokumen ini menggabungkan rencana teknis sebelumnya dengan penyesuaian yang relevan berdasarkan pedoman dan dokumentasi resmi SIMGOS dari Kementerian Kesehatan. Semua integrasi ke sistem nasional harus melalui proses verifikasi resmi dan persetujuan pihak terkait. Untuk langkah selanjutnya saya dapat:

- Menambahkan OpenAPI skeleton dan migration files (pilih stack: Laravel/Django/Express).
- Membuat /docs/INSTALL.md yang memuat ringkasan langkah instalasi dari docs.simgos2.
- Menyusun checklist pilot dan template UAT.

Pilih langkah yang ingin saya kerjakan dan stack yang diinginkan — saya akan commit perubahan berikutnya ke path `files/PLAN.md` di branch baru jika Anda setuju.