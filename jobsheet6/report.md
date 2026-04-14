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
![a](image/16.png) 
![a](image/17.png) 
5. Simpan bukti pengujian ke file toolkit-bash-report.txt.
Minimal luaran:
• isi blok konfigurasi yang ditambahkan ke .bashrc,
• output echo $PATH,
• output type untuk alias, fungsi, dan script,
• file laporan toolkit-bash-report.txt.
buat dulu file toolkit-bash-report.txt dengan perintah nano/touch   
ketik perintah dibawah untuk memasukan output dari perintah tsb ke dalam file toolkit-bash-report.txt  
ll >> toolkit-bash-report.txt   
ceklog >> toolkit-bash-report.txt   
quiick_backup >> toolkit-bash-report.txt   
ringkas-sistem >> toolkit-bash-report.txt   
lalu cek hasil output dari toolkit-bash-report.txt dengan perintah cat    
![a](image/18.png) 

## Tugas Praktikum 2 — Audit File Konfigurasi dan
Logging Aman
Konteks riil: saat troubleshooting, administrator sering perlu menginventarisasi
file konfigurasi dan memisahkan output normal dari pesan error.
Instruksi tugas:
1. Buat file laporan bernama audit-konfigurasi-$(date +%F).txt.
![a](image/21.png) 

2. Cari file *.conf di dalam /etc dan simpan hasilnya ke file laporan.
![a](image/22.png) 

3. Catat jumlah total file konfigurasi yang ditemukan.
![a](image/23.png) 
4. Jika ada pesan error, simpan ke file terpisah, misalnya audit-error.log.
![a](image/24.png) 
5. Tampilkan isi laporan ke terminal dan sekaligus simpan menggunakan tee.
![a](image/25.png) 
6. Tambahkan ringkasan singkat 3–5 baris yang menjelaskan mengapa pemisahan
stdout dan stderr penting dalam audit sistem.
![a](image/26.png) 
Syarat konsep yang harus muncul:
• redirection >, 2>, atau &>,  
• pipeline,  
• tee,  
• penggunaan variabel atau command substitution.  
Minimal luaran:  
• file laporan audit,  
• file log error,  
• perintah yang digunakan,  
• analisis singkat hasil audit.  
Hasil Akhir
![a](image/27.png) 
## Tugas Praktikum 3 — Mini Health Check Harian
Server
Konteks riil: administrator perlu membuat pemeriksaan cepat (health check) untuk
mengetahui kondisi dasar server sebelum dan sesudah maintenance.
Instruksi tugas:
1. Buat script Bash bernama daily-healthcheck pada direktori bin pribadi.
buat file dengan perintah   
nano ~/praktikum-os/week07-bash/bin/daily-healthcheck
2. Script minimal harus menampilkan:
• tanggal dan waktu,
• hostname,
• user aktif,
• shell aktif,
• uptime,
• penggunaan memori,
• penggunaan filesystem root,
• 10 baris terakhir history command yang relevan dengan pengecekan.
![a](image/32.png) 
3. Simpan hasil ke file log harian, misalnya healthcheck-$(date +%F).log.
![a](image/33.png) 

4. Tampilkan hasil ke terminal dan ke file secara bersamaan. 
ubah izin execute file agar file bisa di eksekusi
![a](image/34.png) 
eksekusikan file file  
![a](image/341.png)    
5. Jika Anda menggunakan pipeline dengan tee, cek juga status exit command utama
![a](image/35.png)    
Syarat konsep yang harus muncul:
• environment variable,
• PATH,
• alias atau fungsi pendukung,
• history,
• tee,
• penanganan error dasar.
Minimal luaran:
• file script yang executable,
• contoh isi file log hasil eksekusi,
• penjelasan singkat fungsi tiap bagian script.
Hasil akhir cek file:
![a](image/36.png)    

## Tugas Praktikum 4 — Penanganan File dengan Nama
Kompleks dan Arsip Aman
Konteks riil: file hasil backup, ekspor, atau laporan sering memiliki nama yang
mengandung spasi atau karakter khusus. Administrator harus tetap dapat memproses
file-file tersebut tanpa salah target.
Instruksi tugas:
1. Buat minimal 4 file contoh dengan nama yang bervariasi, termasuk:
• nama file yang mengandung spasi,
• nama file yang mengandung tanda kurung siku atau karakter khusus,
• file dengan pola nama serupa untuk diuji dengan wildcard.  
![a](image/41.png)    
2. Tunjukkan perbedaan hasil jika file diakses tanpa quoting dan dengan quoting yang benar.
![a](image/42.png)    
3. Lakukan preview wildcard dengan echo sebelum dipakai untuk operasi nyata.  
![a](image/43.png)     
4. Salin file-file tersebut ke direktori backup dengan nama yang aman.  
![a](image/44.png)    
5. Buat arsip tar.gz dari hasil backup.   
![a](image/45.png)    
6. Simpan riwayat perintah yang Anda gunakan ke file riwayat-arsip.txt.   
![a](image/46.png)    
Syarat konsep yang harus muncul:
• single quote, double quote, dan escaping,
• wildcard,
• variabel path,
• history,
• operasi file lanjutan yang aman.
Minimal luaran:
• daftar file awal,
• daftar file hasil backup,
• file arsip tar.gz,
• file riwayat-arsip.txt,
• refleksi singkat tentang pentingnya quoting di Bash.  
![a](image/47.png)      