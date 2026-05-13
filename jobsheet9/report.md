# Laporan pertemuan ke -11 sistem operasi
**Tanggal:** 14 April 2026  
**Disusun Oleh:** Ariel Ardani Aris Putra  
**NIM:** 2541070200129  
**Kelas/No:** TI-1G/04

## Praktikum 9.1 — Permissions

1. Buat direktori kerja dan dua file uji.
```
ariel@ubuntuser:~$ mkdir ~/lab-permissions && cd ~/lab-permissions
ariel@ubuntuser:~/lab-permissions$ echo "data rahasia" > secret.txt
ariel@ubuntuser:~/lab-permissions$ echo '#!/bin/bash' > myscript.sh
ariel@ubuntuser:~/lab-permissions$ echo 'echo Hello' >> myscript.sh
ariel@ubuntuser:~/lab-permissions$ ls -la
ls -la
total 16
drwxr-xr-x 2 ariel ariel 4096 May 13 12:19 .
drwxr-x--- 9 ariel ariel 4096 May 13 12:18 ..
-rw-r--r-- 1 ariel ariel   23 May 13 12:19 myscript.sh
-rw-r--r-- 1 ariel ariel   13 May 13 12:19 secret.txt
ariel@ubuntuser:~/lab-permissions$
```
 2. Jadikan secret.txt privat hanya untuk owner.   
 ```
 ariel@ubuntuser:~/lab-permissions$ chmod 600 secret.txt
ariel@ubuntuser:~/lab-permissions$ ls -l secret.txt
-rw------- 1 ariel ariel 13 May 13 12:19 secret.txt
 ```
3. Jadikan myscript.sh dapat dijalankan.
```
ariel@ubuntuser:~/lab-permissions$ chmod 755 myscript.sh
ariel@ubuntuser:~/lab-permissions$ ls -l myscript.sh
-rwxr-xr-x 1 ariel ariel 23 May 13 12:19 myscript.sh
ariel@ubuntuser:~/lab-permissions$ ./myscript.sh
Hello
```
4. Buat direktori bersama dan amati efek SGID sederhana.
```
ariel@ubuntuser:~/lab-permissions$ mkdir shared-dir
ariel@ubuntuser:~/lab-permissions$ chmod g+s shared-dir
ariel@ubuntuser:~/lab-permissions$ ls -ld shared-dir
drwxr-sr-x 2 ariel 1000 4096 May 13 13:13 shared-dir
```
5.  Uji efek umask pada file baru.

```
ariel@ubuntuser:~/lab-permissions$ umask 027
ariel@ubuntuser:~/lab-permissions$ touch testfile-027
ariel@ubuntuser:~/lab-permissions$ ls -l testfile-027
-rw-r----- 1 ariel 1000 0 May 13 13:24 testfile-027
ariel@ubuntuser:~/lab-permissions$
```

Analisis
1. Mengapa secret.txt tidak dapat dibaca oleh group dan others setelah chmod 600?
* 600 memberikan hak baca-tulis hanya kepada Owner. Nilai 0 pada posisi group dan others berarti seluruh hak akses (read, write, execute) dicabut total dari mereka.
2. Apa perbedaan arti 600 dan 755 terhadap file yang diuji?

* 600: Fokus pada privasi (hanya owner yang bisa baca/edit).

* 755: Fokus pada distribusi/program (semua bisa baca dan menjalankan/eksekusi, tapi hanya owner yang bisa edit).
3. Setelah umask 027, permission apa yang dihasilkan untuk file baru, dan mengapa bukan 777?
* Permission file baru dihitung dari base 666 (bukan 777, karena file tidak boleh otomatis executable).

* Hitungan: 666 (Base) - 027 (Umask) = 640.
* Hasil: Owner (rw-), Group (r--), Others (---).
## Praktikum 9.2 — ACL
1. Siapkan file dan lihat permission standar tanpa ACL tambahan.
```
ariel@ubuntuser:~/lab-permissions$ mkdir ~/lab-acl && cd ~/lab-acl
ariel@ubuntuser:~/lab-acl$ echo "Data penting" > confidential.txt
ariel@ubuntuser:~/lab-acl$ chmod 640 confidential.txt
ariel@ubuntuser:~/lab-acl$ ls -l confidential.txt
-rw-r----- 1 ariel 1000 13 May 13 13:51 confidential.txt
ariel@ubuntuser:~/lab-acl$ getfacl confidential.txt
# file: confidential.txt
# owner: ariel
# group: 1000
user::rw-
group::r--
other::---
```
2.  Beri akses baca ke satu user tertentu tanpa mengubah owner atau group.
```
ariel@ubuntuser:~/lab-acl$ setfacl -m u:ariel:r confidential.txt
ariel@ubuntuser:~/lab-acl$ ls -l confidential.txt
-rw-r-----+ 1 ariel 1000 13 May 13 13:51 confidential.txt
ariel@ubuntuser:~/lab-acl$ getfacl confidential.txt
# file: confidential.txt
# owner: ariel
# group: 1000
user::rw-
user:ariel:r--
group::r--
mask::r--
other::---
```

3.  Buat direktori bersama yang mewariskan ACL ke file baru
```
ariel@ubuntuser:~/lab-acl$ mkdir shared
ariel@ubuntuser:~/lab-acl$ setfacl -d -m u:ariel:rwx shared
ariel@ubuntuser:~/lab-acl$ setfacl -d -m u:www-data:r-x shared
ariel@ubuntuser:~/lab-acl$ getfacl shared/
# file: shared/
# owner: ariel
# group: 1000
user::rwx
group::r-x
other::---
default:user::rwx
default:user:www-data:r-x
default:user:ariel:rwx
default:group::r-x
default:mask::rwx
default:other::---

ariel@ubuntuser:~/lab-acl$ touch shared/inherited.txt
ariel@ubuntuser:~/lab-acl$ getfacl shared/inherited.txt
# file: shared/inherited.txt
# owner: ariel
# group: 1000
user::rw-
user:www-data:r-x               #effective:r--
user:ariel:rwx                  #effective:rw-
group::r-x                      #effective:r--
mask::rw-
other::---

ariel@ubuntuser:~/lab-acl$
```

Analisis
1. Mengapa getfacl confidential.txt awalnya tidak menampilkan user tertentu?
* getfacl awalnya tidak menampilkan user tertentu karena file hanya menggunakan Linux Standard Permissions (UGO). Pada mode ini, izin hanya diatur untuk tiga entitas: User (owner), Group, dan Others.
2. Setelah setfacl -m u:userA:r confidential.txt, apa perbedaan output ls -l dan getfacl?
* ls -l: Muncul tanda plus (+) di akhir string permission (contoh: -rw-r-----+). Ini adalah indikator visual bahwa file tersebut memiliki Access Control List (ACL) aktif.

* getfacl: Menampilkan rincian spesifik siapa saja user tambahan yang diberi akses (misalnya user:ariel:r--), yang tidak bisa dilihat hanya dengan perintah ls -l.

3. Mengapa file inherited.txt mewarisi ACL dari direktori shared?
* karena setiap file di dalam direktori akan mewarisi ACL dari direktorinya.
## Praktikum 9.3A — Membuat dan Mengelola User
1.  membuat user baru, memodifikasi propertinya, dan memahami perbedaan opsi useradd dan usermod.
```
ariel@ubuntuser:~/lab-acl$ sudo useradd -m -s /bin/bash userA
[sudo] password for ariel:
ariel@ubuntuser:~/lab-acl$ sudo useradd -m -s /bin/bash userB
ariel@ubuntuser:~/lab-acl$ sudo passwd userA
New password:
Retype new password:
passwd: password updated successfully
ariel@ubuntuser:~/lab-acl$ sudo passwd userB
New password:
Retype new password:
passwd: password updated successfully
ariel@ubuntuser:~/lab-acl$ id userA
uid=1001(userA) gid=1002(userA) groups=1002(userA)
ariel@ubuntuser:~/lab-acl$ get passwd userA
Command 'get' not found, but there are 18 similar ones.
ariel@ubuntuser:~/lab-acl$ getent passwd userA
userA:x:1001:1002::/home/userA:/bin/bash
ariel@ubuntuser:~/lab-acl$ sudo usermod -s /bin/zsh userA
usermod: Warning: missing or non-executable shell '/bin/zsh'
usermod: no changes
ariel@ubuntuser:~/lab-acl$ sudo usermod -L userB
ariel@ubuntuser:~/lab-acl$ sudo passwd -S userB
userB L 2026-05-13 0 99999 7 -1
ariel@ubuntuser:~/lab-acl$ sudo usermod -U userB
ariel@ubuntuser:~/lab-acl$ sudo passwd -S userB
userB P 2026-05-13 0 99999 7 -1
```

Pertanyaan:
1. Apa perbedaan output id userA sebelum dan sesudah menambah group?
* Sebelum ditambah grup: Output hanya menunjukkan GID (Primary Group) yang biasanya sama dengan nama user tersebut.

* Sesudah ditambah grup: Pada bagian groups=..., akan muncul daftar ID dan nama grup tambahan di mana userA menjadi anggotanya. Ini menunjukkan bahwa user tersebut kini memiliki hak akses yang dimiliki oleh grup baru tersebut. 

2. Bagaimana status passwd -S userB berubah saat akun di-lock?
* Saat di-lock (sudo usermod -L):
Status menunjukkan huruf L (Locked). Secara teknis, sistem menambahkan tanda seru (!) di depan password terenkripsi di file /etc/shadow, sehingga user tidak bisa login meskipun passwordnya benar.

* Saat di-unlock (sudo usermod -U):
Status kembali menjadi P (Password set / Usable password). Ini menandakan akun telah aktif kembali dan bisa digunakan untuk login.

## Praktikum 9.3B — Group Management
1. membuat group, menambahkan user ke group, dan memverifikasi keanggotaan.
```
ariel@ubuntuser:~/lab-acl$ sudo groupadd labgroup
ariel@ubuntuser:~/lab-acl$ sudo groupadd readonly - group
Usage: groupadd [options] GROUP

Options:
  -f, --force                   exit successfully if the group already exists,
                                and cancel -g if the GID is already used
  -g, --gid GID                 use GID for the new group
  -h, --help                    display this help message and exit
  -K, --key KEY=VALUE           override /etc/login.defs defaults
  -o, --non-unique              allow to create groups with duplicate
                                (non-unique) GID
  -p, --password PASSWORD       use this encrypted password for the new group
  -r, --system                  create a system account
  -R, --root CHROOT_DIR         directory to chroot into
  -P, --prefix PREFIX_DI        directory prefix
  -U, --users USERS             list of user members of this group
      --extrausers              Use the extra users database

ariel@ubuntuser:~/lab-acl$ sudo usermod - aG labgroup , readonly - group userA
Usage: usermod [options] LOGIN

Options:
  -a, --append                  append the user to the supplemental GROUPS
                                mentioned by the -G option without removing
                                the user from other groups
  -b, --badname                 allow bad names
  -c, --comment COMMENT         new value of the GECOS field
  -d, --home HOME_DIR           new home directory for the user account
  -e, --expiredate EXPIRE_DATE  set account expiration date to EXPIRE_DATE
  -f, --inactive INACTIVE       set password inactive after expiration
                                to INACTIVE
  -g, --gid GROUP               force use GROUP as new primary group
  -G, --groups GROUPS           new list of supplementary GROUPS
  -h, --help                    display this help message and exit
  -l, --login NEW_LOGIN         new value of the login name
  -L, --lock                    lock the user account
  -m, --move-home               move contents of the home directory to the
                                new location (use only with -d)
  -o, --non-unique              allow using duplicate (non-unique) UID
  -p, --password PASSWORD       use encrypted password for the new password
  -P, --prefix PREFIX_DIR       prefix directory where are located the /etc/* files
  -r, --remove                  remove the user from only the supplemental GROUPS
                                mentioned by the -G option without removing
                                the user from other groups
  -R, --root CHROOT_DIR         directory to chroot into
  -s, --shell SHELL             new login shell for the user account
  -u, --uid UID                 new UID for the user account
  -U, --unlock                  unlock the user account
  -v, --add-subuids FIRST-LAST  add range of subordinate uids
  -V, --del-subuids FIRST-LAST  remove range of subordinate uids
  -w, --add-subgids FIRST-LAST  add range of subordinate gids
  -W, --del-subgids FIRST-LAST  remove range of subordinate gids
  -Z, --selinux-user SEUSER     new SELinux user mapping for the user account

ariel@ubuntuser:~/lab-acl$ sudo usermod - aG readonly - group userB
Usage: usermod [options] LOGIN

Options:
  -a, --append                  append the user to the supplemental GROUPS
                                mentioned by the -G option without removing
                                the user from other groups
  -b, --badname                 allow bad names
  -c, --comment COMMENT         new value of the GECOS field
  -d, --home HOME_DIR           new home directory for the user account
  -e, --expiredate EXPIRE_DATE  set account expiration date to EXPIRE_DATE
  -f, --inactive INACTIVE       set password inactive after expiration
                                to INACTIVE
  -g, --gid GROUP               force use GROUP as new primary group
  -G, --groups GROUPS           new list of supplementary GROUPS
  -h, --help                    display this help message and exit
  -l, --login NEW_LOGIN         new value of the login name
  -L, --lock                    lock the user account
  -m, --move-home               move contents of the home directory to the
                                new location (use only with -d)
  -o, --non-unique              allow using duplicate (non-unique) UID
  -p, --password PASSWORD       use encrypted password for the new password
  -P, --prefix PREFIX_DIR       prefix directory where are located the /etc/* files
  -r, --remove                  remove the user from only the supplemental GROUPS
                                mentioned by the -G option without removing
                                the user from other groups
  -R, --root CHROOT_DIR         directory to chroot into
  -s, --shell SHELL             new login shell for the user account
  -u, --uid UID                 new UID for the user account
  -U, --unlock                  unlock the user account
  -v, --add-subuids FIRST-LAST  add range of subordinate uids
  -V, --del-subuids FIRST-LAST  remove range of subordinate uids
  -w, --add-subgids FIRST-LAST  add range of subordinate gids
  -W, --del-subgids FIRST-LAST  remove range of subordinate gids
  -Z, --selinux-user SEUSER     new SELinux user mapping for the user account

ariel@ubuntuser:~/lab-acl$ id userA
uid=1001(userA) gid=1002(userA) groups=1002(userA)
ariel@ubuntuser:~/lab-acl$ id userB
uid=1002(userB) gid=1003(userB) groups=1003(userB)
ariel@ubuntuser:~/lab-acl$ getent group labgroup
labgroup:x:1004:
ariel@ubuntuser:~/lab-acl$ getent group readonly-group
```

Pertanyaan:
1. Apa yang ditampilkan id userA vs groups userA?
* id userA: Menampilkan informasi yang sangat detail dan teknis. Ini mencakup UID (User ID), GID (Primary Group ID), serta semua daftar grup tambahan (groups) beserta nomor ID-nya masing-masing.

* groups userA: Hanya menampilkan daftar nama grup di mana userA menjadi anggotanya dalam format yang lebih manusiawi dan ringkas, tanpa menyertakan nomor ID.

2. Mengapa -a pada usermod -aG penting?
* Tanpa -a: Perintah usermod -G akan menghapus user dari semua grup tambahan yang lama dan hanya memasukkannya ke grup yang baru disebutkan. Ini bisa berbahaya jika user tersebut sebelumnya punya akses penting di grup lain (seperti sudo).

* Dengan -a: Perintah ini akan menambahkan grup baru ke dalam daftar keanggotaan user yang sudah ada tanpa menghapus keanggotaan grup yang lama.
## Praktikum 9.3C — Password Aging Policy
1. menerapkan kebijakan umur password dan mengamati efeknya.
```
ariel@ubuntuser:~/lab-acl$ sudo chage -M 60 -W 7 -m 1 userA
ariel@ubuntuser:~/lab-acl$ sudo chage -l userA
Last password change                                    : May 13, 2026
Password expires                                        : Jul 12, 2026
Password inactive                                       : never
Account expires                                         : never
Minimum number of days between password change          : 1
Maximum number of days between password change          : 60
Number of days of warning before password expires       : 7
ariel@ubuntuser:~/lab-acl$ sudo chage -d 0 userA
ariel@ubuntuser:~/lab-acl$ sudo passwd -l userB
passwd: password changed.
ariel@ubuntuser:~/lab-acl$ sudo passwd -S userB
userB L 2026-05-13 0 99999 7 -1
ariel@ubuntuser:~/lab-acl$ sudo passwd -u userB
passwd: password changed.
ariel@ubuntuser:~/lab-acl$ sudo passwd -S userB
userB P 2026-05-13 0 99999 7 -1
```

Pertanyaan:
1. Apa arti nilai yang ditampilkan chage -l userA?

* Last password change: Tanggal terakhir user mengganti passwordnya (13 Mei 2026).

* Password expires: Tanggal di mana password akan kedaluwarsa dan wajib diganti (12 Juli 2026, tepat 60 hari setelah penggantian terakhir).

* Minimum number of days... (1): User tidak boleh mengganti password lebih dari sekali dalam 24 jam.

* Maximum number of days... (60): Batas maksimal password boleh digunakan sebelum harus diganti.

* Number of days of warning... (7): User akan mulai menerima notifikasi peringatan setiap kali login mulai 7 hari sebelum tanggal kedaluwarsa.

2. Bagaimana cara membuktikan userB terkunci dari output passwd -S?
* Jika muncul huruf L (Locked), berarti akun sedang terkunci.

* Jika muncul huruf P (Password set), berarti akun aktif dan password bisa digunakan.

3. Kapan sebaiknya menggunakan chage -d 0 vs passwd -e?
* chage -d 0: Mengatur tanggal penggantian password terakhir ke "0" (1 Januari 1970). Ini adalah cara manual yang lebih teknis untuk memicu kedaluwarsa seketika.

* passwd -e (expire): Cara yang lebih simpel dan deskriptif untuk segera "mengedaluwarsakan" password user.
## Praktikum 9.4 — Konfigurasi sudo
1. Buat file konfigurasi sudo khusus untuk userA.
```
sudo visudo -f /etc/sudoers.d/lab-userA
```
2. isi file
```
userA ALL=(root) NOPASSWD: /usr/bin/apt update, /usr/bin/apt upgrade
userA ALL=(root) /bin/systemctl status *
```

3.  Verifikasi aturan yang aktif dan uji hasilnya.
```
ariel@ubuntuser:~/lab-acl$ sudo -l -U userA
Matching Defaults entries for userA on ubuntuser:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty

User userA may run the following commands on ubuntuser:
    (root) NOPASSWD: /usr/bin/apt update, /usr/bin/apt upgrade
    (root) /bin/systemctl status *
ariel@ubuntuser:~/lab-acl$ sudo grep "userA" /var/log/auth.log | tail -10
2026-05-13T14:17:45.532617+00:00 ubuntuser usermod[2258]: change user 'userA' shell from '/bin/bash' to '/bin/zsh'
2026-05-13T14:18:13.556857+00:00 ubuntuser sudo:    ariel : TTY=pts/0 ; PWD=/home/ariel/lab-acl ; USER=root ; COMMAND=/usr/sbin/usermod -s /bin/zsh userA
2026-05-13T14:25:18.378307+00:00 ubuntuser sudo:    ariel : TTY=pts/0 ; PWD=/home/ariel/lab-acl ; USER=root ; COMMAND=/usr/sbin/usermod - aG labgroup , readonly - group userA
2026-05-13T14:32:08.014178+00:00 ubuntuser sudo:    ariel : TTY=pts/0 ; PWD=/home/ariel/lab-acl ; USER=root ; COMMAND=/usr/bin/chage -M 60 -W 7 -m 1 userA
2026-05-13T14:32:08.108671+00:00 ubuntuser chage[2343]: changed password expiry for userA
2026-05-13T14:32:13.225286+00:00 ubuntuser sudo:    ariel : TTY=pts/0 ; PWD=/home/ariel/lab-acl ; USER=root ; COMMAND=/usr/bin/chage -l userA
2026-05-13T14:32:19.666930+00:00 ubuntuser sudo:    ariel : TTY=pts/0 ; PWD=/home/ariel/lab-acl ; USER=root ; COMMAND=/usr/bin/chage -d 0 userA
2026-05-13T14:32:19.749099+00:00 ubuntuser chage[2354]: changed password expiry for userA
2026-05-13T14:38:15.213874+00:00 ubuntuser sudo:    ariel : TTY=pts/0 ; PWD=/home/ariel/lab-acl ; USER=root ; COMMAND=/usr/sbin/visudo -f /etc/sudoers.d/lab-userA
2026-05-13T14:39:11.280413+00:00 ubuntuser sudo:    ariel : TTY=pts/0 ; PWD=/home/ariel/lab-acl ; USER=root ; COMMAND=/usr/bin/grep userA /var/log/auth.log
ariel@ubuntuser:~/lab-acl$
```

Analisis
1. Mengapa aturan disimpan di /etc/sudoers.d//, bukan langsung di /etc/sudoers?
* Modularitas: bisa membuat file terpisah untuk setiap user atau proyek (seperti lab-userA).

* Keamanan saat Upgrade: File /etc/sudoers sering kali diperbarui saat pembaruan sistem (system update). Jika memodifikasi file utama secara langsung, perubahan berisiko terhapus atau menyebabkan konflik saat update.

* Menghindari Kesalahan Fatal: Mengisolasi aturan di file kecil mengurangi risiko kesalahan sintaksis yang dapat mengunci semua user (termasuk admin) dari akses sudo.

2. Mana perintah yang bisa dijalankan tanpa password, dan mana yang masih perlu autentikasi?
* Tanpa Password (NOPASSWD): /usr/bin/apt update, /usr/bin/apt upgrade
* Perlu Password (Password): /bin/systemctl status *

3. Informasi apa saja yang dicatat di log sudo?
* Waktu Eksekusi: Kapan perintah dijalankan (misal: 2026-05-13T14:32:08...).

* Identitas User: Siapa yang menjalankan perintah (ariel).

* Terminal (TTY): Lokasi terminal tempat perintah dipicu (pts/0).

* Direktori Kerja (PWD): Lokasi folder saat perintah dijalankan (/home/ariel/lab-acl).

* Target User: Perintah dijalankan sebagai user siapa (biasanya USER=root).

* Perintah (COMMAND): Jalur lengkap perintah yang dieksekusi (misal: /usr/sbin/visudo -f ...).
## Praktikum 9.5 — Disk Quota

1. Buat image filesystem kecil dan mount dengan opsi quota.
```
ariel@ubuntuser:~/lab-acl$ sudo dd if=/dev/zero of=/tmp/quota-test.img bs=1M count=100
100+0 records in
100+0 records out
104857600 bytes (105 MB, 100 MiB) copied, 1.17071 s, 89.6 MB/s
ariel@ubuntuser:~/lab-acl$ sudo mkfs.ext4 /tmp/quota-test.img
mke2fs 1.47.0 (5-Feb-2023)
Discarding device blocks: done
Creating filesystem with 25600 4k blocks and 25600 inodes

Allocating group tables: done
Writing inode tables: done
Creating journal (1024 blocks): done
Writing superblocks and filesystem accounting information: done
ariel@ubuntuser:~/lab-acl$ sudo mkdir -p /mnt/quota-test
ariel@ubuntuser:~/lab-acl$ sudo mount -o loop,usrquota,grpquota /tmp/quota-test.img /mnt/quota-test
```

2. Buat database quota dan aktifkan enforcement.
```
ariel@ubuntuser:~/lab-acl$ sudo quotacheck -cug /mnt/quota-test
ariel@ubuntuser:~/lab-acl$ sudo quotaon -v /mnt/quota-test
quotaon: Your kernel probably supports ext4 quota feature but you are using external quota files. Please switch your filesystem to use ext4 quota feature as external quota files on ext4 are deprecated.
/dev/loop0 [/mnt/quota-test]: group quotas turned on
/dev/loop0 [/mnt/quota-test]: user quotas turned on
ariel@ubuntuser:~/lab-acl$ sudo repquota /mnt/quota-test
*** Report for user quotas on device /dev/loop0
Block grace time: 7days; Inode grace time: 7days
                        Block limits                File limits
User            used    soft    hard  grace    used  soft  hard  grace
----------------------------------------------------------------------
root      --      20       0       0              2     0     0


ariel@ubuntuser:~/lab-acl$
```
3. Tetapkan quota untuk user uji 
```
ariel@ubuntuser:~/lab-acl$ sudo edquota -u userA
```
isi file
```
Disk quotas for user userA (uid 1001):
  Filesystem                   blocks       soft       hard     inodes     soft     hard
  /dev/loop0                        0       5120      10240          0    10240        0
```


```
ariel@ubuntuser:~/lab-acl$ sudo repquota /mnt/quota-test
*** Report for user quotas on device /dev/loop0
Block grace time: 7days; Inode grace time: 7days
                        Block limits                File limits
User            used    soft    hard  grace    used  soft  hard  grace
----------------------------------------------------------------------
root      --      20       0       0              2     0     0

```

4. Bersihkan lingkungan uji setelah selesai.
```
ariel@ubuntuser:~/lab-acl$ sudo quotaoff /mnt/quota-test
ariel@ubuntuser:~/lab-acl$ sudo umount /mnt/quota-test
ariel@ubuntuser:~/lab-acl$ sudo rm /tmp/quota-test.img
```

Analisis
1. Apa perbedaan soft limit dan hard limit saat quota mulai terlampaui?

* Soft Limit (Batas Peringatan): Jika user melewati batas ini (dalam contohmu 5 MB), sistem masih mengizinkan penyimpanan file, tetapi user akan mulai mendapatkan pesan peringatan. User memiliki waktu tertentu (Grace Period, standarnya 7 hari) untuk mengurangi penggunaan file sebelum sistem benar-benar memblokirnya.

* Hard Limit (Batas Mutlak): Jika user mencapai batas ini (dalam contohmu 10 MB), sistem akan langsung menolak proses penulisan data baru. User tidak akan bisa menambah file sama sekali meskipun durasi grace period masih ada.

2. Mengapa praktikum ini memakai loopback filesystem, bukan langsung /home/?
* Keamanan Data: Mengaktifkan kuota pada direktori aktif seperti /home berisiko menyebabkan gangguan jika terjadi kesalahan konfigurasi (misalnya user sistem terkunci).

* Kemudahan Pembersihan: Kamu tidak perlu mengubah struktur partisi asli harddisk kamu. Setelah praktikum selesai, kamu cukup menghapus satu file image tersebut untuk mengembalikan semuanya seperti semula.

* Kompatibilitas: Memungkinkan kamu bereksperimen dengan opsi mount khusus (usrquota, grpquota) tanpa harus melakukan restart atau mengubah file /etc/fstab utama.

3. Dari output repquota, informasi apa yang menunjukkan quota sudah aktif?
* Munculnya Baris Block/File Limits: Terlihat kolom soft dan hard yang sudah memiliki nilai numerik (bukan hanya angka 0), menunjukkan database kuota sedang memantau limit tersebut.

* Munculnya Informasi Grace Time: Di baris awal laporan terlihat Block grace time: 7days. Ini menandakan fitur penalti waktu untuk soft limit sudah berjalan.

* Daftar User Terdata: Munculnya root (dan nantinya userA setelah ada aktivitas file) dalam laporan membuktikan bahwa sistem sedang melakukan penghitungan (accounting) penggunaan blok data secara real-time pada perangkat /dev/loop0.
## 1.7 Latihan
## Latihan Latihan 9.A — Audit dan Kolaborasi

1. Temukan file SUID aktif dengan find / -perm -4000 -type f 2>/dev/null, lalu jelaskan tiga file yang  kenali beserta alasannya.
```
ariel@ubuntuser:~/lab-acl$ find / -perm -4000 -type f 2>/dev/null
/usr/bin/mount
/usr/bin/su
/usr/bin/chsh
/usr/bin/chfn
/usr/bin/sudo
/usr/bin/newgrp
/usr/bin/gpasswd
/usr/bin/umount
/usr/bin/passwd
/usr/bin/fusermount3
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
/usr/lib/polkit-1/polkit-agent-helper-1
```
3 file yang saya kenali
- /usr/bin/sudo. sudo adalah gerbang utama menuju hak akses admin.
- /usr/bin/mount. File ini digunakan untuk memasang sistem file atau perangkat  ke dalam struktur direktori Linux.
- /usr/bin/passwd. file utama yang digunakan oleh pengguna untuk mengubah kata sandi (password) akun mereka sendiri
2. Cari direktori world-writable dan tentukan mana yang valid dan mana yang berisiko.
```
ariel@ubuntuser:~/lab-acl$ find / -type d -perm -0002 -ls 2>/dev/null
      367      0 drwxrwxrwt   2 root     root           40 May 13 12:10 /dev/mqueue
        1      0 drwxrwxrwt   2 root     root           40 May 13 12:10 /dev/shm
  1179649      4 drwxrwxrwt  13 root     root         4096 May 13 15:16 /tmp
  1179652      4 drwxrwxrwt   2 root     root         4096 May 13 12:10 /tmp/.ICE-unix
  1179653      4 drwxrwxrwt   2 root     root         4096 May 13 12:10 /tmp/.XIM-unix
  1179654      4 drwxrwxrwt   2 root     root         4096 May 13 12:10 /tmp/.font-unix
  1179651      4 drwxrwxrwt   2 root     root         4096 May 13 12:10 /tmp/.X11-unix
  1966094      4 drwxrwxrwt   8 root     root         4096 May 13 15:16 /var/tmp
  1966086      4 drwxrwsrwt   2 root     root         4096 Aug  5  2025 /var/crash
      619      0 drwxrwxrwt   2 root     utmp           40 May 13 12:10 /run/screen
        1      0 drwxrwxrwt   4 root     root           80 May 13 12:10 /run/lock
```

- valid:
* * /tmp dan /var/tmp
* * /dev/shm dan /dev/mqueue
* * /run/lock dan /run/screen
* * /tmp/.X11-unix
-  beresiko: 
* * /var/crash

3. Rancang konfigurasi permission standar dan ACL untuk direktori proyek /srv/webapp/ agar group webapp-team dapat menulis, user deploy hanya membaca, dan file baru selalu mewarisi group proyek.

## Latihan Latihan 9.B — Kebijakan Akun dan Quota

Tuliskan langkah untuk membuat user intern, menambahkannya ke group labgroup, memaksa pergantian password tiap 45 hari (warning 7 hari), memberi izin sudo hanya untuk systemctl status, dan menetapkan quota ruang serta inode sederhana pada /home/.
1. Membuat User dan Grup
```
ariel@ubuntuser:~/lab-acl$ sudo useradd -m -s /bin/bash intern
[sudo] password for ariel:
ariel@ubuntuser:~/lab-acl$ sudo usermod -aG labgroup intern
```

2. Konfigurasi Kebijakan Password (Aging Policy)
```
ariel@ubuntuser:~/lab-acl$ sudo chage -M 45 -W 7 intern
ariel@ubuntuser:~/lab-acl$ sudo chage -l intern
Last password change                                    : May 13, 2026
Password expires                                        : Jun 27, 2026
Password inactive                                       : never
Account expires                                         : never
Minimum number of days between password change          : 0
Maximum number of days between password change          : 45
Number of days of warning before password expires       : 7
```

3. Konfigurasi Hak Akses Sudo Terbatas
```
ariel@ubuntuser:~/lab-acl$ sudo visudo -f /etc/sudoers.d/lab-intern
```
isi file
```
intern ALL=(root) /bin/systemctl status *
```

4. Menetapkan Quota Ruang dan Inode
```
ariel@ubuntuser:~/lab-acl$ sudo edquota -u intern
```
isi quota
```
Disk quotas for user intern (uid 1003):
  Filesystem                   blocks       soft       hard     inodes     >
<        4        1000        1200
```

5. Verifikasi Akhir
```
ariel@ubuntuser:~/lab-acl$ sudo repquota /
*** Report for user quotas on device /dev/sda2
Block grace time: 7days; Inode grace time: 7days
                        Block limits                File limits
User            used    soft    hard  grace    used  soft  hard  grace
----------------------------------------------------------------------
root      -- 4696920       0       0         141137     0     0
man       --    2060       0       0            164     0     0
_apt      --      36       0       0              6     0     0
systemd-network --      16       0       0              4     0     0
systemd-timesync --       4       0       0              2     0     0      
messagebus --       4       0       0              1     0     0
pollinate --       4       0       0              2     0     0
syslog    --    2928       0       0             17     0     0
uuidd     --       4       0       0              1     0     0
tss       --       4       0       0              1     0     0
landscape --      12       0       0              5     0     0
fwupd-refresh --       4       0       0              1     0     0
ariel     --     376       0       0            132     0     0
userA     --      16       0       0              4     0     0
userB     --      16       0       0              4     0     0
intern    --      16   51200  102400              4  1000  1200


ariel@ubuntuser:~/lab-acl$
```
