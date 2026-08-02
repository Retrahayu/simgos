# PLAN: Sistem Informasi Klinik / Rumah Sakit Kelas D Pratama (Rawat Inap)

Dokumen ini adalah single-file specification-driven development (SDD) untuk proyek SIMGOS - modul Klinik/Rumah Sakit Kelas D Pratama dengan layanan rawat inap. Tujuan: menyusun spesifikasi fungsional, teknis, arsitektur, acceptance criteria, rencana pengujian, deployment, dan analisis SWOT sehingga tim dapat mengembangkan fitur secara terarah.

> NOTE: Simpan file ini di `files/PLAN.md` dalam repository untuk menjadi acuan tunggal pengembangan (living document).

## 1. Ringkasan Proyek

- Nama proyek: SIMGOS - Modul Kelas D Pratama (Rawat Inap)
- Scope awal: fasilitas rawat inap skala kecil/kelas D pratama — pendaftaran pasien, manajemen kamar/bed, rekam medis dasar, penjadwalan perawatan, farmasi sederhana, tata usaha/pembayaran, laporan statistik rutin.
- Stakeholders: Dinas Kesehatan, admin klinik, petugas registrasi, perawat, dokter umum, apoteker, bendahara, tim IT lokal.
- Bahasa/stack yang direkomendasikan: PHP (Laravel) / Node.js (Express) atau Python (Django) untuk backend, PostgreSQL untuk DB, React / Vue untuk frontend; containerized (Docker) dan optional Kubernetes untuk scale.

## 2. Tujuan Utama

1. Menyediakan sistem informasi rawat inap yang sederhana, dapat dipasang di fasilitas kelas D dengan infrastruktur terbatas.
2. Memenuhi persyaratan integrasi nasional (SatuSehat/BPJS) bila memungkinkan.
3. Memastikan privasi dan keamanan data pasien sesuai peraturan (mis. Peraturan Kemenkes, UU ITE/PDPL bila berlaku).
4. Meminimalkan beban operasional staf melalui otomatisasi registrasi, pengelolaan kamar, dan laporan.

## 3. Prinsip Design (Spec Driven)

- Setiap fitur ditulis sebagai spesifikasi terukur (user stories + acceptance criteria).
- API-first: endpoint ditentukan sebelum implementasi UI.
- Backward-compatible: perubahan skema harus migratable.
- Minimal viable product (MVP) fokus pada core workflows: registrasi, rawat inap, administrasi, farmasi, laporan.

## 4. Ruang Lingkup & Batasan

- Termasuk: pendaftaran pasien, kunjungan rawat inap (admis, disch), manajemen kamar/bed, catatan medis dasar (SOAP singkat), resep & stok obat sederhana, pencatatan tindakan, billing dasar, laporan bulanan, sistem user & role.
- Tidak termasuk (MVP): modul lanjutan radiologi, PACS, EHR klinis lengkap, scheduling bed integrated with EMS, advanced billing/insurance claims beyond BPJS basic integration (ditunda ke fase 2).

## 5. Persona & Alur Pengguna

- Registrasi (Front-desk): mendaftarkan pasien baru/lama, memverifikasi dokumen, membuat kunjungan rawat inap.
- Perawat: assign bed, catatan vital, administrasi obat, catatan harian.
- Dokter: pemeriksaan, diagnosis, orders, resep.
- Apoteker: manajemen stok, proses resep, dispensed record.
- Bendahara/Keuangan: mencetak invoice, menerima pembayaran, laporan arus kas.

## 6. Functional Requirements (Spec-driven)

Setiap item: user story -> acceptance criteria -> API & data model referensi.

1) Registrasi Pasien
- User story: Sebagai petugas registrasi, saya ingin mendaftarkan pasien baru sehingga pasien dapat dirawat.
- Acceptance:
  - Dapat menyimpan identitas pasien (NIK/ID lokal, nama, DOB, alamat, kontak, BPJS no jika ada).
  - Validasi NIK format; optional cek duplikasi.
- API:
  - POST /api/patients
  - GET /api/patients/:id
- Data model: patients(id, nik, nama, gender, dob, alamat, telepon, bpjs_no, created_at, updated_at)

2) Pendaftaran Rawat Inap (Admission)
- Story: Sebagai petugas, saya ingin melakukan admisi pasien ke unit rawat inap dan assign bed.
- Acceptance:
  - Bisa pilih pasien, tanggal masuk, diagnosa sementara, unit/kelas, nomor bed tersedia.
  - Saat admisi, status bed berubah menjadi occupied.
- API:
  - POST /api/admissions
  - PATCH /api/admissions/:id/discharge
- Models: admissions(id, patient_id, admitted_at, provisional_diagnosis, ward_id, bed_id, admitted_by, status, discharged_at)

3) Manajemen Kamar/Bed
- CRUD ward/room/bed. Track occupancy.
- Models: wards(id, name, level), rooms(id, ward_id, name), beds(id, room_id, bed_label, status)

4) Rekam Medis Singkat (SOAP)
- Dokter/perawat dapat menambah entry SOAP per kunjungan.
- Models: notes(id, admission_id, author_id, type, content, created_at)

5) Farmasi & Resep
- Dokter membuat resep, apoteker memproses dan mengurangi stok.
- Models: drugs(id, code, name, unit, stock_qty), prescriptions(id, admission_id, prescribed_by, items -> prescription_items)

6) Billing & Pembayaran Dasar
- Mengumpulkan biaya: konsultasi, tindakan, obat, inap harian.
- Generate invoice per discharge atau sementara.
- Models: invoices(id, admission_id, items(json), total, paid_amount, status)

7) Laporan & Statistik
- Laporan okupansi kamar, lama inap rata-rata, jumlah pasien per bulan, drug usage.
- API: GET /api/reports/occupancy?from=&to=

8) User Management & Roles
- Roles: admin, registrasi, perawat, dokter, apoteker, keuangan; RBAC sederhana.

## 7. Non-Functional Requirements

- Performance: respons < 1s untuk lookup pasien di DB lokal sampai 10k records.
- Reliability: backup harian DB, retention minimal 30 hari log aktivitas.
- Security: TLS, hashed passwords (bcrypt/argon2), role-based access control, logging audit untuk akses data pasien.
- Privacy: akses data pasien berdasarkan role, persetujuan tertulis rekam medis.
- Maintainability: modular code, automated tests, migration scripts.

## 8. API Spec (ringkas)

- Auth: POST /api/auth/login -> returns JWT
- Patients: GET/POST/PUT /api/patients
- Admissions: POST /api/admissions, GET /api/admissions/:id
- Wards/Rooms/Beds: CRUD endpoints
- Notes: POST /api/admissions/:id/notes
- Prescriptions: POST /api/admissions/:id/prescriptions
- Invoices: POST /api/admissions/:id/invoices, GET /api/invoices/:id

Gunakan OpenAPI/Swagger di fase desain untuk lengkapinya.

## 9. Data Model (ERD - ringkasan tabel utama)

- patients
- admissions
- wards, rooms, beds
- users, roles, permissions
- notes
- drugs, prescription_items
- invoices, invoice_items
- logs/audit_trail

(Detail kolom lihat bagian functional models di atas.)

## 10. Workflows & UI Screens

Prioritaskan alur sederhana dengan layar:
- Login / Dashboard (ringkasan pasien hari ini)
- Registrasi pasien
- Pencarian pasien
- Admission form (pilih bed)
- Ward view (daftar bed & status)
- Catatan harian/notes
- Resep & pharmacy queue
- Invoice & pembayaran
- Laporan

## 11. Acceptance Criteria (Contoh untuk fitur Admission)

- Skenario 1: Petugas registrasi admisi pasien baru
  - Given pasien terdaftar, when petugas membuat admission dan menassign bed kosong, then bed status berubah dan admission tercatat.
- Skenario 2: Discharge
  - When dokter menandai discharge, then admission.status=discharged, discharged_at diisi, dan invoice dapat digenerate.

## 12. Testing Strategy

- Unit tests untuk model & service.
- Integration tests untuk API endpoints.
- End-to-end (E2E) tests untuk critical flows (registrasi -> admisi -> resep -> discharge -> billing) menggunakan Cypress/Playwright.
- Security tests: basic dependency vulnerability scan (Snyk/Dependabot), OWASP ZAP scan pada staging.

## 13. Deployment & Ops

- Containerize with Docker.
- CI: GitHub Actions for lint/test and build images.
- Registry: GitHub Packages / Docker Hub.
- Environment: staging & production. DB backups automated, DB migrations via release pipeline.
- Monitoring: Prometheus/Grafana or simple uptime checks; error logging with Sentry.

## 14. Timeline & Milestones (MVP 8-12 minggu)

- Week 0: Kickoff, requirement finalization, infra setup
- Week 1-3: Core models, patient & registration, auth
- Week 4-5: Admission, bed management, ward UI
- Week 6: Notes & basic EMR entries
- Week 7: Pharmacy & prescription flows
- Week 8: Billing & reports, E2E tests
- Week 9-10: Hardening, security, docs, deployment
- Week 11-12: Pilot at 1 facility, feedback & iteration

## 15. Risks & Mitigasi

- Low IT capability at fasilitas: sediakan training, remote support, simple installer (Docker Compose).
- Data privacy/regulatory compliance: consult legal / Dinas, implement logging & access controls.
- Connectivity issues: enable offline-capable UI patterns or lightweight local deployment.

## 16. Security & Privacy Considerations

- Enforce HTTPS, strong password policy, rate-limit auth endpoints.
- Audit trail for CRUD on patient/admission data.
- Encrypt backups at rest.
- Data retention policy and procedures for data deletion requests.

## 17. Integration & Interoperability

- Design API adaptors for SatuSehat / BPJS future integration; map internal patient identifiers to national registries; support FHIR resources incrementally (Patient, Encounter, Observation, MedicationRequest).

## 18. Documentation & Training

- Provide quickstart deployment guide, admin guide, and end-user manuals (registrasi, perawat, dokter, apoteker) in docs/.
- Short video walkthroughs and training checklist for pilot sites.

## 19. Review & Verification Checklist (Untuk setiap release)

- [ ] Unit & integration tests pass on CI
- [ ] E2E tests for critical flows pass
- [ ] Security scan & dependency checks cleaned
- [ ] DB migration scripts reviewed & tested on staging
- [ ] Backup & restore tested
- [ ] Documentation updated (changelogs + admin guide)
- [ ] Pilot site sign-off

## 20. Comprehensive SWOT Analysis

Strengths:
- Governmental alignment & potential to integrate with national health systems (SatuSehat/BPJS).
- Open-source approach reduces licensing cost and enables local customization.
- Focused MVP for class D facilities reduces complexity and targets achievable impact.

Weaknesses:
- Keterbatasan sumber daya IT di fasilitas kelas D (staff + infrastruktur).
- Fitur klinis kompleks (EMR lengkap) ditunda; dapat dianggap "kurang fitur" dibanding vendor komersial.
- Ketergantungan pada proses adopsi manual dan pelatihan.

Opportunities:
- Dukungan pemerintah / program digitalisasi kesehatan dapat mempercepat adopsi.
- Komunitas pengembang lokal dapat menambah modul (telemedicine, analytics).
- Integrasi dengan BPJS dan SatuSehat membuka akses pembiayaan dan standardisasi data.

Threats:
- Risiko keamanan & kebocoran data yang dapat merusak kepercayaan publik.
- Adopsi vendor komersial yang menawarkan UI lebih modern dan fitur lengkap.
- Perubahan regulasi yang menambah beban kepatuhan.

Rekomendasi berbasis SWOT:
- Fokus pada pengalaman onboarding dan installer sederhana untuk mengurangi hambatan teknis.
- Prioritaskan keamanan dasar dan backup untuk membangun kepercayaan.
- Siapkan roadmap integrasi FHIR bertahap untuk mempermudah konektivitas nasional.

## 21. Review Notes & Verification Plan

- Verifikasi teknis: jalankan checklist di bagian 19 untuk setiap release.
- Review fungsional: tes bersama pengguna kunci (registrasi, perawat, apoteker) di pilot.
- Audit keamanan: lakukan penetration test sebelum roll-out ke banyak fasilitas.

## 22. Next Steps (Immediate)

1. Validasi requirement dengan stakeholder di 1-2 fasilitas kelas D.
2. Putuskan stack teknis (pilih Laravel/Postgres/React atau Django/Postgres/React).
3. Buat OpenAPI skeleton dan basic DB migrations.
4. Implementasikan auth + patients + registration endpoints (MVP slice).
5. Siapkan staging environment & run E2E for core flow.

--

Jika Anda setuju, saya akan commit file ini sebagai `PLAN.md` di root repo (atau sesuai lokasi yang Anda minta). Saya juga dapat menambahkan OpenAPI skeleton dan contoh migration awal di PR terpisah.
