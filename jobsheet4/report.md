# Laporan pertemuan ke -4 sistem operasi
**Tanggal:** 04 Maret 2026  
**Disusun Oleh:** Ariel Ardani Aris Putra  
**NIM:** 2541070200129  
**Kelas/No:** TI-1G/04

## TUGAS PENDAHULUAN:
    Jawablah pertanyaan-pertanyaan di bawah ini :
1. Apa yang dimaksud perintah-perintah direktory : pwd, cd, mkdir, rmdir.
2. Apa yang dimaksud perintah-perintah manipulasi file :	cp, mv dan rm (sertakan format yang digunakan)
3. Jelaskan perbedaan Symbolic link menggunakan hard link (direct) dan soft link (indirect).
4. Tuliskan maksud perintah-perintah : file, find, which, locate dan grep.

Jawab:  
1.  -  pwd (Print Working Directory)  
    Perintah untuk menampilkan direktori aktif saat ini (posisi kita sedang berada di folder mana).  
    -  cd (Change Directory)  
    Perintah untuk berpindah direktori.   
    -  mkdir (Make Directory)      
    Perintah untuk membuat direktori baru.  
    -  rmdir (Remove Directory)  
    Perintah untuk menghapus direktori kosong.

2.  - cp (Copy)  
    Perintah untuk menyalin file atau direktori.
    - mv (Move)
    Perintah untuk memindahkan atau mengganti nama file/direktori.
    - rm (Remove)
    Perintah untuk menghapus file atau direktori
3. Perbedaan
> Hard Link (Direct Link)
- Merupakan salinan referensi langsung ke inode file asli.
- Jika file asli dihapus, hard link tetap bisa mengakses data.
- Tidak bisa digunakan untuk folder.
- Tidak bisa lintas partisi.
> Soft Link (Symbolic Link / Indirect Link)

- Merupakan shortcut yang menunjuk ke file asli.
- Jika file asli dihapus, soft link akan rusak (broken link).
- Bisa digunakan untuk folder.
- Bisa lintas partisi.
4.  - file Digunakan untuk mengetahui tipe atau jenis suatu file.
    - find Digunakan untuk mencari file atau direktori berdasarkan kriteria tertentu.
    - which Digunakan untuk mengetahui lokasi file executable dari suatu perintah.
    - locate Digunakan untuk mencari file dengan cepat berdasarkan database sistem.
    - grep Digunakan untuk mencari teks tertentu di dalam file.
## Percobaan
### Percobaan 1 Direktory
- Melihat direktory home  
![a](image/Per11.png)
- Melihat direktori aktual dan parent direktori  
![a](image/Per12.png)
- Membuat satu direktori, lebih dari satu direktori atau sub direktori  
![a](image/Per13.png)
- Menghapus satu atau lebih direktori
Menghapus satu atau lebih direktori hanya dapat dilakukan pada direktori kosong dan hanya dapat dihapus oleh pemiliknya kecuali bila diberikan ijin aksesnya.
![a](image/Per14.png)
1. terdapat pesan error "rmdir: failed to remove 'B': Directory not empty"  
Perintah rmdir B gagal karena direktori B tidak kosong. rmdir hanya bisa menghapus direktori yang kosong.
2. terdapat pesan error "ls: cannot access 'B': No such file or directory"  
Direktori B sudah tidak ada lagi di sistem karena sudah di hapus dengan perintah "rmdir B/F B"

- Navigasi direktori dengan instruksi cd
Instruksi cd digunakan untuk pindah dari satu direktori ke direktori lain  
![a](image/Per15.png)

    terdapat pesan error "no such file or directory" hal tersebut dikarenakan file yang di tujukan sebagai change tidak ada

### Percobaan 2 : Manipulasi file
- Perintah cp untuk mengkopi file atau seluruh direktori  
![a](image/Per21.png)
- Perintah mv untuk memindah file  
![a](image/Per22.png)
- Perintah rm untuk menghapus file   
![a](image/Per23.png)

### Percobaan 3 : Symbolic Link
- Membuat Shortcut (file link)
![a](image/Per31.png)
### Percobaan 4 : Melihat Isi File
![a](image/Per41.png)
### Percobaan 5 : Mencari file
- Perintah find  
![a](image/Per51.png)
- Perintah which  
![a](image/Per52.png)
- Perintah locate  
![a](image/Per53.png)
### Percobaan 6 : Mencari text pada file
![a](image/Per61.png)
## LATIHAN:
1. Coba urutan perintah berikut :
- cd  
- pwd  
- ls –al  
- cd .
- pwd
- cd ..
- pwd
- ls -al
- cd ..
- pwd
- ls -al
- cd /etc
- ls –al | more
- cat passwd
- cd –
- pwd  
![a](image/Lat11.png)  
![a](image/Lat12.png)
![a](image/Lat13.png)
![a](image/Lat14.png)
![a](image/Lat15.png)

2. Lanjutkan penelusuran pohon pada sistem file menggunakan cd, ls, pwd dan cat. Telusuri direktory /bin, /usr/bin, /sbin, /tmp dan /boot.  
- /bin  
![a](image/Lat21.png)
- /usr/bin  
![a](image/Lat22.png)
- /sbin  
![a](image/Lat23.png)
- /tmp  
![a](image/Lat24.png)
- /boot  
![a](image/Lat25.png)
3. Telusuri direktory /dev. Identifikasi perangkat yang tersedia. Identifikasi tty (termninal) Anda (ketik who am i); siapa pemilih tty Anda (gunakan ls –l).  
![a](image/Lat31.png)
![a](image/Lat32.png)
4. Telusuri derectory /proc. Tampilkan isi file interrupts, devices, cpuinfo, meminfo dan uptime menggunakan perintah cat. Dapatkah Anda melihat mengapa directory /proc disebut pseudo -filesystem yang memungkinkan akses ke struktur data kernel ? 
- cat interrupts   
![a](image/Lat41.png)
- cat devices    
![a](image/Lat42.png)
- cat cpuinfo  
![a](image/Lat43.png)
- cat meminfo  
![a](image/Lat44.png)
- cat uptime  
![a](image/Lat45.png)
5. Ubahlah direktory home ke user lain secara langsung menggunakan cd ~username.  
![a](image/Lat.png)
6. Ubah kembali ke direktory home Anda.  
![a](image/Lat.png)
7. Buat subdirektory work dan play.  
![a](image/Lat.png)
8. Hapus subdirektory work.  
![a](image/Lat.png)
9. Copy file /etc/passwd ke direktory home Anda.  
![a](image/Lat.png)
10. Pindahkan ke subirectory play.  
![a](image/Lat.png)
11. Ubahlah ke subdirektory play dan buat symbolic link dengan nama terminal yang menunjuk ke perangkat tty. Apa yang terjadi jika melakukan hard link ke perangkat tty ?  
![a](image/Lat.png)
12. Buatlah file bernama hello.txt yang berisi kata ”hello word”. Dapatkah Anda gunakan ”cp” menggunakan ”terminal” sebagai file asal untuk menghasilkan efek yang sama ?  
![a](image/Lat.png)
13. Copy hello.txt ke terminal. Apa yang terjadi ?  
![a](image/Lat.png)
14. Masih direktory home, copy keseluruhan direktory play ke direktory bernama work menggunakan symbolic link.  
![a](image/Lat.png)
15. Hapus direktory work dan isinya dengan satu perintah.  
![a](image/Lat.png)

## LAPORAN RESMI:

1. Analisa hasil percobaan yang Anda lakukan.'

    a. Analisa setiap hasil tampilannya.  
    b. Pada Percobaan 1 point 3 buatlah pohon dari struktur file dan direktori.  
    c. Bila terdapat pesan error, jelaskan penyebabnya.

2. Kerjakan latihan diatas dan analisa hasil tampilannya.
3. Berikan kesimpulan dari praktikum ini.
