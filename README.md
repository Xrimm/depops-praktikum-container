### A. Tahap 1 – Eksekusi Aplikasi Mandiri Tanpa Docker (Baseline)
![Server aktif](docs/screenshots/sc01-tahap1-tanpa-docker.png)
![Healthz dengan NIM dan hostname](docs/screenshots/sc02-tahap1-tanpa-docker.png)
![Error bind address already in use](docs/screenshots/sc03-tahap1-tanpa-docker.png)
* **Penjelasan Tahap 1**:
Pada tahap pertama ini, aplikasi dijalankan langsung di sistem operasi laptop tanpa docker. Karena hal tersebut, nilai 'container_or_host_id' pada 'healthz' berisi hostname laptop yang sedang digunakan 'LAPTOP-UC8AUANL' dan bukan id container.

Saat aplikasi kedua dijalankan, muncul error 'bind: address already in use'. Karena port 8080 sudah dipakai oleh aplikasi pertama, hal tersebut menunjukkan adanya konflik port karena satu port hanya bisa dipakai untuk satu proses.

### B. Tahap 2 – Kontainer Tunggal dengan Single-Stage Dockerfile
![Bukti Screenshot Tahap 2](docs/screenshots/sc01-tahap2-single-stage.png)
* **Penjelasan Tahap 2**:
Pada tahap kedua ini, docker digunakan agar dua aplikasi dapat berjalan bersamaan tanpa adanya konflik port karena setiap container memiliki jaringan terisolasinya sendiri-sendiri. Walaupun keduanya tetap memakai port 8080, konflik dapat dihindari karena container pertama dipetakan ke host lewat '-p 8081:8080' tetapi container kedua lewat '-p 8082:8080'. Sehingga port yang terbuka di laptoop berbeda.

Ukuran pada image single-stage juga membengkak menjadi 1.32GB karena base image-nya yaitu 'golang:1.22-bookworm' berisi OS lengkap beserta compiler go dan toolchain, semua itu ikut tersimpan ke dalam image akhir bersama source code-nya. Walaupun yang benar-benar dibutuhkan untuk menjalankan aplikasi hanya file biner hasil kompilasi.

### C. Tahap 3 (Lab 1 RPS) – Optimasi Dockerfile Multi-Stage Build (< 100 MB)
![Bukti Screenshot Tahap 3](docs/screenshots/sc01-tahap3-multistage.png)
* **Penjelasan Analisis Tahap 3**:
Pada tahap ketiga ini, ukuran image turun dari 1.32GB menjadi 26,5MB melalui multi-stage, sehingga penghematan yang terjadi mencapai sekitar 98% dan memenuhi syarat di bawsah 100MB.

Instruksi 'COPY --from=builder' berfungsi untuk menyalin file biner hasil kompilasi-nya saja dari stage 'builder' ke stage 'production'. Sehingga compiler go, source code, dan cache build-nya tetap tertinggal di stage pertama dan tidak masuk ke image akhir.

Instruksi 'USER appuser' membuat aplikasi berjalan sebagai user biasa dengan id 'uid=10001' dan bukan root. Alasan keamanannya yaitu apabila aplikasi berjasil dibobol, penyerang hanya akan mendapatkan hak akses terbatas di dalam container saja, dan bukan hak penuh sebagai root.
