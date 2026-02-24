# Laporan pertemuan ke -2 sistem operasi
**Tanggal:** 24 Februari 2026  
**Disusun Oleh:** Ariel Ardani Aris Putra  
**NIM:** 254107020129  
**Kelas/No:** TI-1G/04


## 1.1 Deteksi Perangkat Keras di Linux

## 1. Praktikum 2.1 — Identifikasi CPU dan Memori
1. tampilan informasi cpu:
![informasi cpu](image/211.png)

2. tampilan ringkasan memori:
![informasi memory](image/212.png)

3. cek informasi hardware dari DMI/BIOS:
![informasi hardware dari bios](image/213.png)

4. Catat: (1) jumlah CPU(s), core/thread, (2) total RAM, (3) total swap. Jelaskan perbedaan RAM vs swap dalam 2–3 kalimat.

jawab : jumlah cpu = 2, core/thread = 2/1, swap = 4GB.
perbedaan ram & swap = ram adalah memori fisik berkecepatan tinggi yang menyimpan data aplikasi yang sedang aktif agar bisa diakses secara instan oleh prosesor. sedangkan swap adalah area di penyimpanan SSD/HDD yang berfungsi sebagai memori cadangan ketika kapasitas RAM sudah habis terpakai. Karena menggunakan media penyimpanan fisik. seacara umum swap lebih lambat dibandingkan ram


## 2.2 — Identifikasi Perangkat PCI/USB dan Driver
1. Lihat daftar perangkat PCI:
![informasi perangkat PCI](image/221.png)

2. Lihat perangkat PCI beserta driver kernel yang digunakan:
![informasi perangkat pci beserta driver](image/222.png)

3. Fokus pada NIC (Ethernet) untuk mencari modul driver:
![informasi untuk memastikan ethernet terdeteksi dan memiliki driver yang sesuai](image/223.png)

4. Lihat perangkat USB:
![informasi untuk melihat perangkan usb](image/224.png)

5. Lihat topologi USB (tree):
![informasi untuk melihat topologi usb tree](image/225.png)

6. Temukan 1 perangkat PCI (misal NIC) dan tuliskan: Vendor:Device ID (angka heksadesimal), nama driver/modul kernel, dan deskripsi singkat fungsinya.

jawab : Nama perangkat = Intel Corporation 82540EM Gigabit Ethernet Controller, vendor:device id = 8086:100e, kernel driver = e1000, kernel moduls = e1000.
Deskripsi singkat& fungsi = pengontrol jaringan berkecepatan tinggi yang memungkinkan sistem untuk melakukan pertukaran data secara stabil. Fungsinya agar Ubuntu dapat memperoleh alamat IP melalui protokol DHCP dan memungkinkan akses jarak jauh melalui SSH.

## 2.3 — Identifikasi Storage dan Filesystem
1. Lihat daftar disk/partisi:
![informasi untuk melihat daftar blok perangkat beserta sistem filenya](image/231.png)

2. Tampilkan UUID dan tipe filesystem:
![informasi untuk mendapatkan identitas unik dari setiap blok penyimpanan](image/232.png)

3. Lihat mount point untuk root filesystem:
![informasi untuk memastikan partisi sistem terpasang dengan benar](image/233.png)


## 2.4 — Melihat Modul Aktif dan Informasinya
1. Cek versi kernel:
![informasi versi kernel](image/241.png)

2. Tampilkan daftar modul aktif:
![informasi daftar modul aktif](image/242.png)

3. Pilih salah satu modul (contoh aman: loop) dan lihat detailnya:
![informasi detail modul dengan modinfo](image/243.png)

4. Muat modul (jika belum aktif), lalu verifikasi:
![load modul dan verifikasi](image/244.png)

5. (Opsional) lihat pesan kernel terbaru:
![informasi log kernel terbaru](image/245.png)


## 2.5 — Konfigurasi Auto-load dan Blacklist:
1. konfigurasi autoload dan blacklist modul
![Melakukan konfigurasi pada modul kernel loop auto load dan blacklist modul.](image/251.png)
![Simulasikan verifikasi (tanpa reboot) dengan memastikan modul sudah aktif](image/252.png)
![ blacklist modul](image/253.png)

## 2.6 — Mengenali block vs character  device:
1. Lihat detail salah satu disk (sesuaikan dengan perangkat Anda, misal sda):
![Melihat detail device node disk](image/131.png)

2. Lihat detail device terminal:
![Melihat detail device node terminal](image/132.png) 

3. Lihat disk dan partisi untuk mengaitkan dengan /dev:
![Mapping disk/partisi](image/133.png)

4. Dari output ls -l, jelaskan perbedaan penanda file untuk block device dan
character device. (Hint: karakter pertama pada permission string)

jawab : ls -l /dev/sda	karakter pertama b jenis filenya Block Device (Penyimpanan),
        ls -l /dev/tty	karakter pertama c jenis filenya Character Device (Terminal)


## 2.7 — Melihat Informasi udev:
1. Cek atribut udev untuk disk:
![Melihat atribut udev untuk disk](image/271.png)

2. (Opsional) monitor event udev (jalankan, lalu colok/lepas USB pada mesin
fisik):
![Monitor event udev](image/272.png)


## 2.8 — Membuat Workspace Praktikum:
1. Buat direktori praktikum dan masuk ke dalamnya:
![Membuat workspace praktikum](image/281.png)

2. Buat beberapa file contoh:
![Membuat file contoh](image/282.png)

3. Isi file log contoh (simulasi):
![Mengisi file log contoh](image/283.png)

4. Baca file dengan less:
![Membaca file dengan less](image/284.png)


## 2.9 — Pencarian Pola dengan grep
1. cari barisan error,warn,info dengan grep:
![tampilan dengan grep](image/151.png)
![tampilan dengan grep](image/152.png)
![tampilan dengan grep](image/153.png)
![tampilan dengan grep](image/154.png)

2. Gunakan grep untuk menampilkan hanya baris yang mengandung INFO atau
WARN dari data.log. (Hint: gunakan grep -E dengan pola alternatif)

jawab: untuk menggunakan alternatif or kita menggunakan opsi -E dan | berfungsi sebagai atau.  berikut contohnya:
![contoh](image/155.png)


## 2.10 — Substitusi dengan sed (Aman di File Latihan)
1. Siapkan file konfigurasi latihan:
![Siapkan file konfigurasi latihan](image/1521.png)
2. Ganti dev menjadi prod (tanpa mengubah file asli):
![Ganti dev menjadi prod (tanpa mengubah file asli):](image/1522.png)
3. Terapkan perubahan langsung ke file (-i):
![konfigurasi dan edit](image/1523.png)
4.  Ganti semua kemunculan kata (g untuk global), contoh ubah myserver menjadi node::
![ Ganti semua kemunculan kata (g untuk global), contoh ubah myserver menjadi node](image/1524.png)

5. Gunakan sed -i dengan hati-hati jika mengedit file sistem. Untuk administrasi
nyata, praktik aman adalah membuat backup: sed -i.bak ’s/old/new/g’
file.conf

jawab: hasil backup
![hasil backup](image/1525.png)


## 2.11  — Ekstraksi Kolom dengan awk
1. Lihat output df -h:
![Lihat output df -h](image/1531.png)
2. Ambil kolom filesystem dan persentase pemakaian:
![Ambil kolom filesystem dan persentase pemakaian](image/1532.png)
3. Filter hanya yang pemakaian disk di atas 80%::
![Filter hanya yang pemakaian disk di atas 80%:](image/1533.png)


## 2.12 — Melihat Proses dengan ps
1. Tampilkan semua proses (format BSD):
![Tampilkan semua proses (format BSD)](image/1611.png)
2. Cari proses tertentu (misal sshd):
![Cari proses tertentu (misal sshd):](image/1612.png)


## 2.13 — Monitoring Real-time dengan top
1. Jalankan top:
![menjalankan top](image/1621.png)


## 2.14 — Menghentikan Proses dengan kill
1. Jalankan proses dummy di background:
![Jalankan proses dummy di background](image/1631.png)
2. Cari PID proses sleep:
![Cari PID proses sleep](image/1632.png)
3. Hentikan dengan SIGTERM:
![Hentikan dengan SIGTERM](image/1633.png)
4.Verifikasi proses berhenti:
![Verifikasi proses berhenti](image/1634.png)
5. Jika proses sulit untuk dihentikan dan Anda membutukan untuk menghentikan proses tersebut, gunakan SIGKILL::
![Jika proses sulit untuk dihentikan dan Anda membutukan untuk menghentikan proses tersebut, gunakan SIGKILL:](image/1635.png)


## 2.15 — Cek Disk, Load, dan Service
1. Cek penggunaan disk:
![cek kapasitas disk](image/1711.png)

2. Cari direktori yang besar (contoh pada /var):
![Cek ukuran direktori](image/1712.png)

3. Cek load dan uptime:
![cek load average](image/1713.png)

4. Cek service yang gagal:
![service yang gagal](image/1714.png)

5. Ambil log error terbaru:
![log error terbaru](image/1715.png)


## 2.16 — Monitoring Port dan Koneksi (Network Basics)
1. Lihat interface dan IP:
![Lihat interface dan IP](image/1721.png)
3. Lihat routing table:
![Lihat routing table](image/1722.png)
3. Lihat port yang sedang listening:
![Lihat port yang sedang listening:](image/1723.png)

4. Pilih satu port yang listening dari output ss -tulpn(misal port 22), lalu
tuliskan service/proses yang membukanya. Jelaskan kegunaan port tersebut
secara singkat.

jawab: Port 53 dibuka oleh proses systemd-resolve, Port ini digunakan untuk layanan DNS (Domain Name System) yang memungkinkan penerjemahan nama domain (seperti google.com) menjadi alamat IP agar server dapat terhubung ke internet.


## 1.9 Latihan
1. 2.A Jalankan lspci -nnk. Pilih 1 perangkat PCI dan tuliskan: nama perangkat, ID vendor:device, dan kernel driver in use.
![hasil](image/LatA.png)
jawab: awab: Nama perangkat: Intel Corporation 82540EM Gigabit Ethernet Controller, ID vendor:device: 8086:100e, Kernel driver in use: e1000.


2. 2.B Tentukan device root filesystem dengan findmnt /. Lalu cocokkan dengan lsblk -f dan tuliskan tipe filesystem serta UUID-nya.
![hasil](image/LatB.png)
jawab: Device Root Filesystem: /dev/sda2
Tipe Filesystem: ext4
UUID: 8d527946-ed3b-40d2-914f-0c068e649ac6


3. 2.C Buat file server.log berisi minimal 10 baris dengan variasi kata: INFO, WARN, ERROR. Gunakan grep untuk menampilkan hanya baris ERROR.
- ![Hasil](image/LatC.png)


4. 2.D Gunakan sed untuk mengganti semua kata server menjadi node pada file latihan. Tunjukkan sebelum dan sesudah.
- ![sebelum](image/LatD.png)


5. 2.E Gunakan df -h lalu awk untuk menampilkan filesystem yang penggunaan disk di atas 70%.
![informasi & filter disk](image/LatE.png)


6. 2.F Jalankan sleep 600 &. Temukan PID-nya dengan ps. Hentikan dengan SIGTERM. Jelaskan beda SIGTERM vs SIGKILL.
![hasil](image/LatF.png)
perbedaan sigterm dan sigkill  
 SIGTERM : proses bisa cleanup dulu  
 SIGKILL : langsung paksa mati 


7. 2.G Gunakan systemctl –failed. Jika tidak ada yang gagal, pilih satu service aktif (misal ssh) dan tampilkan status serta 30 baris log terakhirnya.
![hasil](image/LatG.png)