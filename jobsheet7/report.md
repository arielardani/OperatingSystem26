# Laporan pertemuan ke -9 sistem operasi
**Tanggal:** 14 April 2026  
**Disusun Oleh:** Ariel Ardani Aris Putra  
**NIM:** 2541070200129  
**Kelas/No:** TI-1G/04

## Praktikum 7.1 Script Pertama: Laporan Sistem
1. Buat workspace praktikum:  
```
mkdir -p ~/praktikum-os/week09/{scripts,logs,data}
cd ~/praktikum-os/week09/scripts 
```

2. Buat script dengan nano:  
``` 
nano laporan-sistem.sh 
```

3. Ketik isi berikut, simpan ( Ctrl+O Enter ), lalu keluar ( Ctrl+X ):   
![a](image/3.png)
4. Beri izin dan jalankan:   
![a](image/4.png)  
![a](image/5.png)  
### Latihan 9.1
Modifikasi laporan-sistem.sh agar menyimpan output ke file laporan-YYYY-MM-DD.txt sekaligus menampilkannya di terminal. Petunjuk: gunakan tee yang sudah dipelajari di bab sebelumnya.
- buka kembali file laporan-sistem.sh lalu tambahkan   
![a](image/6.png)  
- Jalankan lalu cek hasil dengan perintah cat   
![a](image/7.png)  

## Praktikum 7.2 Script Info Sistem dengan Argumen
1. Buat script:
``` 
nano ~/praktikum-os/week09/scripts/info-sistem.sh 
```
2. Ketik isi berikut: 
![a](image/8.png)  
3. Simpan, beri izin, uji dengan berbagai kombinasi argumen:
![a](image/9.png) 
### Latihan 9.2
Buat script kalkulator.sh yang menerima tiga argumen: "angka1 operator angka2 dengan operator +, -, *, atau /. Contoh:
./kalkulator.sh 20 + 5 menghasilkan 25. Gunakan case untuk memilih operasi, dan validasi jika argumen tidak lengkap

- buat file dengan perintah
```
nano kalkulator.sh
```
- isikan dengan:   
![a](image/10.png)  
- beri izin execute dan jalankan   
![a](image/11.png)  

## Praktikum 7.3 Script Grading dan Menu Interaktif

1. Buat script grading (menggunakan if dan for):
```
nano ~/praktikum-os/ week09/scripts /grading-batch.sh
``` 
2. Ketik isi berikut:   
![a](image/12.png)  
3. Simpan, beri izin, dan jalankan:   
![a](image/13.png)  
4. Buat script menu interaktif (while + case):
```
nano ~/praktikum-os/week09/scripts/ menu-sistem.sh
```
5. Ketik isi berikut:
![a](image/14.png)  
6. Beri izin dan jalankan, coba setiap opsi:   
![a](image/15.png)  
### Latihan 9.3
Tambahkan ke script grading-batch.sh sebuah ringkasan di bagian bawah yang menampilkan: jumlah mahasiswa per grade (A, B, C, D, E) menggunakan perulangan for kedua yang mengiterasi array MAHASISWA.
- Buka kembali file lalu tambahkan:   
![a](image/16.png)  
- Hasil test:   
![a](image/17.png)  
## Praktikum 7.4 Library Fungsi Validasi
1. Buat file library:
```
nano ~/praktikum-os/week09/scripts/lib-validasi.sh
```
2. Ketik isi berikut:   
![a](image/18.png)  
3. Buat script yang menggunakan library:
```
nano ~/praktikum-os/week09/scripts/pakai-library.sh
```
4. Ketik isi berikut:   
![a](image/19.png)  
5. Beri izin dan uji semua skenario:   
![a](image/20.png)   
### Latihan 9.4
Tambahkan fungsi konfirmasi() ke lib-validasi.sh. Fungsi inimenampilkan pertanyaan, membaca input Y/N dari user, mengembalikan 0 jika Y dan 1 jika N. Buat script demo yang memanggil fungsi ini sebelum menghapus sebuah file.
- tambahan file pada lib-validasi.sh   
![a](image/21.png)  
- fungsi demo untuk memanggil fungsi konfirmasi()   
![a](image/22.png)    
- Beri izin dan test    
![a](image/23.png)   
## Praktikum 7.5 Script Backup dengan Opsi
1. Buat script: 
```
nano ~/praktikum-os/week09/scripts/backup-data.sh
```
2. Ketik isi berikut:   
![a](image/24.png)  
3. Beri izin dan uji:   
![a](image/25.png)    
## Praktikum 7.6 Debugging Script 
1. Buat script untuk dianalisis:
```
nano ~/praktikum-os/week09/scripts/debug-latihan.sh
```
2. Ketik isi berikut:   
![a](image/26.png)   
3. Cek sintaks, lalu jalankan dengan tracing:
![a](image/27.png)    
### Latihan 9.5
Script debug-latihan.sh tidak menangani direktori yang tidak ada. Perbaiki dengan menambahkan:
- set -e di baris kedua
- Pengecekan -d "$DIREKTORI" sebelum memanggil du
- Pesan error yang informatif jika direktori tidak ditemukan       

Uji dengan direktori yang tidak ada.
- tambahan kode pada file debug-latihan.sh   
![a](image/28.png)   
- Hasil test   
![a](image/29.png)    
## Tugas 1 Script Absensi Kelas  
1. buat file script dengan perintah:
```
nano absensi.sh
```
2. isi file:    
![a](image/30.png)   
![a](image/31.png)   
![a](image/32.png)  
3. beri izin execute dan jalankan:    
![a](image/33.png)   
![a](image/34.png)   
## Tugas 2 Script Health Check Sistem
1. buat file:
```
healthcheck.sh
```
2. isi file:   
![a](image/35.png)   
![a](image/36.png)   
![a](image/37.png)   
3. beri izin dan jalankan file:    
![a](image/38.png)   


