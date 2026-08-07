# README — Modul Rawat Inap SIMGOS (Kelas D Pratama)

Dokumen ringkasan ini berisi: Product Requirement Document (PRD), diagram urutan (UML Sequence) dengan Mermaid, blueprint arsitektur, data dictionary, Data Flow Diagram (DFD) dengan Mermaid, dan pandu[...]

---

## 1. Product Requirement (PRD) — Ringkasan

Tujuan produk: Menyediakan modul rawat inap sederhana untuk fasilitas Kelas D Pratama yang memungkinkan registrasi pasien, admisi rawat inap, manajemen kamar/bed, catatan klinis singkat (SOAP), res[...]

Ruang lingkup (MVP):
- Registrasi pasien & manajemen master pasien (NIK, nama, DOB, kontak)
- Admission rawat inap (assign ward/room/bed, status okupansi)
- Catatan klinis (notes / SOAP) per admission
- Farmasi & resep (prescription -> dispensing -> stok)
- Billing sederhana (invoice per discharge / sementara)
- Laporan rutin: okupansi, lama inap rata-rata, penggunaan obat

Acceptance criteria (ringkas):
- Petugas registrasi dapat membuat pasien dan memulai admission; bed ter-assign dan status berubah.
- Dokter dapat menambah note & membuat resep; apoteker dapat memproses resep dan mengurangi stok.
- Sistem dapat menghasilkan invoice untuk admission yang dipulangkan.
- Backup & restore prosedur terdokumentasi dan diuji di staging.

Non-functional:
- Respons API < 1s untuk lookup pasien hingga 10k record.
- Autentikasi JWT / session secure, TLS mandatory.
- Backup harian dan enkripsi backup.

---

## 2. UML Sequence Diagram (Mermaid)

Diagram urutan inti (Registrasi → Admission → Note/Resep → Discharge/Billing).

```mermaid
sequenceDiagram
    autonumber
    participant Frontend as UI
    participant API as SIMGOS API
    participant Auth as Auth Service
    participant DB as Database
    participant Pharmacy as Pharmacy Module
    participant Billing as Billing Module

    UI->>API: POST /api/auth/login {credentials}
    API->>Auth: validate(credentials)
    Auth-->>API: token
    API-->>UI: 200 OK + token

    UI->>API: POST /api/patients {nik, name, dob, contact}
    API->>DB: INSERT patients
    DB-->>API: patient_id
    API-->>UI: 201 Created {patient_id}

    UI->>API: POST /api/admissions {patient_id, ward, bed, admitted_at}
    API->>DB: INSERT admissions; UPDATE beds.status=occupied
    DB-->>API: admission_id
    API-->>UI: 201 Created {admission_id}

    UI->>API: POST /api/admissions/{id}/notes {author, content}
    API->>DB: INSERT notes
    DB-->>API: note_id
    API-->>UI: 201 Created {note_id}

    UI->>API: POST /api/admissions/{id}/prescriptions {items}
    API->>Pharmacy: createPrescription
    Pharmacy->>DB: INSERT prescriptions & reduce stock
    DB-->>Pharmacy: success
    Pharmacy-->>API: prescription_id
    API-->>UI: 201 Created {prescription_id}

    UI->>API: PATCH /api/admissions/{id}/discharge
    API->>Billing: generateInvoice(admission_id)
    Billing->>DB: INSERT invoices
    DB-->>Billing: invoice_id
    Billing-->>API: invoice_id
    API-->>DB: UPDATE admissions.status=discharged, beds.status=available
    API-->>UI: 200 OK {discharged, invoice_id}
```

---

## 3. Blueprint Arsitektur (Mermaid - component diagram / flowchart)

```mermaid
flowchart LR
    subgraph U [User Layer]
      UI[Web UI / Mobile UI]
    end

    subgraph A [Application Layer]
      API[SIMGOS API]
      Auth[Auth Service]
      Pharmacy[Pharmacy Service]
      Billing[Billing Service]
      Integration[Integrator (SatuSehat/BPJS Adapter)]
    end

    subgraph P [Platform]
      DB[(PostgreSQL / MariaDB)]
      Storage[(File Storage / Uploads Volume)]
      Queue[(Background Queue: Redis/RabbitMQ)]
    end

    UI -->|REST/gRPC| API
    API --> Auth
    API --> DB
    API --> Storage
    API --> Queue
    API --> Pharmacy
    Pharmacy --> DB
    Billing --> DB
    API --> Billing
    Integration ---|FHIR/SatuSehat| API
    subgraph Infra
      Docker[Docker / Docker Compose]
      Backup[Backup Storage]
    end
    API --> Docker
    DB --> Backup
    Storage --> Backup
```

Catatan arsitektur:
- Rekomendasi DB: PostgreSQL atau MariaDB/MySQL (sesuaikan dengan docs resmi).
- Deploy: Docker Compose untuk quickstart; Kubernetes untuk skala lebih besar.
- Integrator modul bertanggung jawab mengimplementasi mapping ke FHIR/SatuSehat.

---

## 4. Data Dictionary (Ringkasan entitas & field utama)

| Entitas | Field (tipe) | Deskripsi | Constraints / Notes |
|---|---|---|---|
| patients | id (uuid/int) | Primary key pasien | unique
|  | nik (string) | Nomor Induk Kependudukan | format NIK (16 digits), optional/required depending on site
|  | name (string) | Nama lengkap pasien | required
|  | gender (string) | L/P | enum
|  | dob (date) | Tanggal lahir | required
|  | address (text) | Alamat | optional
|  | phone (string) | No. telepon | optional

| admissions | id | Primary key admission | 
|  | patient_id (fk) | FK -> patients.id | required
|  | admitted_at (timestamp) | Waktu admisi | required
|  | provisional_diagnosis (string) | Diagnosis awal | optional
|  | ward_id (fk) | FK -> wards.id | required
|  | bed_id (fk) | FK -> beds.id | required
|  | admitted_by (fk) | FK -> users.id | 
|  | status (enum) | admitted / discharged | 
|  | discharged_at (timestamp) | waktu pulang | nullable

| wards | id, name, level | Unit/kelas
| rooms | id, ward_id, name | Ruangan
| beds | id, room_id, bed_label, status | available/occupied/out_of_service

| notes | id, admission_id, author_id, type, content, created_at | Catatan klinis (SOAP)

| drugs | id, code, name, unit, stock_qty | Master obat

| prescriptions | id, admission_id, prescribed_by, created_at | header
| prescription_items | id, prescription_id, drug_id, qty, instruction | detail

| invoices | id, admission_id, items(json), total, paid_amount, status | Billing record

| users | id, username, name, role | roles: admin, registrasi, perawat, dokter, apoteker, keuangan

Catatan: lengkapkan data dictionary di files/PLAN.md untuk kolom/tipe yang lebih detail bila diperlukan.

---

## 5. Data Flow Diagram (DFD) — Mermaid

```mermaid
flowchart TD
    A[Pasien / Petugas] -->|input data| UI[User Interface]
    UI -->|REST| API[SIMGOS API]
    API --> DB[(Database)]
    API --> Storage[(File Storage)]
    API --> Pharmacy[Pharmacy Module]
    Pharmacy --> DB
    API --> Billing[Billing Module]
    Billing --> DB
    API --> Integration[SatuSehat/BPJS Adapter]
    Integration -->|FHIR| NationalSystem[SatuSehat / BPJS]
    Backup[Backup Service] <-- DB
    Backup <-- Storage
```

---

## 6. User Guide (Singkat)

Persingkat langkah untuk peran inti.

- Registrasi (Front-desk):
  1. Login ke UI.
  2. Buka menu "Registrasi" → klik "Tambah Pasien".
  3. Isi NIK (jika tersedia), nama, DOB, kontak.
  4. Simpan; catat patient_id.

- Admission (Front-desk / Registrasi):
  1. Cari pasien (nama / NIK) → pilih pasien.
  2. Klik "Admission Baru" → pilih ward & bed kosong.
  3. Isi diagnosa sementara & tanggal admisi; simpan.
  4. Sistem akan menandai bed sebagai occupied.

- Perawat: menambah catatan harian
  1. Login → buka menu Ward → pilih admission.
  2. Klik "Tambah Note" → pilih type (vital/observation) → isi dan simpan.

- Dokter: menambahkan resep
  1. Buka admission patient → klik "Buat Resep".
  2. Tambah item obat, dosis, instruksi → submit.

- Apoteker: memproses resep
  1. Buka queue resep → verifikasi stock; jika cukup: proses dispensing.
  2. Sistem akan mengurangi stok obat; cetak label jika perlu.

- Keuangan: membuat invoice
  1. Buka admission yang akan dipulangkan → klik "Generate Invoice".
  2. Review item; simpan & cetak invoice; tandai pembayaran.

---

## 7. Catatan & Tindak Lanjut

- Untuk diagram lanjutan atau diagram sequence per-proses (contoh: proses klaim BPJS), beri tahu proses mana yang harus dijabarkan.
- Jika Anda mau, saya bisa membuat file OpenAPI skeleton dan migration SQL berdasarkan data dictionary di atas.

---

Dokumen ini dibuat untuk memudahkan tim pengembang dan operator memahami arsitektur inti dan kebutuhan produk modul rawat inap. Jika Anda ingin saya commit perubahan lain (OpenAPI, migration, con...]
