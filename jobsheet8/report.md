# Laporan pertemuan ke -10 sistem operasi
**Tanggal:** 14 April 2026  
**Disusun Oleh:** Ariel Ardani Aris Putra  
**NIM:** 2541070200129  
**Kelas/No:** TI-1G/04

## Studi Kasus 10.1 Server Lambat karena Memori

Skenario: Server aplikasi terasa lambat saat banyak pengguna aktif. Administrator perlu menentukan apakah penyebabnya adalah kekurangan memori.
1. Periksa kondisi memori secara keseluruhan.
 ```
ariel@ubuntuser:~$ free -h
               total        used        free      shared  buff/cache   available
Mem:           3.3Gi       393Mi       2.4Gi       1.0Mi       731Mi       3.0Gi
Swap:             0B          0B          0B
 ```
2. Pantau proses secara real-time.
 ```
ariel@ubuntuser:~$ cat /proc/meminfo | head -n 20
MemTotal:        3504512 kB
MemFree:         2504128 kB
MemAvailable:    3104560 kB
Buffers:           25928 kB
Cached:           707088 kB
SwapCached:            0 kB
Active:           299468 kB
Inactive:         518608 kB
Active(anon):      94888 kB
Inactive(anon):        0 kB
Active(file):     204580 kB
Inactive(file):   518608 kB
Unevictable:       27316 kB
Mlocked:           27316 kB
SwapTotal:             0 kB
SwapFree:              0 kB
Zswap:                 0 kB
Zswapped:              0 kB
Dirty:               860 kB
Writeback:             0 kB
 ```
Analisis:
1. Apakah nilai available sangat kecil (misalnya di bawah 200 MB pada server
dengan RAM 2 GB)? Jika ya, server kemungkinan kekurangan memori.
2. Apakah kolom used pada baris Swap lebih dari 0? Jika ya, kernel sedang
menggunakan swap, yang berarti performa menurun.
3. Di tampilan top, proses apa yang memiliki %MEM terbesar? Proses tersebut
menjadi kandidat utama penyebab lambatnya server.

hasil:
1. Kondisi Memori (Available)
* Status: Sangat Sehat / Aman.
* Analisis: Memori yang tersedia (available) sebesar 3.0 GiB dari total 3.3 GiB (~90%).
2. Penggunaan Swap
* Status: Tidak Aktif (0B).
* Analisis: Server tidak memiliki partisi atau file Swap.

3. Pemantauan Proses (%MEM)
* Status: Ringan.
* Analisis: Memori yang benar-benar terpakai (used) hanya 393 MiB. 

 ## Praktikum 10.2 Mengamati Aktivitas Paging
Jalankan vmstat dengan interval 1 detik, 5 sampel.
```
ariel@ubuntuser:~$ vmstat 1 5
procs -----------memory---------- ---swap-- -----io---- -system-- -------cpu-------
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st gu
 0  0      0 2483996  26848 757176    0    0   114   114 1570    0  0  4 96  0  0  0
 0  0      0 2483996  26848 757296    0    0     0     0 1901  178  0  3 97  0  0  0
 0  0      0 2483996  26856 757324    0    0     0    36 1783  169  0  4 96  0  0  0
 0  0      0 2483996  26856 757324    0    0     0     0 1720   79  0  4 96  0  0  0
 0  0      0 2483744  26856 757404    0    0     0     0 1640  108  0  4 96  0  0  0
```
Analisis:
1. Amati nilai si dan so pada kelima baris. Pada sistem normal dengan RAM cukup, kedua nilai ini selalu 0.
2. Jika nilai si atau so sesekali muncul lebih dari 0, artinya pernah ada aktivitas swap. Ini masih wajar jika tidak terus-menerus.
3. Jika si dan so terus-menerus lebih dari 0, sistem dalam kondisi memory pressure serius — performa turun drastis karena akses disk jauh lebih lambat dari RAM.
4. Perhatikan juga kolom free (RAM kosong) dan buff (buffer) untuk memahami kondisi keseluruhan RAM saat itu.

Hasil:
1. Pengamatan si (Swap-In) dan so (Swap-Out)
* Status: Sangat Stabil.
* Analisis: Nilai si dan so pada kelima baris sampel secara konsisten menunjukkan angka 0.

2. Indikasi Memory Pressure
* Status: Tidak Ada Kendala.
* Analisis: Karena nilai si dan so tetap 0, sistem tidak mengalami tekanan memori (memory pressure).

3. Kondisi RAM (Free & Buff)
* Status: Melimpah.
* Analisis: Kolom free menunjukkan sisa RAM yang luas di kisaran 2.4 GB, sementara kolom buff stabil di angka 26 MB.

## Praktikum 10.3 Membuat dan Mengonfigurasi Swap File
sebelum itu pastika  sudah masuk ke direktori week10-memory
```
ariel@ubuntuser:~$ cd ~/praktikum-os/week10-memory
```
1.  Buat file berukuran 512 MB sebagai calon swap.
```
ariel@ubuntuser:~/praktikum-os/week10-memory$ sudo fallocate -l 512M /swapfile-week10
```
2.  Atur permission file menjadi 600 — hanya root yang boleh membaca dan menulis.
```
ariel@ubuntuser:~/praktikum-os/week10-memory$ sudo chmod 600 /swapfile-week10
```
3. Format file sebagai area swap, lalu aktifkan.
```
ariel@ubuntuser:~/praktikum-os/week10-memory$ sudo mkswap /swapfile-week10
Setting up swapspace version 1, size = 512 MiB (536866816 bytes)
no label, UUID=dec9a6e1-1fdf-4733-88fc-ced23501bd6c
ariel@ubuntuser:~/praktikum-os/week10-memory$ sudo swapon /swapfile-week10
```
4.  Verifikasi swap aktif. Anda akan melihat entri /swapfile-week10
```
ariel@ubuntuser:~/praktikum-os/week10-memory$ swapon --show
NAME             TYPE SIZE USED PRIO
/swapfile-week10 file 512M   0B   -2
```

5.  Periksa nilai swappiness, ubah sementara, dan verifikasi perubahan.
```
ariel@ubuntuser:~/praktikum-os/week10-memory$ free -h
               total        used        free      shared  buff/cache   available
Mem:           3.3Gi       407Mi       2.3Gi       1.0Mi       790Mi       2.9Gi
Swap:          511Mi          0B       511Mi
```
Analisis:
1. Berapa nilai swappiness default? Apa artinya bagi perilaku kernel dalam menggunakan swap?
* Nilai swappiness default pada sistem Linux umumnya adalah 60
2. Setelah diubah ke 10, konfirmasi nilai berubah pada output cat kedua. Apa dampak nilai 10 terhadap penggunaan swap dibanding nilai 60?
* membuat kernel menjadi lebih pasif dalam menggunakan swap.
3. Apakah entri /swapfile-week10 muncul di swapon –show? Jika tidak, pastikan Langkah 2 (chmod 600) sudah dijalankan sebelum Langkah 3.

* muncul dengan ukuran 512 MB.

## Praktikum 10.4 Monitoring Memory

1. Ambil snapshot proses diurutkan dari penggunaan memori terbesar.
```
ariel@ubuntuser:~/praktikum-os/week10-memory$ ps aux --sort=-%mem | head
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root        2502  0.1  1.2 552264 42360 ?        Ssl  15:47   0:00 /usr/libexec/fwupd/fwupd
root         374  0.0  0.7 288988 27324 ?        SLsl 13:14   0:04 /sbin/multipathd -d -s
root         690  0.0  0.6 109688 23068 ?        Ssl  13:14   0:00 /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal
root        2498  0.0  0.6 372596 21048 ?        Ssl  15:47   0:00 /usr/libexec/packagekitd
root         324  0.0  0.4  66852 17308 ?        S<s  13:14   0:00 /usr/lib/systemd/systemd-journald
root         627  0.0  0.3 468972 13580 ?        Ssl  13:14   0:00 /usr/libexec/udisks2/udisksd
root           1  0.0  0.3  22548 13576 ?        Ss   13:14   0:05 /sbin/init splash noprompt noshell automatic-ubiquity
systemd+     436  0.0  0.3  21584 13060 ?        Ss   13:14   0:00 /usr/lib/systemd/systemd-resolved
root         704  0.0  0.3 392100 12816 ?        Ssl  13:14   0:00 /usr/sbin/ModemManager
```
2.  Pantau secara real-time dengan top.
```
ariel@ubuntuser:~/praktikum-os/week10-memory$ top
top - 15:52:53 up  2:37,  1 user,  load average: 0.19, 0.55, 0.32
Tasks: 126 total,   1 running, 125 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni, 95.8 id,  0.0 wa,  0.0 hi,  4.2 si,  0.0 st
MiB Mem : 12.2/3422.4   [                                                                                             ]
MiB Swap:  0.0/512.0    [                                                                                             ]

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
   1548 ariel     20   0   13764   7548   6048 S   3.7   0.2   0:04.20 sshd
   1779 root      20   0       0      0      0 I   3.7   0.0   0:00.90 kworker/u8:0-events_unbound
   9444 ariel     20   0    8916   5604   3416 R   3.7   0.2   0:00.19 top
      1 root      20   0   22548  13576   9496 S   0.0   0.4   0:05.77 systemd
      2 root      20   0       0      0      0 S   0.0   0.0   0:00.02 kthreadd
      3 root      20   0       0      0      0 S   0.0   0.0   0:00.00 pool_workqueue_release
      4 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-rcu_g
      5 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-rcu_p
      6 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-slub_
      7 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-netns
     10 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/0:0H-events_highpri
     11 root      20   0       0      0      0 I   0.0   0.0   0:04.74 kworker/u6:0-ipv6_addrconf
     12 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/R-mm_pe
     13 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_kthread
     14 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_rude_kthread
     15 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_trace_kthread
     16 root      20   0       0      0      0 S   0.0   0.0   0:04.65 ksoftirqd/0
     17 root      20   0       0      0      0 I   0.0   0.0   0:05.62 rcu_preempt
     18 root      rt   0       0      0      0 S   0.0   0.0   0:00.49 migration/0
     19 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/0
     20 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/0
     21 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/1
     22 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/1
``` 
Analisis:
1. Proses apa yang berada di urutan pertama? Catat nilai %MEM dan RSS-nya.
* , proses yang berada di urutan pertama adalah /usr/libexec/fwupd/fwupd (PID 2502). %MEM sebesar 1.2%, nilai RSS sebesar 42360 KB.
2. Konversikan RSS dari KB ke MB (bagi 1024). Misalnya, RSS=524288 berarti proses menggunakan 512 MB RAM. Apakah wajar untuk jenis program tersebut?
* 41.36 MB. aran: Penggunaan memori sebesar 41 MB untuk fwupd (Firmware Update Daemon) tergolong sangat wajar.
3. Mengapa VSZ selalu lebih besar dari RSS pada proses yang sama?
* VSZ (Virtual Set Size) selalu lebih besar dari RSS (Resident Set Size) karena VSZ mencakup seluruh memori yang dialokasikan oleh proses, termasuk pustaka (libraries) yang dibagikan, memori yang di-swap, serta memori yang sudah dipesan tetapi belum benar-benar digunakan. Sedangkan RSS hanya menghitung bagian memori yang saat ini benar-benar berada di dalam RAM fisik.
4. Apakah urutan proses di ps konsisten dengan tampilan top saat diurutkan berdasarkan %MEM?
* Urutan proses antara ps aux dan top menunjukkan konsistensi dalam hal identitas proses yang berjalan, namun nilai spesifiknya bisa sedikit berbeda karena top bersifat real-time.

## Praktikum 10.5 Script Monitor Memori

1. Masuk ke direktori kerja dan buat file script:
```
ariel@ubuntuser:~/praktikum-os/week10-memory$ nano monitor-memori.sh
```

2. Ketik script berikut:
```
#!/bin/bash
set -euo pipefail

THRESHOLD=20

echo "=== Monitor Memori ==="
date
echo

free -h
echo

AVAIL=$(free | awk '/Mem/ {printf "%d", $7/$2*100}')
if [ "$AVAIL" -lt "$THRESHOLD" ]; then
    echo "PERINGATAN: Memori tersedia hanya ${AVAIL}%!"
else
    echo "Status: Memori tersedia ${AVAIL}% (normal)"
fi
echo

echo "--- 5 Proses Memori Tertinggi ---"
ps aux --sort=-%mem | head -n 6 | tail -n 5
```
3. beri izin execute lalu jalankan program
```
ariel@ubuntuser:~/praktikum-os/week10-memory$ chmod +x monitor-memori.sh
ariel@ubuntuser:~/praktikum-os/week10-memory$ bash monitor-memori.sh
=== Monitor Memori ===
Tue May 12 16:10:09 UTC 2026

               total        used        free      shared  buff/cache   available
Mem:           3.3Gi       415Mi       1.8Gi       1.0Mi       1.4Gi       2.9Gi
Swap:          511Mi          0B       511Mi

Status: Memori tersedia 87% (normal)

--- 5 Proses Memori Tertinggi ---
root         374  0.0  0.7 288988 27324 ?        SLsl 13:14   0:05 /sbin/multipathd -d -s
root         690  0.0  0.6 109688 23068 ?        Ssl  13:14   0:00 /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal
root         324  0.0  0.4  66852 17316 ?        S<s  13:14   0:00 /usr/lib/systemd/systemd-journald
root         627  0.0  0.3 468972 13580 ?        Ssl  13:14   0:00 /usr/libexec/udisks2/udisksd
root           1  0.0  0.3  22548 13576 ?        Ss   13:14   0:05 /sbin/init splash noprompt noshell automatic-ubiquity
```

Analisis:
1. Variabel THRESHOLD=20 menetapkan batas persentase. Perintah free | awk ’/Mem/ {printf "%d", $7/$2*100}’ mengambil kolom ke-7 (available) dibagi kolom ke-2 (total) dari baris Mem, lalu dikalikan 100 untuk menghasilkan persentase bilangan bulat.
2. Kondisi if [ "$AVAIL" -lt "$THRESHOLD" ] bernilai benar jika persentase memori tersedia di bawah 20.
3. Ubah THRESHOLD menjadi 90 dan jalankan ulang. Apa yang berubah pada output? Mengapa demikian?
* setelah di ubah hasil output menjadi
```
ariel@ubuntuser:~/praktikum-os/week10-memory$ bash monitor-memori.sh
=== Monitor Memori ===
Tue May 12 16:14:46 UTC 2026

               total        used        free      shared  buff/cache   available
Mem:           3.3Gi       415Mi       1.8Gi       1.0Mi       1.4Gi       2.9Gi
Swap:          511Mi          0B       511Mi

PERINGATAN: Memori tersedia hanya 87%!

--- 5 Proses Memori Tertinggi ---
root         374  0.0  0.7 288988 27324 ?        SLsl 13:14   0:05 /sbin/multipathd -d -s
root         690  0.0  0.6 109688 23068 ?        Ssl  13:14   0:00 /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal
root         324  0.0  0.4  66852 17316 ?        S<s  13:14   0:00 /usr/lib/systemd/systemd-journald
root         627  0.0  0.3 468972 13580 ?        Ssl  13:14   0:00 /usr/libexec/udisks2/udisksd
root           1  0.0  0.3  22548 13576 ?        Ss   13:14   0:05 /sbin/init splash noprompt noshell automatic-ubiquity
ariel@ubuntuser:~/praktikum-os/week10-memory$
```
* Pesan Status: Memori tersedia 87% (normal) akan berubah menjadi PERINGATAN: Memori tersedia hanya 87%!.
Karena nilai ketersediaan memori saat ini (87%) lebih kecil daripada ambang batas baru (90%), kondisi if [ 87 -lt 90 ] menjadi benar (true).

## Praktikum 10.6 Mengamati System Call dengan strace

1.  Lihat 30 baris pertama system call dari perintah ls.
```
ariel@ubuntuser:~/praktikum-os/week10-memory$ strace ls 2>&1 | head -n 30
execve("/usr/bin/ls", ["ls"], 0x7fff54a72420 /* 18 vars */) = 0
brk(NULL)                               = 0x562ffeb93000
mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7b5352dbe000
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=32431, ...}) = 0
mmap(NULL, 32431, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7b5352db6000
close(3)                                = 0
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libselinux.so.1", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\0\0\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0644, st_size=174472, ...}) = 0
mmap(NULL, 181960, PROT_READ, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7b5352d89000
mmap(0x7b5352d8f000, 118784, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x6000) = 0x7b5352d8f000
mmap(0x7b5352dac000, 24576, PROT_READ, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x23000) = 0x7b5352dac000
mmap(0x7b5352db2000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x29000) = 0x7b5352db2000
mmap(0x7b5352db4000, 5832, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7b5352db4000
close(3)                                = 0
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\220\243\2\0\0\0\0\0"..., 832) = 832
pread64(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784, 64) = 784
fstat(3, {st_mode=S_IFREG|0755, st_size=2125328, ...}) = 0
pread64(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784, 64) = 784
mmap(NULL, 2170256, PROT_READ, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7b5352a00000
mmap(0x7b5352a28000, 1605632, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x28000) = 0x7b5352a28000
mmap(0x7b5352bb0000, 323584, PROT_READ, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1b0000) = 0x7b5352bb0000
mmap(0x7b5352bff000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1fe000) = 0x7b5352bff000
mmap(0x7b5352c05000, 52624, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7b5352c05000
close(3)                                = 0
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libpcre2-8.so.0", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\0\0\0\0\0\0\0\0"..., 832) = 832
```
2. Lihat ringkasan statistik dan bandingkan dua direktori berbeda.
```
ariel@ubuntuser:~/praktikum-os/week10-memory$ strace -c ls
monitor-memori.sh
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
 31.48    0.002599         371         7           fstat
 27.19    0.002245         132        17           mmap
  8.07    0.000666         111         6           openat
  6.35    0.000524         174         3           brk
  5.27    0.000435          87         5           read
  4.23    0.000349          43         8           close
  2.85    0.000235          47         5           mprotect
  2.25    0.000186          93         2         2 access
  1.66    0.000137          68         2           getdents64
  1.61    0.000133         133         1           rseq
  1.49    0.000123          61         2         2 statfs
  1.47    0.000121         121         1           arch_prctl
  1.31    0.000108          54         2           pread64
  0.87    0.000072          72         1           set_tid_address
  0.81    0.000067          67         1           write
  0.81    0.000067          33         2           ioctl
  0.73    0.000060          60         1           set_robust_list
  0.67    0.000055          55         1           munmap
  0.59    0.000049          49         1           getrandom
  0.31    0.000026          26         1           prlimit64
  0.00    0.000000           0         1           execve
------ ----------- ----------- --------- --------- ----------------
100.00    0.008257         117        70         4 total
ariel@ubuntuser:~/praktikum-os/week10-memory$ strace -c ls /etc 2>&1 | tail
-5
  0.00    0.000000           0         1           getrandom
  0.00    0.000000           0         1           statx
  0.00    0.000000           0         1           rseq
------ ----------- ----------- --------- --------- ----------------
100.00    0.001885          26        70         5 total
```

Analisis:
1. Dari output Langkah 1, identifikasi minimal 4 system call berbeda. Jelaskan fungsi singkat masing-masing berdasarkan argumen yang terlihat.
execve: Digunakan untuk menjalankan program (dalam hal ini /usr/bin/ls). Ini adalah langkah pertama untuk memulai proses baru.

brk: Digunakan untuk mengelola alokasi memori pada segmen data (heap). Biasanya dipanggil untuk memperbesar atau memperkecil ruang memori yang tersedia bagi program.

mmap: Digunakan untuk memetakan file atau perangkat ke dalam memori. Dalam log tersebut, mmap sering digunakan untuk memuat pustaka sistem (.so) ke dalam ruang alamat memori proses.
* execve: Digunakan untuk menjalankan program (dalam hal ini /usr/bin/ls). Ini adalah langkah pertama untuk memulai proses baru.

* brk: Digunakan untuk mengelola alokasi memori pada segmen data (heap). Biasanya dipanggil untuk memperbesar atau memperkecil ruang memori yang tersedia bagi program.

* mmap: Digunakan untuk memetakan file atau perangkat ke dalam memori. Dalam log tersebut, mmap sering digunakan untuk memuat pustaka sistem (.so) ke dalam ruang alamat memori proses
* openat: Digunakan untuk membuka file (seperti /etc/ld.so.cache atau direktori pustaka). Angka yang dihasilkan (file descriptor) akan digunakan oleh sistem call berikutnya untuk membaca isi file tersebut.
2. Dari ringkasan strace -c, system call mana yang paling sering dipanggil? Mengapa?
* mmap adalah salah satu yang paling sering dipanggil (17 kali). Hal ini terjadi karena setiap program Linux perlu memetakan berbagai pustaka dependensi (shared libraries) ke dalam memori agar fungsi-fungsi dasar program bisa berjalan.
3. Apakah ada system call dengan errors lebih dari 0? Apakah itu berarti
program bermasalah, ataukah bagian normal dari logika program?
* Terdapat error pada system call access (2 error) dan statfs (2 error). Ini tidak berarti program rusak atau bermasalah. Dalam logika program Linux, hal ini normal.
4. Apakah jumlah system call berbeda antara ls dan ls /etc? Faktor apa yang
menyebabkan perbedaan tersebut?
* Jumlah total system call pada kedua perintah menunjukkan angka yang hampir sama (total 70 panggilan), namun waktu eksekusinya bisa berbeda. Perbedaan jumlah panggilan biasanya dipengaruhi oleh jumlah entri di dalam direktori tersebut. P
## 1.6 Tugas Praktikum
## Tugas 10.1 Audit Penggunaan Memori Sistem
1. Buat script memory-audit.sh yang menghasilkan laporan kondisi memori sistem secara otomatis.
 ```
nano ~/praktikum-os/week10-memory/memory-audit.sh
```
2. isi file
```
#!/bin/bash
set -euo pipefail

LAPORAN="memory-report.txt"

{
    echo "=== LAPORAN MEMORI SISTEM ==="
    date
    echo
    echo "--- Ringkasan free -h ---"
    free -h
    echo
    echo "--- /proc/meminfo ---"
    cat /proc/meminfo | head -n 20
} > "$LAPORAN"

echo "Laporan disimpan ke: $LAPORAN"
cat "$LAPORAN"
```
3. berikan izin execute lalu jalankan
```
ariel@ubuntuser:~/praktikum-os/week10-memory$ chmod +x ~/praktikum-os/week10-memory/memory-audit.sh
ariel@ubuntuser:~/praktikum-os/week10-memory$ bash memory-audit.sh
Laporan disimpan ke: memory-report.txt
=== LAPORAN MEMORI SISTEM ===
Tue May 12 16:29:00 UTC 2026

--- Ringkasan free -h ---
               total        used        free      shared  buff/cache   available
Mem:           3.3Gi       413Mi       1.8Gi       1.0Mi       1.4Gi       2.9Gi
Swap:          511Mi          0B       511Mi

--- /proc/meminfo ---
MemTotal:        3504512 kB
MemFree:         1846188 kB
MemAvailable:    3080604 kB
Buffers:           37188 kB
Cached:          1260304 kB
SwapCached:            0 kB
Active:           262748 kB
Inactive:        1070904 kB
Active(anon):      45984 kB
Inactive(anon):        0 kB
Active(file):     216764 kB
Inactive(file):  1070904 kB
Unevictable:       27316 kB
Mlocked:           27316 kB
SwapTotal:        524284 kB
SwapFree:         524284 kB
Zswap:                 0 kB
Zswapped:              0 kB
Dirty:                 0 kB
Writeback:             0 kB
ariel@ubuntuser:~/praktikum-os/week10-memory$
```
Analisis
1. Hitung persentase memori tersedia (available / total × 100%). Apakah sistem dalam kondisi normal?
* Data: Available = 2.9 GiB, Total = 3.3 GiB.

* Perhitungan: (2.9 / 3.3) × 100% = 87.8%.
* sistem dalam kondisi normal
2. Mengapa buff/cache tidak dihitung sebagai memori yang terpakai dari sudut pandang ketersediaan untuk aplikasi?
* karena sifatnya yang dinamis.
3. Dari /proc/meminfo, apakah SwapTotal lebih besar dari 0? Berapa nilai SwapFree?
* Data SwapTotal: 524284 kB. Nilai SwapTotal lebih besar dari 0.
* Data SwapFree: 524284 kB.
## Tugas 10.2 Identifikasi Proses dengan Memori Tertinggi
1. Simpan daftar 10 proses pengguna memori terbesar ke file.

```
ariel@ubuntuser:~/praktikum-os/week10-memory$ ps aux --sort=-%mem | head -n 10 > top-memory-process.txt
ariel@ubuntuser:~/praktikum-os/week10-memory$ cat top-memory-process.txt
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         374  0.0  0.7 288988 27324 ?        SLsl 13:14   0:05 /sbin/multipathd -d -s
root         690  0.0  0.6 109688 23068 ?        Ssl  13:14   0:00 /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal
root         324  0.0  0.4  66852 17328 ?        S<s  13:14   0:00 /usr/lib/systemd/systemd-journald
root         627  0.0  0.3 468972 13580 ?        Ssl  13:14   0:00 /usr/libexec/udisks2/udisksd
root           1  0.0  0.3  22548 13576 ?        Ss   13:14   0:05 /sbin/init splash noprompt noshell automatic-ubiquity
systemd+     436  0.0  0.3  21584 13060 ?        Ss   13:14   0:00 /usr/lib/systemd/systemd-resolved
root         704  0.0  0.3 392100 12816 ?        Ssl  13:14   0:00 /usr/sbin/ModemManager
ariel       1503  0.0  0.3  20088 11212 ?        Ss   13:41   0:00 /usr/lib/systemd/systemd --user
polkitd      610  0.0  0.2 383664  9948 ?        Ssl  13:14   0:01 /usr/lib/polkit-1/polkitd --no-debug
ariel@ubuntuser:~/praktikum-os/week10-memory$
```

Analisis
1. Proses apa di urutan pertama? Catat nilai %MEM dan RSS.
* /sbin/multipathd. dengan nilai %MEM sebesar 0.7% dan nilai RSS sebesar 27324 KB.
2. Konversikan RSS ke MB (bagi 1024). Apakah wajar?
* 26.68 MB, wajar.
3. Jumlahkan %MEM dari 5 proses teratas. Berapa persen RAM yang mereka
gunakan bersama?
* 0.7 + 0.6 + 0.4 + 0.3 + 0.3 = 2.3%
## Tugas 10.3 Membuat dan Memverifikasi Swap File
1.  Buat swap file khusus tugas sebesar 256 MB dan verifikasi.
```
ariel@ubuntuser:~/praktikum-os/week10-memory$ sudo fallocate -l 256M /swapfile-tugas-week10
[sudo] password for ariel:
ariel@ubuntuser:~/praktikum-os/week10-memory$ sudo chmod 600 /swapfile-tugas-week10
ariel@ubuntuser:~/praktikum-os/week10-memory$ sudo mkswap /swapfile-tugas-week10
Setting up swapspace version 1, size = 256 MiB (268431360 bytes)
no label, UUID=5765df55-8bc9-449a-996a-7e1adc8665bd
ariel@ubuntuser:~/praktikum-os/week10-memory$ sudo swapon /swapfile-tugas-week10
```
2. verifikasi dan simpan hasil
```
ariel@ubuntuser:~/praktikum-os/week10-memory$ {
    echo "=== VERIFIKASI SWAP ==="
    swapon --show
    echo
    free -h
} > swap-check.txt
cat swap-check.txt
=== VERIFIKASI SWAP ===
NAME                   TYPE SIZE USED PRIO
/swapfile-week10       file 512M   0B   -2
/swapfile-tugas-week10 file 256M   0B   -3

               total        used        free      shared  buff/cache   available
Mem:           3.3Gi       418Mi       1.8Gi       1.1Mi       1.4Gi       2.9Gi
Swap:          767Mi          0B       767Mi
ariel@ubuntuser:~/praktikum-os/week10-memory$
```
Analisis
1. Identifikasi kolom NAME, TYPE, SIZE, dan USED pada output swapon –show.
* NAME: Menunjukkan lokasi atau nama file yang digunakan sebagai area swap. Pada output Anda, terdapat dua entri aktif, yaitu /swapfile-week10 dan /swapfile-tugas-week10.

* TYPE: Menunjukkan jenis area swap. Nilai file berarti swap tersebut berbentuk file yang terletak di dalam sistem file, bukan partisi disk terpisah.

* SIZE: Menunjukkan kapasitas total memori virtual yang disediakan. File tugas yang baru dibuat memiliki ukuran 256M.

* USED: Menunjukkan jumlah data yang saat ini sedang disimpan di area swap. Nilai 0B menunjukkan bahwa RAM fisik masih mencukupi sehingga kernel belum memindahkan data ke swap
2. Apakah nilai total pada baris Swap di free -h bertambah 256 MB?
* bertambah menjadi 767Mi. 512+216=767 artinya swap sudah bertambah sesuai dengan tambahan swap baru
3. Mengapa permission 600 penting? Apa risiko jika diatur ke 644?
* Izin akses 600 (read/write hanya untuk pemilik/root) sangat krusial karena area swap berisi data mentah dari RAM fisik yang sedang tidak digunakan.

* Jika diatur ke 644 (world-readable), pengguna lain di sistem tersebut dapat membaca isi file swap secara langsung

## Tugas 10.4 Analisis System Call dengan strace
1. Analisis system call yang dipanggil perintah ls.
```
ariel@ubuntuser:~/praktikum-os/week10-memory$ strace -c ls 2> strace-summary.txt
memory-audit.sh    monitor-memori.sh   swap-check.txt
memory-report.txt  strace-summary.txt  top-memory-process.txt
ariel@ubuntuser:~/praktikum-os/week10-memory$ strace ls /etc 2> strace-ls-etc.txt
ImageMagick-6           iproute2             python3.12
ModemManager            iscsi                rc0.d
PackageKit              issue                rc1.d
UPower                  issue.net            rc2.d
X11                     kernel               rc3.d
adduser.conf            landscape            rc4.d
alternatives            ld.so.cache          rc5.d
apparmor                ld.so.conf           rc6.d
apparmor.d              ld.so.conf.d         rcS.d
apport                  ldap                 resolv.conf
apt                     legal                rmt
bash.bashrc             libaudit.conf        rpc
bash_completion         libblockdev          rsyslog.conf
bash_completion.d       libibverbs.d         rsyslog.d
bindresvport.blacklist  libnl-3              screenrc
binfmt.d                libpaper.d           security
byobu                   locale.alias         selinux
ca-certificates         locale.conf          sensors.d
ca-certificates.conf    locale.gen           sensors3.conf
cloud                   localtime            services
console-setup           logcheck             sgml
credstore               login.defs           shadow
credstore.encrypted     logrotate.conf       shadow-
cron.d                  logrotate.d          shells
cron.daily              lsb-release          skel
cron.hourly             lvm                  sos
cron.monthly            machine-id           ssh
cron.weekly             magic                ssl
cron.yearly             magic.mime           subgid
crontab                 manpath.config       subgid-
cryptsetup-initramfs    mdadm                subuid
crypttab                mime.types           subuid-
dbus-1                  mke2fs.conf          sudo.conf
debconf.conf            modprobe.d           sudo_logsrvd.conf
debian_version          modules              sudoers
default                 modules-load.d       sudoers.d
deluser.conf            mtab                 supercat
depmod.d                multipath            sysctl.conf
dhcp                    multipath.conf       sysctl.d
dhcpcd.conf             nanorc               sysstat
dpkg                    needrestart          systemd
e2scrub.conf            netconfig            terminfo
environment             netplan              thermald
ethertypes              network              timezone
fonts                   networkd-dispatcher  tmpfiles.d
fstab                   networks             ubuntu-advantage
fuse.conf               newt                 ucf.conf
fwupd                   nftables.conf        udev
gai.conf                nsswitch.conf        udisks2
ghostscript             opt                  ufw
gnutls                  os-release           update-manager
groff                   overlayroot.conf     update-motd.d
group                   pam.conf             update-notifier
group-                  pam.d                usb_modeswitch.conf
grub.d                  papersize            usb_modeswitch.d
gshadow                 passwd               vconsole.conf
gshadow-                passwd-              vim
gss                     perl                 vmware-tools
hdparm.conf             pki                  vtrgb
host.conf               plymouth             w3m
hostname                pm                   wgetrc
hosts                   polkit-1             xattr.conf
hosts.allow             pollinate            xdg
hosts.deny              profile              xml
init.d                  profile.d            zsh_command_not_found
initramfs-tools         protocols
inputrc                 python3
ariel@ubuntuser:~/praktikum-os/week10-memory$ cat strace-summary.txt
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
 67.67    0.001218        1218         1           execve
  8.61    0.000155          77         2         2 access
  8.56    0.000154          19         8           close
  8.50    0.000153           9        17           mmap
  2.22    0.000040           6         6           openat
  1.11    0.000020          10         2           getdents64
  0.78    0.000014           2         5           mprotect
  0.67    0.000012           1         7           fstat
  0.56    0.000010           2         5           read
  0.56    0.000010           5         2           write
  0.28    0.000005           2         2           ioctl
  0.11    0.000002           0         3           brk
  0.11    0.000002           1         2           pread64
  0.11    0.000002           2         1           arch_prctl
  0.06    0.000001           1         1           set_tid_address
  0.06    0.000001           1         1           set_robust_list
  0.06    0.000001           1         1           rseq
  0.00    0.000000           0         1           munmap
  0.00    0.000000           0         2         2 statfs
  0.00    0.000000           0         1           prlimit64
  0.00    0.000000           0         1           getrandom
------ ----------- ----------- --------- --------- ----------------
100.00    0.001800          25        71         4 total
```
Analisis
1. Sebutkan minimal 5 system call dari strace-summary.txt beserta fungsi singkatnya.
* execve: Berfungsi untuk mengeksekusi program. Ini adalah panggilan pertama yang dilakukan untuk menjalankan perintah ls.

* mmap: Berfungsi untuk memetakan file atau perangkat ke dalam memori virtual. Digunakan untuk memuat pustaka sistem (.so) agar program bisa berjalan.

* openat: Berfungsi untuk membuka file atau direktori. Dalam kasus ls, ini digunakan untuk membuka direktori yang ingin didaftar isinya.

* getdents64: Singkatan dari get directory entries. Berfungsi untuk membaca daftar nama file/direktori dari sebuah direktori yang sudah dibuka.

* write: Berfungsi untuk menulis data ke file descriptor. Dalam konteks ini, digunakan untuk menampilkan teks hasil ls ke layar (terminal).
2. System call mana yang paling sering dipanggil? Mengapa?
* mmap (17 kali). Hal ini dikarenakan setiap program Linux modern bergantung pada banyak pustaka bersama (shared libraries).
3. Apakah ada errors lebih dari 0? Apakah program tetap berjalan normal meskipun ada kegagalan tersebut?
* Terdapat total 4 error (2 pada access dan 2 pada statfs). hal ini normal sehingga program tetap berjalan normal.

## Tugas 10.5 Studi Kasus Diagnosa Server Lambat
1. buat file
```
nano ~/praktikum-os/week10-memory/diagnosa-server.sh
```
2. isi file
```
#!/bin/bash
set -euo pipefail

LAPORAN="diagnosa-server-lambat.txt"
WARN_MEM=false
WARN_SWAP=0

cek_memori() {
    echo "--- Kondisi Memori ---"
    free -h
    echo
    AVAIL_PCT=$(free | awk '/Mem/ {printf "%d", $7/$2*100}')
    if [ "$AVAIL_PCT" -lt 20 ]; then
        echo "PERINGATAN: Memori tersedia hanya ${AVAIL_PCT}%"
        WARN_MEM=true
    fi
}

cek_swap() {
    echo "--- Penggunaan Swap ---"
    swapon --show 2>/dev/null || echo "Tidak ada swap aktif"
    echo
    WARN_SWAP=$(free | awk '/Swap/ {print $3}')
    if [ "$WARN_SWAP" -gt 0 ]; then
        echo "INFO: Swap digunakan (${WARN_SWAP} kB)"
    fi
}

cek_proses() {
    echo "--- 10 Proses Memori Tertinggi ---"
    ps aux --sort=-%mem | head -n 11
    echo
}

cek_paging() {
    echo "--- Aktivitas Paging (5 sampel) ---"
    vmstat 1 5
    echo
}

ringkasan() {
    echo "=== RINGKASAN ==="
    if [ "$WARN_MEM" = true ]; then
        echo "- Memori: KRITIS - perlu tindakan segera"
    else
        echo "- Memori: normal"
    fi

    if [ "$WARN_SWAP" -gt 0 ]; then
        echo "- Swap: aktif - pantau aktivitas paging"
    else
        echo "- Swap: tidak digunakan"
    fi
}

{
    echo "=== LAPORAN DIAGNOSA SERVER ==="
    date
    echo

    cek_memori
    cek_swap
    cek_proses
    cek_paging
    ringkasan
} | tee "$LAPORAN"

echo
echo "Laporan disimpan ke: $LAPORAN"
```
3. ubah izin file lalu jalankan
```

ariel@ubuntuser:~/praktikum-os/week10-memory$ chmod +x diagnosa-server.sh
ariel@ubuntuser:~/praktikum-os/week10-memory$ bash diagnosa-server.sh
=== LAPORAN DIAGNOSA SERVER ===
Tue May 12 17:19:29 UTC 2026

--- Kondisi Memori ---
               total        used        free      shared  buff/cache   available
Mem:           3.3Gi       412Mi       1.8Gi       1.0Mi       1.4Gi       2.9Gi
Swap:          767Mi          0B       767Mi

--- Penggunaan Swap ---
NAME                   TYPE SIZE USED PRIO
/swapfile-week10       file 512M   0B   -2
/swapfile-tugas-week10 file 256M   0B   -3

--- 10 Proses Memori Tertinggi ---
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         374  0.0  0.7 288988 27324 ?        SLsl 13:14   0:07 /sbin/multipathd -d -s
root         690  0.0  0.6 109688 23068 ?        Ssl  13:14   0:00 /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal
root         324  0.0  0.4  66852 17376 ?        S<s  13:14   0:00 /usr/lib/systemd/systemd-journald
root         627  0.0  0.3 468972 13580 ?        Ssl  13:14   0:00 /usr/libexec/udisks2/udisksd
root           1  0.0  0.3  22548 13576 ?        Ss   13:14   0:06 /sbin/init splash noprompt noshell automatic-ubiquity
systemd+     436  0.0  0.3  21584 13060 ?        Ss   13:14   0:00 /usr/lib/systemd/systemd-resolved
root         704  0.0  0.3 392100 12816 ?        Ssl  13:14   0:00 /usr/sbin/ModemManager
ariel       1503  0.0  0.3  20088 11216 ?        Ss   13:41   0:00 /usr/lib/systemd/systemd --user
polkitd      610  0.0  0.2 383664  9952 ?        Ssl  13:14   0:01 /usr/lib/polkit-1/polkitd --no-debug
systemd+     480  0.0  0.2  19012  9588 ?        Ss   13:14   0:00 /usr/lib/systemd/systemd-networkd

--- Aktivitas Paging (5 sampel) ---
procs -----------memory---------- ---swap-- -----io---- -system-- -------cpu-------
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st gu
 1  0      0 1844396  38540 1402080    0    0    53   118 1600    0  0  4 95  0  0  0
 0  0      0 1844396  38540 1402124    0    0     0     0 1875  127  0  3 97  0  0  0
 0  0      0 1844396  38540 1402124    0    0     0     4 1814  117  0  3 97  0  0  0
 0  0      0 1844396  38540 1402124    0    0     0     0 1740   89  0  4 96  0  0  0
 1  0      0 1844396  38540 1402124    0    0     0     0 1683   37  0  3 97  0  0  0

=== RINGKASAN ===
- Memori: normal
- Swap: tidak digunakan

Laporan disimpan ke: diagnosa-server-lambat.txt
```
analisis
1. Jelaskan peran masing-masing fungsi: cek_memori, cek_swap, cek_proses, cek_paging, dan ringkasan. Mengapa diagnosa dipecah menjadi fungsi terpisah?
* cek_memori: Berfungsi untuk menampilkan penggunaan RAM secara keseluruhan melalui perintah free -h dan menghitung persentase memori yang tersedia untuk menentukan apakah memori dalam ambang batas aman.

* cek_swap: Memeriksa apakah ada partisi/file swap yang aktif dan mendeteksi seberapa besar kapasitas yang sedang digunakan.

* cek_proses: Mengurutkan dan menampilkan 10 proses teratas berdasarkan penggunaan memori (%MEM) untuk mengidentifikasi proses mana yang paling banyak mengonsumsi sumber daya.

* cek_paging: Memantau aktivitas keluar-masuk data dari RAM ke Swap secara real-time sebanyak 5 sampel menggunakan vmstat.

* ringkasan: Memberikan kesimpulan akhir berdasarkan variabel peringatan yang diset oleh fungsi-fungsi sebelumnya (seperti WARN_MEM), sehingga pengguna mendapatkan laporan cepat tanpa harus membaca semua detail teknis.

* fungsi dipisah untuk menjaga struktur kode tetap bersih(modular) sehingga mudah di pelihara.
2. Berdasarkan bagian RINGKASAN, apakah kondisi sistem normal atau kritis? Jelaskan berdasarkan nilai threshold yang digunakan script.
* kondisi sistem normal
* Threshold Memori: di atas 20% (sekitar 87%).
* Threshold Swap: penggunaan swap adalah 0B, sehingga statusnya tetap "tidak digunakan"
3. Mengapa script menggunakan tee "$LAPORAN" bukan redirection biasa > "$LAPORAN"? Apa keuntungannya?
* bisa langsung ditampilkan secara bersamaan di terminal.
4. Dari output cek_paging, apakah ada aktivitas si atau so? Jika ada, apa implikasinya terhadap performa server?
* Kolom si (swap-in) dan so (swap-out) semuanya bernilai 0 pada kelima sampel. Nilai 0 menunjukkan bahwa tidak ada aktivitas pemindahan data antara RAM dan Swap saat skrip dijalankan.