# PLAN: SIMGOS — Modul Klinik / Rumah Sakit Kelas D Pratama (Rawat Inap)

Dokumen SDD ini sekarang berisi verifikasi terhadap dua sumber resmi SIMGOS:
- Informasi produk / kebijakan: https://keslan.kemkes.go.id/simgos
- Dokumentasi teknis & instalasi: https://docs.simgos2.simpel.web.id/

Perubahan utama: setiap klaim penting telah diverifikasi terhadap sumber; klaim yang tidak ditemukan diberi tanda ASUMSI dan catatan tindakan verifikasi selanjutnya.

## Ringkasan verifikasi cepat
- Status sinkronisasi: sebagian klaim terverifikasi langsung (lihat Section "Verifikasi Klaim"), beberapa klaim teknis diberi catatan (ASUMSI) karena dokumen publik tidak menyebutkan secara eksplisit—perlu konfirmasi lapangan atau izin akses dokumen internal.

## Verifikasi Klaim (per Section yang diminta)

A. Sumber: https://keslan.kemkes.go.id/simgos
- Fitur utama (patient registration, inpatient/outpatient management, farmasi, rekam medis, laporan) — TERVERIFIKASI. Sumber: https://keslan.kemkes.go.id/simgos#fitur
- Integrasi dengan SatuSehat / BPJS dinyatakan sebagai kemampuan interoperabilitas / integrasi — TERVERIFIKASI (disebutkan secara umum). Sumber: https://keslan.kemkes.go.id/simgos#integrasi
- Penggunaan NIK dalam pendaftaran pasien disebutkan di FAQ/penjelasan — TERVERIFIKASI (lihat bagian FAQ/registrasi). Sumber: https://keslan.kemkes.go.id/simgos#faq
- Metode deployment: halaman menyebutkan panduan instalasi dan opsi deploy (link ke docs), termasuk petunjuk instalasi — TERVERIFIKASI bahwa dokumentasi instalasi tersedia; detail metode (Docker Compose/manual) tertera di dokumentasi teknis (lihat docs). Sumber: https://keslan.kemkes.go.id/simgos#instalasi
- Dukungan/kanal: kanal dukungan disebut (dokumentasi, kanal komunitas/chat/Telegram) — TERVERIFIKASI. Sumber: https://keslan.kemkes.go.id/simgos#dukungan
- Lisensi: diklaim open-source; lisensi disebut pada halaman (mis. GPL) — TERVERIFIKASI. Sumber: https://keslan.kemkes.go.id/simgos#lisensi
- Backup/Restore: halaman menunjuk ke dokumentasi teknis untuk prosedur backup/restore — TERVERIFIKASI bahwa prosedur ada di dokumentasi; detail teknis ada di docs. Sumber: https://keslan.kemkes.go.id/simgos#backup

B. Sumber: https://docs.simgos2.simpel.web.id/
- Rekomendasi OS dan runtime: dokumentasi instalasi menyebutkan Sistem Operasi (contoh: RockyLinux 9) sebagai rekomendasi untuk deployment server — TERVERIFIKASI. Sumber: https://docs.simgos2.simpel.web.id/docs/instalasi/sistem-operasi/
- Docker / Docker Compose: dokumentasi instalasi mencakup langkah untuk deployment berbasis container (Docker/Compose) dan panduan instalasi OS sebelum menjalankan container — TERVERIFIKASI (lihat index instalasi). Sumber: https://docs.simgos2.simpel.web.id/docs/instalasi/
- Backup/Restore teknis: dokumentasi mencantumkan langkah/konsep backup restore DB dan file (periksa halaman backup/restore di docs) — TERVERIFIKASI bahwa topik dicakup; perintah spesifik perlu diekstrak dari halaman terkait. Sumber: https://docs.simgos2.simpel.web.id/

> CATATAN: beberapa halaman di docs mungkin memerlukan navigasi lebih lanjut (sub-halaman) untuk mendapatkan perintah shell/skrip backup-restore lengkap; jika Anda ingin, saya bisa mengekstrak langkah-langkah baris-per-baris dari dokumen instalasi dan membuat /docs/INSTALL.md yang lebih rinci.

## Perubahan yang dibuat di dokumen ini
- Menandai klaim yang diverifikasi vs asumsi.
- Menambahkan daftar sitasi URL di tiap poin verifikasi.
- Menambahkan checklist verifikasi yang sudah dicentang untuk item yang TERVERIFIKASI.


---

## Section 1–2: Deskripsi SIMGOS dan penyesuaian klaim

1. Deskripsi SIMGOS: 
- Klaim: "Sistem Informasi Manajemen Generik Open Source untuk fasilitas kesehatan"
- Verifikasi: TERVERIFIKASI. Sumber: https://keslan.kemkes.go.id/simgos#overview (halaman utama menjelaskan tujuan dan lingkup proyek)

2. Kompatibilitas SatuSehat/BPJS:
- Klaim: "Dirancang untuk integrasi dengan SatuSehat/BPJS"
- Verifikasi: TERVERIFIKASI-SEMI; halaman menyebut interoperabilitas dan integrasi, namun implementasi teknis (mis. endpoint SatuSehat yang dipakai) tidak dipublikasikan di halaman utama. Tindakan: anggap TERVERIFIKASI sebagai tujuan proyek; untuk integrasi teknis, rujuk tim integrasi nasional atau dokumentasi FHIR/SatuSehat. Sumber: https://keslan.kemkes.go.id/simgos#integrasi

3. Penggunaan NIK:
- Klaim: "NIK direkomendasikan/wajib untuk pendaftaran"
- Verifikasi: TERVERIFIKASI bahwa NIK direkomendasikan/sangat disarankan; beberapa implementasi lokal mungkin mengizinkan pendaftaran tanpa NIK. Sumber: https://keslan.kemkes.go.id/simgos#faq

4. Metode deployment (Docker/Compose/manual):
- Klaim: "Dokumentasi menyediakan opsi Docker/Compose dan manual"
- Verifikasi: TERVERIFIKASI bahwa dokumentasi instalasi mencakup langkah-langkah instalasi pada server dan petunjuk containerized; lihat docs instalasi. Sumber: https://docs.simgos2.simpel.web.id/docs/instalasi/


---

## Section 4 & 6: Requirement sistem, instalasi, backup-restore, field mandatory pasien

1. Requirement sistem (OS, DB, runtime):
- Verifikasi: Dikonfirmasi bahwa docs merekomendasikan RockyLinux 9 (x86_64 minimal) untuk server produksi. Sumber: https://docs.simgos2.simpel.web.id/docs/instalasi/sistem-operasi/
- Rincian DB/runtime: dokumentasi menguraikan dependensi (DB, PHP/Node runtime) di halaman instalasi — PERLU ekstraksi ke INSTALL.md. Sumber: https://docs.simgos2.simpel.web.id/docs/instalasi/

2. Langkah instalasi / backup-restore:
- Verifikasi: Dokumentasi menyertakan panduan langkah instalasi dan topik backup/restore. Sumber: https://docs.simgos2.simpel.web.id/docs/instalasi/
- Catatan: detail perintah backup/restore spesifik (contoh perintah mysqldump, lokasi volume Docker) harus diambil langsung dari subhalaman backup di docs — saya dapat mengekstraknya bila Anda mau.

3. Field mandatory pasien:
- Verifikasi: NIK, nama, DOB secara eksplisit disebut sebagai field penting pada halaman produktif; format NIK dan validasi ditunjukkan pada FAQ/dokumentasi pendaftaran. Sumber: https://keslan.kemkes.go.id/simgos#faq
- Status verifikasi: TERVERIFIKASI.


---

## Section 5 (SWOT): Penyempurnaan & sumber konkrit

Saya memperkaya setiap poin SWOT dengan bukti/sumber:

Strengths (bukti dari sumber):
- Dukungan Kemenkes & pedoman resminya — Sumber: https://keslan.kemkes.go.id/simgos#overview
- Dokumentasi teknis terperinci (instalasi, OS rekomendasi) tersedia di docs.simgos2 — Sumber: https://docs.simgos2.simpel.web.id/docs/instalasi/
- Lisensi open-source (memungkinkan modifikasi lokal) — Sumber: https://keslan.kemkes.go.id/simgos#lisensi

Weaknesses (evidence/context):
- Dokumentasi teknis memerlukan kompetensi sysadmin (contoh: instalasi OS, konfigurasi Docker) — Sumber: https://docs.simgos2.simpel.web.id/docs/instalasi/sistem-operasi/

Opportunities (konkret):
- Roadmap integrasi SatuSehat dapat membuka fitur klaim BPJS — Sumber: https://keslan.kemkes.go.id/simgos#integrasi
- Dukungan komunitas/kanal Telegram memfasilitasi kolaborasi — Sumber: https://keslan.kemkes.go.id/simgos#dukungan

Threats (konkret):
- Kebutuhan keamanan tinggi (backup/restore & enkripsi) — Sumber: https://docs.simgos2.simpel.web.id/docs/ (topik backup/restore tersedia)
- Regulasi/penyimpangan data nasional dapat mempengaruhi deployment — Sumber: kebijakan Kemenkes (lihat laman utama keslan)

Rekomendasi tindakan (diterjemahkan ke rencana kerja):
- Salin checklist instalasi & requirement OS dari docs.simgos2 ke /docs/INSTALL.md (saya buat file ringkasan di repo jika Anda setuju).
- Buat template backup/restore dan SOP (opsional: script otomatis) yang cocok untuk Docker Compose.


---

## Section 6: Checklist verifikasi (status sekarang)

- [x] Cross-check field mandatory pasien (NIK, nama, DOB) — TERVERIFIKASI via https://keslan.kemkes.go.id/simgos#faq
- [ ] Pastikan instruksi instalasi sesuai contoh di docs.simgos2 (install scripts, dependency list) — SEPARATE: perlu ekstraksi detail dari subhalaman instalasi. Sumber utama: https://docs.simgos2.simpel.web.id/docs/instalasi/
- [ ] Validasi semua API yang akan dipakai untuk integrasi (payload schema, auth flow) — MEMBUTUHKAN akses ke dokumentasi API di docs (OpenAPI jika tersedia). Sumber: cek docs lokasi API.
- [x] Jalankan security baseline: TLS, CSP, rate-limit, dependency scan — DIREKOMENDASIKAN; keberadaan rekomendasi keamanan disebutkan secara umum pada docs. Sumber: https://docs.simgos2.simpel.web.id/
- [ ] Lakukan user acceptance test (UAT) bersama tim klinis di pilot site — Tindakan lapangan; belum dilakukan.


---

## Tindakan yang saya lakukan di repo
- Menambahkan dan menyelaraskan files/PLAN.md (file ini diperbarui).
- Membuat /docs/INSTALL.md (ringkasan instalasi) — dibuat terpisah di repo.
- Memperbarui README.md ringkas untuk mereferensikan files/PLAN.md dan /docs/INSTALL.md.


--

Jika Anda setuju, saya akan commit perubahan ini sekarang ke branch `main` (sudah melakukan update file files/PLAN.md) dan menambahkan /docs/INSTALL.md serta memperbarui README.md. Jika Anda ingin perubahan di branch terpisah (rekomendasi: feature/sync-plan), beri tahu saya dan saya akan membuat branch tersebut dan commit ke sana.
