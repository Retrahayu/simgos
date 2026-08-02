# SWOT Analysis — SIMGOS v2

Dokumen ini memparafrasekan dan merangkum posisi strategis SIMGOS v2 berdasarkan dokumen internal repo serta verifikasi sumber publik yang tersedia per 2026-08-02. Fokus analisis adalah kelayakan SIMGOS untuk fasilitas kesehatan Indonesia, terutama Klinik dan RS Kelas D Pratama.

## Ringkasan eksekutif

SIMGOS v2 memiliki kekuatan utama pada legitimasi kelembagaan, cakupan modul layanan kesehatan yang luas, dokumentasi teknis/pengguna yang terstruktur, dan dukungan integrasi nasional. Halaman Kementerian Kesehatan menyatakan SIMGOS sebagai sistem informasi milik Kemenkes RI untuk pencatatan dan pengelolaan data pelayanan elektronik di fasilitas kesehatan. Dokumentasi resmi juga menyebut SIMGOS v2 sebagai Sistem Informasi Manajemen Generik Open Source, menyediakan panduan instalasi, konfigurasi, pembaruan, integrasi, pengguna, dan video tutorial.

Risiko utama terletak pada kompleksitas implementasi: instalasi dan integrasi membutuhkan kemampuan sysadmin, akses basis data, konfigurasi file server, kredensial dari pihak nasional/eksternal, dan penguatan keamanan yang disiplin. Karena SIMGOS menangani data kesehatan dan integrasi eksternal, kegagalan tata kelola keamanan, backup, credential management, atau perubahan regulasi dapat berdampak besar pada operasional fasyankes.

## Sumber yang diverifikasi

| Area verifikasi | Status | Bukti ringkas |
|---|---:|---|
| Definisi dan kepemilikan | Terverifikasi | Halaman Keslan Kemenkes menyebut SIMGOS sebagai sistem informasi milik Kementerian Kesehatan RI untuk pencatatan dan pengelolaan data pelayanan elektronik. |
| Open source dan cakupan dokumentasi | Terverifikasi | Beranda docs menyebut SIMGOS v2 sebagai Sistem Informasi Manajemen Generik Open Source, dengan bagian instalasi, konfigurasi, pembaruan, integrasi, panduan pengguna, dan video tutorial. |
| Kebutuhan hardware | Terverifikasi | Halaman Keslan mencantumkan minimum server untuk praktik mandiri, Klinik/RS Kelas D Pratama, RS Kelas A-D, dan minimum client. |
| Integrasi nasional/eksternal | Terverifikasi | Docs mencantumkan integrasi VClaim 2.0, E-Klaim, RSOnline, Satu Sehat, dan integrasi BPJS lainnya. |
| Kompleksitas teknis integrasi | Terverifikasi | Panduan Satu Sehat dan VClaim meminta konfigurasi file, database, credentials, cache, scheduler, dan testing. |
| Keamanan operasional | Terverifikasi | Panduan keamanan memuat contoh header keamanan, konfigurasi SSL, restart service, dan pembukaan firewall HTTPS. |

## Strengths — kekuatan

1. **Legitimasi kuat untuk konteks Indonesia.** SIMGOS bukan sekadar proyek komunitas; halaman resmi Keslan menyebutnya sebagai sistem milik Kementerian Kesehatan RI. Ini memperkuat penerimaan di fasyankes yang membutuhkan rujukan kebijakan nasional.
2. **Open source dan generik.** Karakter generik open source membuat SIMGOS lebih mudah disesuaikan dengan variasi proses bisnis fasyankes, tanpa mengikat seluruh pengembangan pada vendor tunggal.
3. **Cakupan modul luas.** Struktur dokumentasi menunjukkan menu dan panduan untuk pendaftaran, layanan, rekam medis, farmasi, pembayaran, laporan, tempat tidur, dan integrasi. Cakupan ini cocok untuk roadmap bertahap dari MVP rawat inap menuju operasional yang lebih lengkap.
4. **Dukungan integrasi strategis.** Dokumentasi integrasi memuat Satu Sehat, VClaim, Aplicares, ICare, Antrian Online, E-Klaim, RS Online, SIRS Online, dan dashboard. Ini memperbesar nilai SIMGOS dibanding aplikasi lokal yang berdiri sendiri.
5. **Dokumentasi operasional tersedia.** Docs publik menyediakan area instalasi, konfigurasi, pembaruan, panduan pengguna, dan video tutorial. Untuk organisasi dengan tim TI terbatas, dokumentasi ini mempercepat onboarding dibanding memulai dari nol.
6. **Kebutuhan minimum hardware eksplisit.** Halaman resmi memisahkan kebutuhan server dan client untuk praktik mandiri, klinik/RS D Pratama, dan RS kelas A-D. Ini membantu perencanaan anggaran infrastruktur sejak awal.

## Weaknesses — kelemahan

1. **Implementasi membutuhkan kompetensi teknis.** Integrasi Satu Sehat dan VClaim tidak cukup dilakukan dari UI; panduannya mencakup pengeditan file `local.php`, akses database, perubahan status integrasi, penghapusan cache, pengaturan scheduler, dan testing. Ini menjadi hambatan bagi fasyankes tanpa SDM TI internal.
2. **Ketergantungan pada konfigurasi manual.** Banyak langkah integrasi mengandalkan perubahan langsung pada konfigurasi dan database. Risiko salah input, lupa membersihkan cache, atau memakai credential environment yang keliru cukup tinggi bila tidak ada SOP dan change control.
3. **Beban keamanan berada di operator.** Dokumentasi menyediakan arahan keamanan seperti header HTTP, SSL, dan firewall HTTPS, tetapi keberhasilan di produksi tetap bergantung pada operator dalam menerapkan TLS, rotasi credential, backup terenkripsi, audit akses, dan hardening server.
4. **Dokumen publik belum menggantikan validasi lapangan.** Sumber publik membuktikan cakupan, kebutuhan dasar, dan alur konfigurasi, tetapi tidak membuktikan performa pada beban produksi tertentu, kualitas data lokal, kesiapan SDM, atau kesesuaian penuh dengan workflow tiap rumah sakit.
5. **Repositori ini masih berupa dokumentasi/rencana, bukan implementasi aplikasi.** Isi repo saat ini berfokus pada README, PLAN, INSTALL, dan BACKUP-RESTORE, sehingga verifikasi teknis di repo hanya bisa menilai konsistensi dokumen, bukan hasil build, test aplikasi, atau kualitas kode runtime.

## Opportunities — peluang

1. **Digitalisasi rekam medis nasional.** Regulasi RME dan interoperabilitas Satu Sehat menciptakan kebutuhan besar untuk sistem yang siap integrasi nasional. SIMGOS dapat diposisikan sebagai opsi implementasi hemat biaya bagi fasyankes yang belum memiliki SIMRS/SIM klinik matang.
2. **Adopsi bertahap untuk Klinik dan RS D Pratama.** Karena kebutuhan minimum untuk Klinik/RS D Pratama dicantumkan secara eksplisit, fasilitas kecil dapat menyusun rencana implementasi bertahap: pendaftaran, rawat inap, farmasi, billing, laporan, lalu integrasi.
3. **Standardisasi SOP operasional.** Repo ini dapat berkembang menjadi paket pendamping implementasi: checklist instalasi, SOP backup/restore, matriks risiko, template UAT, dan runbook incident response.
4. **Ekosistem integrasi.** Integrasi Satu Sehat, BPJS, dan pelaporan Kemenkes membuka peluang otomatisasi data, pengurangan input ganda, monitoring mutu, dan laporan manajemen yang lebih cepat.
5. **Pelatihan dan komunitas.** Karena docs menyediakan panduan pengguna dan video tutorial, materi tersebut dapat dijadikan dasar kurikulum pelatihan operator, admin, dan tenaga klinis.

## Threats — ancaman

1. **Perubahan regulasi dan endpoint eksternal.** Integrasi dengan Satu Sehat, BPJS, dan aplikasi nasional lain bergantung pada kebijakan, endpoint, credential, format data, dan aturan operasional yang bisa berubah. Tanpa monitoring perubahan, integrasi bisa rusak.
2. **Risiko privasi dan keamanan data kesehatan.** SIMGOS memproses data pelayanan kesehatan. Salah konfigurasi SSL, header keamanan, firewall, backup, atau akses database dapat menimbulkan insiden privasi dan gangguan layanan.
3. **Keterbatasan SDM TI fasyankes.** Panduan integrasi memperlihatkan kebutuhan kemampuan teknis yang tidak selalu tersedia di klinik kecil. Ketergantungan pada pihak ketiga tanpa dokumentasi internal dapat menambah risiko keberlanjutan.
4. **Kualitas data master dan mapping.** Integrasi VClaim dan Satu Sehat membutuhkan kode fasyankes, organization id, mapping penjamin, mapping DPJP, mapping ruangan, serta referensi lain. Data master yang tidak rapi dapat menyebabkan klaim, pelaporan, atau sinkronisasi gagal.
5. **Kegagalan backup/restore.** Backup yang tidak diuji dapat memberi rasa aman palsu. Ancaman ransomware, kerusakan disk, salah migrasi, atau kesalahan operator harus diatasi dengan backup terenkripsi, offsite copy, dan uji restore berkala.

## Rekomendasi prioritas

1. **Buat runbook implementasi produksi.** Gabungkan checklist server, network, TLS, database, file storage, user roles, backup, dan integrasi eksternal dalam satu dokumen operasional.
2. **Tambahkan matriks kontrol keamanan.** Petakan kontrol minimal: HTTPS, HSTS, CSP, backup terenkripsi, least privilege, rotasi credential, audit log, segmentasi jaringan, dan uji restore.
3. **Tambahkan template UAT per peran.** Buat skenario pendaftaran, admisi rawat inap, CPPT/SOAP, order resep, dispensing, billing, discharge, laporan, dan integrasi.
4. **Pisahkan klaim terverifikasi dan asumsi.** Untuk setiap PRD atau blueprint, beri label `TERVERIFIKASI`, `ASUMSI`, atau `PERLU VALIDASI LAPANGAN` agar reviewer tahu mana yang berasal dari sumber resmi dan mana yang merupakan rancangan lokal.
5. **Automasi validasi dokumen.** Tambahkan pemeriksaan markdown/link agar dokumentasi tetap konsisten saat bertambah.

## Kesimpulan

SIMGOS v2 layak dipertimbangkan sebagai fondasi sistem informasi kesehatan open source untuk fasyankes Indonesia, khususnya bila organisasi membutuhkan keselarasan dengan ekosistem Kemenkes/BPJS. Namun, keberhasilan implementasi bukan hanya soal instalasi aplikasi. Faktor penentu adalah kesiapan SDM TI, governance data, hardening keamanan, disiplin backup/restore, kualitas data master, dan kemampuan mengikuti perubahan regulasi maupun endpoint integrasi.

## Referensi publik

- Halaman produk SIMGOS Kemenkes: https://keslan.kemkes.go.id/simgos
- Beranda dokumentasi SIMGOS v2: https://docs.simgos2.simpel.web.id/
- Tentang SIMGOS v2: https://docs.simgos2.simpel.web.id/docs/pengantar/tentang/
- Kebutuhan hardware dan software: https://docs.simgos2.simpel.web.id/docs/instalasi/kebutuhan-hardware-dan-software/
- Konfigurasi keamanan: https://docs.simgos2.simpel.web.id/docs/konfigurasi/keamanan/
- Integrasi Satu Sehat: https://docs.simgos2.simpel.web.id/docs/integrasi/kemenkes/satu-sehat/
- Integrasi BPJS VClaim: https://docs.simgos2.simpel.web.id/docs/integrasi/bpjs/vclaim/
