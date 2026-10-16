### A. Tahap 1 – Eksekusi Aplikasi Mandiri Tanpa Docker (Baseline)
![Server aktif](docs/screenshots/sc01-tahap1-tanpa-docker.png)
![Healthz dengan NIM dan hostname](docs/screenshots/sc02-tahap1-tanpa-docker.png)
![Error bind address already in use](docs/screenshots/sc03-tahap1-tanpa-docker.png)
* **Penjelasan Tahap 1**:
Pada tahap ini, aplikasi dijalankan langsung di sistem operasi laptop tanpa docker. Karena hal tersebut, nilai 'container_or_host_id' pada 'healthz' berisi hostname laptop yang sedang digunakan 'LAPTOP-UC8AUANL' dan bukan id container.

Saat aplikasi kedua dijalankan, muncul error 'bind: address already in use'. Karena port 8080 sudah dipakai oleh aplikasi pertama, hal tersebut menunjukkan adanya konflik port karena satu port hanya bisa dipakai untuk satu proses.
