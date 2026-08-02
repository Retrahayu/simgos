# BACKUP & RESTORE — SIMGOS v2 (Ringkasan Praktis)

Dokumen ini merangkum praktik backup & restore yang direkomendasikan untuk instalasi SIMGOS v2 (containerized atau manual). Rujuk dokumentasi resmi untuk detail dan contoh script: https://docs.simgos2.simpel.web.id/ dan halaman produk https://keslan.kemkes.go.id/simgos

Penting: selalu uji prosedur restore di lingkungan staging sebelum menerapkannya di produksi.

1) Pendekatan umum
- Backup database (dump) + backup file aplikasi/volume.
- Simpan backup di lokasi terpisah dan terenkripsi.
- Automasi backup dengan cron atau task scheduler.

2) Backup untuk deployment Docker/Docker Compose
- Database biasanya berjalan sebagai service (MySQL/MariaDB atau PostgreSQL) di container.
- Langkah contoh (MySQL/MariaDB):
  - Dump: docker exec CONTAINER_DB_NAME sh -c 'exec mysqldump --databases DB_NAME -uUSER -p"PASSWORD"' > /backup/db_backup.sql
  - Alternatif (menggunakan mysqldump di host): docker run --rm --network container:CONTAINER_DB_NAME -v $(pwd):/backup mysql:8 sh -c 'exec mysqldump --databases DB_NAME -uUSER -p"PASSWORD"' > ./db_backup.sql
- Backup volumes (file uploads, attachments):
  - docker run --rm -v simgos_uploads:/data -v $(pwd):/backup alpine sh -c 'cd /data && tar czf /backup/uploads-$(date +%F).tgz .'

3) Restore (Docker)
- Matikan aplikasi/stop containers: docker compose down
- Restore volume files: tar xzf uploads-YYYY-MM-DD.tgz -C /path/to/volume-mount
- Restore DB:
  - docker exec -i CONTAINER_DB_NAME sh -c 'mysql -uUSER -p"PASSWORD"' < db_backup.sql
- Jalankan kembali: docker compose up -d

4) Backup untuk instalasi manual (non-container)
- Database dump local: mysqldump -uUSER -p DB_NAME > db_backup.sql
- Backup file aplikasi: tar czf app-backup-$(date +%F).tgz /var/www/simgos

5) Enkripsi dan rotasi backup
- Enkripsi: gpg --symmetric --cipher-algo AES256 db_backup.sql
- Rotasi: simpan 7 hari harian + 4 minggu mingguan + 12 bulan bulanan (sesuaikan kebijakan organisasi)

6) Verifikasi backup
- Lakukan test restore secara berkala (mis. mingguan di staging) untuk memastikan backup dapat dipulihkan.

7) Referensi
- Dokumentasi instalasi & topik backup: https://docs.simgos2.simpel.web.id/
- Halaman produk SIMGOS (overview/faq): https://keslan.kemkes.go.id/simgos

Catatan: contoh di atas generik — sesuaikan nama container, kredensial, dan path backup sesuai lingkungan Anda. Jika Anda mau, saya dapat mengekstrak perintah persis dari subhalaman backup di docs.simgos2 dan menaruh script siap pakai ke /scripts/backup.sh dan /scripts/restore.sh.