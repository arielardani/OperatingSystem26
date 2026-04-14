# Laporan pertemuan ke -7 sistem operasi
**Tanggal:** 14 April 2026  
**Disusun Oleh:** Ariel Ardani Aris Putra  
**NIM:** 2541070200129  
**Kelas/No:** TI-1G/04

## Tugas Praktikum 1 — Toolkit Bash Administrator Pribadi
Konteks riil: seorang administrator sering mengulang perintah yang sama setiap
hari. Agar pekerjaan lebih efisien dan konsisten, ia perlu memiliki toolkit Bash
1 Bash Shell dan Shell Basic 19
1.8 Tugas Praktikum
pribadi yang otomatis aktif setiap login.
Instruksi tugas:
1. Tambahkan konfigurasi pada .bashrc untuk:
• menambahkan direktori bin pribadi ke PATH,
• membuat minimal 2 alias yang membantu kerja harian,
• membuat minimal 1 fungsi shell yang berguna untuk administrasi.
dikarenakan akan mengotak atik file system backup dulu file tersebut agar bisa menjadi checkpoint.      
![a](image/11.png) 
lalu lakukan konfigurasi
![a](image/12.png) 
![a](image/16.png) 
![a](image/17.png) 


2. Pastikan konfigurasi tersebut aktif kembali saat membuka shell login.  
Gunakan perintah source ~/.bashrc
![a](image/13.png) 
lalu buar folder dengan perintah  
mkdir -p ~/praktikum-os/week07-bash/bin
3. Buat satu script sederhana di direktori bin pribadi, misalnya script untuk
menampilkan ringkasan sistem.  
buat file ringkas-sistem dengan perintah   
nano ~/praktikum-os/week07-bash/bin/ringkas-sistem   
lalu isi file dengan
![a](image/14.png) 
setelah itu berikan izin eksekusi untuk file tersebut   
![a](image/15.png) 
4. Uji dari direktori yang berbeda untuk memastikan script dapat dipanggil tanpa menuliskan path lengkap.
5. Simpan bukti pengujian ke file toolkit-bash-report.txt.
Minimal luaran:
• isi blok konfigurasi yang ditambahkan ke .bashrc,
• output echo $PATH,
• output type untuk alias, fungsi, dan script,
• file laporan toolkit-bash-report.txt.