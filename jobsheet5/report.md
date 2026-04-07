# Laporan pertemuan ke -4 sistem operasi
**Tanggal:** 04 Maret 2026  
**Disusun Oleh:** Ariel Ardani Aris Putra  
**NIM:** 2541070200129  
**Kelas/No:** TI-1G/04

## Latihan 6.1
Jalankan ps aux dan amati outputnya:
1. Berapa total proses yang berjalan? Proses apa yang memiliki PID
terkecil?  
![a](image/lat611.png) 
Karena outputnya terlalu banyak jadi saya simpan outputnya pada file dengan perintah tee untuk memasukan hasil output ke suatu file
![a](image/lat612.png) 
lalu buka file nya dan scroll ke line paling bawah, lalu tekan ctrl+c sehingga bisa tampil lokasi line pada folder.
![a](image/lat613.png) 
maka terlihat proses yang sedang berjalan pada file saya ada 113 line. untuk melihat jumlah pid paling sedikit bisa dilihat di proses paling atas
![a](image/lat614.png) 
2. Jalankan pstree -p dan temukan proses bash Anda. Proses apa yang
menjadi induk (PPID) dari bash tersebut?
![a](image/lat615.png) 
Induk (PPID) dari bash Anda adalah proses su dengan PID 1029.
3. Bandingkan output ps aux dan ps aux -L. Apa perbedaan yang Anda lihat?
- ps aux: Digunakan untuk melihat Daftar Proses. Outputnya lebih ringkas karena satu baris hanya mewakili satu program yang sedang berjalan. Fokusnya adalah melihat aplikasi apa saja yang sedang aktif.
- ps aux -L: Digunakan untuk melihat Daftar Thread. Outputnya jadi jauh lebih panjang karena perintah ini membongkar isi di dalam proses. Satu proses yang sama bisa muncul berkali-kali di banyak baris kalau proses tersebut punya banyak thread (anak proses yang bekerja barengan).
## Latihan 6.2
1. Jalankan sleep 120 & dan amati kolom STAT pada ps aux. Kondisi apa yang ditampilkan? Mengapa proses sleep berada di kondisi tersebut?
![a](image/lat621.png) 
kolom STAT untuk proses sleep 120 (dengan PID 6535 dan 6537 pada gambar) menunjukkan status S. Status S merupakan singkatan dari Interruptible Sleep (Tidur yang bisa disela).
2. Jalankan beberapa perintah yang berhasil dan yang gagal, lalu catat exit code masing-masing. Pola apa yang Anda temukan?
![a](image/lat622.png) 
angka 0 menunjukan program sukses dan angka selain 0 menunjukan gagal
## Latihan 6.3
1. Jalankan nice -n 5 sleep 200 & dan verifikasi nilai NI-nya dengan ps.
![a](image/lat631.png) 
2. Ubah nilai nice menjadi 10 menggunakan renice, lalu verifikasi kembali.
![a](image/lat632.png) 
3. Coba ubah nilai nice menjadi -5 tanpa sudo. Apa yang terjadi? Mengapa Linux membatasi hal ini untuk user biasa?
![a](image/lat633.png) 
Linux membatasi pengaturan nice negatif bagi user biasa untuk memastikan tidak ada satu user pun yang bisa memonopoli CPU secara sepihak, sehingga sistem tetap stabil dan proses-proses penting milik sistem tidak terganggu
## Latihan 6.4
1. Jalankan sleep 400 &, kirim SIGSTOP, dan amati perubahan kolom STAT. Kondisi apa yang muncul?
![a](image/lat641.png) 
Kolom STAT akan berubah menjadi T
2. Kirim SIGCONT dan verifikasi proses kembali berjalan.
![a](image/lat642.png) 
3. Hentikan proses dengan SIGTERM lalu verifikasi sudah tidak ada. Kapan Anda memilih SIGKILL daripada SIGTERM?
![a](image/lat643.png) 
Jika di ibarakan pada windows 
- SIGTERM itu ibarat melakukan close aplikasi
- SIGKILL itu ibarat melakukan end task pada task manajer
- Kesimpulan nya pilih SIGTERM apabila program berjalan lancar dan pilih SIGKILL bila terjadi freeze atau program tidak berjalan lancar
## Latihan 6.5
1. Jalankan top di foreground. Apa yang terjadi di terminal?
![a](image/lat651.png) 
2. Tekan Ctrl+Z dan cek statusnya dengan jobs. Kondisi apa yang ditampilkan?
![a](image/lat652.png) 
3. Pindahkan ke background dengan bg. Apakah top dapat berjalan dengan baik di background? Mengapa?
![a](image/lat653.png) 
top tidak berjalan dengan baik, Meskipun setelah perintah bg tampilan top masih terlihat di layar terminal, sebenarnya proses tersebut tidak berjalan dengan baik (mengalami freeze)
4. Kembalikan ke foreground dengan fg, lalu keluar dengan q .
![a](image/lat654.png)  
## Latihan 6.6
1. Gunakan ps aux –sort=%mem untuk menemukan proses yang menggunakan memori paling banyak di VM Anda. Proses apa itu?
![a](image/lat661.png)  
proses fwupd yang ada pada direktori /usr/libexec/fwupd/fwupd
2. Di dalam top, tekan 1 . Apa yang berubah pada tampilan? Mengapa informasi ini berguna?
![a](image/lat662.png)  
![a](image/lat663.png)  
muncul Cpu1 setelah Cpu 0, informasi ini berguna untuk menampilkan Cpu yang ada.
3. Di dalam htop, navigasikan ke proses sshd menggunakan tombol panah. Tekan F9 dan amati opsi sinyal yang tersedia.
![a](image/lat664.png)  
## Latihan 6.A
Eksplorasi Proses Sistem
1. Jalankan ps aux –-forest dan temukan proses dengan PID 1. Apa nama dan fungsi proses tersebut dalam sistem Linux modern?
![a](image/lat6a1.png)  
nama /sbin/init. yang berfungsi mengelola proses lain, menjalankan layanan (seperti sshd tadi), dan memastikan sistem siap dipakai. Kalau systemd mati, sistem bakal crash (Kernel Panic).
2. Hitung berapa proses yang dimiliki oleh user root dan berapa yang dimiliki oleh user Anda. Mengapa root memiliki lebih banyak proses?
![a](image/lat6a2.png)  
Root harus menjalankan semua layanan latar belakang (background services/daemons) yang menjaga agar sistem operasi tetap hidup (seperti manajemen jaringan, log sistem, manajemen disk, keamanan, dll). Sedangkan User  biasanya cuma menjalankan proses yang berkaitan dengan aktivitas login
3. Temukan semua proses yang berada dalam kondisi S. Mengapa sebagian besar proses di sistem berada dalam kondisi ini?
![a](image/lat6a3.png)  
Sebagian besar proses di sistem Linux dalam kondisi S supaya penggunaan CPU tetap rendah (0%) dan baterai/daya VM nggak boros. Kalau semuanya R, VM bakal langsung panas dan lemot!
## Latihan 6.B
Simulasi Manajemen Job
1. Jalankan tiga perintah sleep dengan durasi 100, 200, dan 300 detik di background. Verifikasi ketiganya dengan jobs.
![a](image/lat6b1.png)  
2. Bawa job kedua ke foreground, jeda dengan Ctrl+Z , lalu kembalikan ke background dengan bg.
![a](image/lat6b2.png)  
3. Hentikan job pertama dengan kill %1. Tampilkan kembali daftar job. Berapa job yang tersisa?  
![a](image/lat6b3.png)  
tersisa 2 job.
## Latihan 6.C
Prioritas dan Sinyal
1. Jalankan dua proses sleep: satu dengan nice +5 dan satu dengan nice +15. Verifikasi nilai NI keduanya dengan ps.
![a](image/lat6c1.png)  
2. Gunakan renice untuk mengubah nice proses pertama menjadi +10. Proses mana yang kini lebih diprioritaskan scheduler?
![a](image/lat6c2.png)  
Di Linux, angka nice yang lebih kecil (mendekati -20) punya prioritas lebih tinggi, sedangkan angka yang besar (mendekati 19) punya prioritas rendah. maka proses yang pertama tetap diutamakan
3. Kirim SIGSTOP ke salah satu proses, verifikasi kondisi T-nya, lalu kirim SIGCONT. Akhiri semua proses percobaan dengan pkill sleep.

![a](image/lat6c3.png)  