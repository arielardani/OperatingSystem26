# Laporan pertemuan ke -11 sistem operasi
**Tanggal:** 20 Mei 2026  
**Disusun Oleh:** Ariel Ardani Aris Putra  
**NIM:** 2541070200129  
**Kelas/No:** TI-1G/04

## Praktek 10.1: Amati Layanan Aktif Saat Boot
1. Lihat semua layanan yang sedang berjalan.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ systemctl list-units --type=service --state=running
  UNIT                           LOAD   ACTIVE SUB     DESCRIPTION         >
  cron.service                   loaded active running Regular background p>
  dbus.service                   loaded active running D-Bus System Message>
  getty@tty1.service             loaded active running Getty on tty1
  ModemManager.service           loaded active running Modem Manager
  multipathd.service             loaded active running Device-Mapper Multip>
  polkit.service                 loaded active running Authorization Manager
  rsyslog.service                loaded active running System Logging Servi>
  ssh.service                    loaded active running OpenBSD Secure Shell>
  systemd-journald.service       loaded active running Journal Service
  systemd-logind.service         loaded active running User Login Management
  systemd-networkd.service       loaded active running Network Configuration
  systemd-resolved.service       loaded active running Network Name Resolut>
  systemd-udevd.service          loaded active running Rule-based Manager f>
  udisks2.service                loaded active running Disk Manager
  unattended-upgrades.service    loaded active running Unattended Upgrades >
  user@1000.service              loaded active running User Manager for UID>
  virtualbox-guest-utils.service loaded active running Virtualbox guest uti>

Legend: LOAD   → Reflects whether the unit definition was properly loaded.
        ACTIVE → The high-level unit activation state, i.e. generalization >
        SUB    → The low-level unit activation state, values depend on unit>

17 loaded units listed.
```
2. Lihat semua unit service yang ada (aktif maupun tidak).
```
ariel@ubuntuser:~/lab-os/chapter10-services$ systemctl list-unit-files --type=service | head -30
UNIT FILE                                    STATE           PRESET
apparmor.service                             enabled         enabled
apport-autoreport.service                    static          -
apport-coredump-hook@.service                static          -
apport-forward@.service                      static          -
apport.service                               enabled         enabled
apt-daily-upgrade.service                    static          -
apt-daily.service                            static          -
apt-news.service                             static          -
autovt@.service                              alias           -
blk-availability.service                     enabled         enabled
bolt.service                                 static          -
cloud-config.service                         enabled         enabled
cloud-final.service                          enabled         enabled
cloud-init-hotplugd.service                  static          -
cloud-init-local.service                     enabled         enabled
cloud-init.service                           enabled         enabled
console-getty.service                        disabled        disabled
console-setup.service                        enabled         enabled
container-getty@.service                     static          -
cron.service                                 enabled         enabled
cryptdisks-early.service                     masked          enabled
cryptdisks.service                           masked          enabled
dbus-org.freedesktop.hostname1.service       alias           -
dbus-org.freedesktop.locale1.service         alias           -
dbus-org.freedesktop.login1.service          alias           -
dbus-org.freedesktop.ModemManager1.service   alias           -
dbus-org.freedesktop.resolve1.service        alias           -
dbus-org.freedesktop.thermald.service        alias           -
dbus-org.freedesktop.timedate1.service       alias           -
```

3. Analisis waktu boot dan temukan layanan paling lambat.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ systemd-analyze
Startup finished in 8.943s (kernel) + 5.771s (userspace) = 14.714s
graphical.target reached after 5.725s in userspace.
ariel@ubuntuser:~/lab-os/chapter10-services$ systemd-analyze blame | head -15
13.265s apt-daily-upgrade.service
 3.483s dev-sda2.device
 2.676s snapd.seeded.service
 2.408s snapd.service
 2.007s systemd-networkd-wait-online.service
 1.737s dpkg-db-backup.service
 1.543s polkit.service
 1.452s grub-common.service
 1.384s rsyslog.service
 1.251s apparmor.service
 1.215s apport.service
 1.178s dbus.service
 1.163s systemd-networkd.service
 1.148s e2scrub_reap.service
 1.103s udisks2.service
```

## Praktek 10.2: Kelola Layanan SSH
1. Periksa status SSH secara menyeluruh.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ systemctl status ssh
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/usr/lib/systemd/system/ssh.service; enabled; preset: >
     Active: active (running) since Wed 2026-05-20 14:59:51 UTC; 20min ago
TriggeredBy: ● ssh.socket
       Docs: man:sshd(8)
             man:sshd_config(5)
    Process: 889 ExecStartPre=/usr/sbin/sshd -t (code=exited, status=0/SUCC>
   Main PID: 937 (sshd)
      Tasks: 6 (limit: 4008)
     Memory: 9.0M (peak: 20.2M)
        CPU: 9.504s
     CGroup: /system.slice/ssh.service
             ├─ 937 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startup>
             ├─1169 "sshd: ariel [priv]"
             ├─1172 "sshd: ariel@pts/0"
             ├─1173 -bash
             ├─1355 systemctl status ssh
             └─1356 less

Warning: some journal files were not opened due to insufficient permissions.
ariel@ubuntuser:~/lab-os/chapter10-services$ systemctl is-active ssh
active
ariel@ubuntuser:~/lab-os/chapter10-services$ systemctl is-enabled ssh
enabled
```
2. Lakukan restart dan pantau perubahannya.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo systemctl restart ssh
[sudo] password for ariel:
ariel@ubuntuser:~/lab-os/chapter10-services$ systemctl status ssh
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/usr/lib/systemd/system/ssh.service; enabled; preset: >
     Active: active (running) since Wed 2026-05-20 15:21:39 UTC; 13s ago
TriggeredBy: ● ssh.socket
       Docs: man:sshd(8)
             man:sshd_config(5)
    Process: 1367 ExecStartPre=/usr/sbin/sshd -t (code=exited, status=0/SUC>
   Main PID: 1369 (sshd)
      Tasks: 6 (limit: 4008)
     Memory: 14.1M (peak: 30.2M)
        CPU: 1.234s
     CGroup: /system.slice/ssh.service
             ├─1169 "sshd: ariel [priv]"
             ├─1172 "sshd: ariel@pts/0"
             ├─1173 -bash
             ├─1369 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startup>
             ├─1402 systemctl status ssh
             └─1403 less

May 20 15:21:39 ubuntuser sudo[1362]: pam_unix(sudo:session): session close>
```
3. Lihat dependensi SSH.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ systemctl list-dependencies ssh
ssh.service
● ├─-.mount
● ├─ssh.socket
● ├─system.slice
● └─sysinit.target
●   ├─apparmor.service
●   ├─blk-availability.service
●   ├─dev-hugepages.mount
●   ├─dev-mqueue.mount
●   ├─finalrd.service
●   ├─keyboard-setup.service
●   ├─kmod-static-nodes.service
○   ├─ldconfig.service
●   ├─lvm2-lvmpolld.socket
●   ├─lvm2-monitor.service
●   ├─multipathd.service
○   ├─open-iscsi.service
●   ├─plymouth-read-write.service
●   ├─plymouth-start.service
●   ├─proc-sys-fs-binfmt_misc.automount
●   ├─setvtrgb.service
●   ├─sys-fs-fuse-connections.mount
●   ├─sys-kernel-config.mount
●   ├─sys-kernel-debug.mount
●   ├─sys-kernel-tracing.mount
○   ├─systemd-ask-password-console.path
●   ├─systemd-binfmt.service
○   ├─systemd-firstboot.service
○   ├─systemd-hwdb-update.service
○   ├─systemd-journal-catalog-update.service
●   ├─systemd-journal-flush.service
●   ├─systemd-journald.service
○   ├─systemd-machine-id-commit.service
●   ├─systemd-modules-load.service
○   ├─systemd-pcrmachine.service
○   ├─systemd-pcrphase-sysinit.service
○   ├─systemd-pcrphase.service
○   ├─systemd-pstore.service
●   ├─systemd-random-seed.service
○   ├─systemd-repart.service
●   ├─systemd-resolved.service
●   ├─systemd-sysctl.service
```
4. Cek semua unit yang gagal di sistem.4
```
ariel@ubuntuser:~/lab-os/chapter10-services$ systemctl --failed
  UNIT LOAD ACTIVE SUB DESCRIPTION

0 loaded units listed.
```
## Praktek 10.3: Buat Layanan Sederhana dari Skrip Bash
1. Siapkan konten yang akan dilayani.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ mkdir -p situs-demo
ariel@ubuntuser:~/lab-os/chapter10-services$ nano situs-demo/index.html
```
isi file:
```
<!DOCTYPE html>
<html>
<body>
<h1>Halo dari layanan systemd kustom!</h1>
<p>Layanan ini dibuat pada praktek Bab 10.</p>
</body>
</html>
```
2. Buat skrip wrapper untuk server HTTP.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ nano ~/lab-os/chapter10-services/jalankan-server.sh
```
isi file:
```
#!/bin/bash
DIREKTORI="$HOME/lab-os/chapter10-services/situs-demo"
PORT=9090
echo "Memulai server di port $PORT..."
exec python3 -m http.server $PORT --directory "$DIREKTORI"
chmod +x ~/lab-os/chapter10-services/jalankan-server.sh
```
3. Buat berkas unit systemd untuk layanan ini.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ nano ~/lab-os/chapter10-services/demo-web.service
```
isi file:
```
[Unit]
Description=Demo Web Server Praktek Bab 10
After=network.target

[Service]
Type=simple
User=nama-pengguna-kamu
WorkingDirectory=/home/nama-pengguna-kamu/lab-os/chapter10-services/situs-demo
ExecStart=/usr/bin/python3 -m http.server 9090
Restart=on-failure
RestartSec=3s

[Install]
WantedBy=multi-user.target
```
salin ke lokasi unit systemd dan minta systemd membaca ulang berkas unit yang baru dibuat
```
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo cp ~/lab-os/chapter10-services/demo-web.service /etc/systemd/system/demo-web.service
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo systemctl daemon-reload
```
4. Jalankan layanan dan verifikasi.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo systemctl start demo-web
ariel@ubuntuser:~/lab-os/chapter10-services$ systemctl status demo-web
● demo-web.service - Demo Web Server Praktek Bab 10
     Loaded: loaded (/etc/systemd/system/demo-web.service; disabled; preset>
     Active: activating (auto-restart) (Result: exit-code) since Wed 2026-0>
    Process: 1498 ExecStart=/usr/bin/python3 -m http.server 9090 (code=exit>
   Main PID: 1498 (code=exited, status=217/USER)
        CPU: 2ms
ariel@ubuntuser:~/lab-os/chapter10-services$ curl http://localhost:9090
curl: (7) Failed to connect to localhost port 9090 after 2 ms: Couldn't connect to server
```
5. Uji fitur restart otomatis.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ systemctl status demo-web | grep "Main PID"
   Main PID: 1508 (code=exited, status=217/USER)
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo kill -9 $(systemctl show demo-web --property=MainPID --value)
Killed
ariel@ubuntuser:~/lab-os/chapter10-services$ sleep 5
ariel@ubuntuser:~/lab-os/chapter10-services$ systemctl status demo-web
● demo-web.service - Demo Web Server Praktek Bab 10
     Loaded: loaded (/etc/systemd/system/demo-web.service; disabled; preset>
     Active: activating (auto-restart) (Result: exit-code) since Wed 2026-0>
    Process: 1524 ExecStart=/usr/bin/python3 -m http.server 9090 (code=exit>
   Main PID: 1524 (code=exited, status=217/USER)
        CPU: 20ms
```
6. Bersihkan layanan uji setelah selesai.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo systemctl disable --now demo-web
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo rm /etc/systemd/system/demo-web.service
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo systemctl daemon-reload
```
## Praktek 10.4: Filter dan Analisis Log Layanan
1. Lihat log SSH dari satu jam terakhir.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ journalctl -u ssh --since "1 hour ago" --no-pager
Hint: You are currently not seeing messages from other users and the system.
      Users in groups 'adm', 'systemd-journal' can see all messages.
      Pass -q to turn off this notice.
May 20 15:21:39 ubuntuser sudo[1362]:    ariel : TTY=pts/0 ; PWD=/home/ariel/lab-os/chapter10-services ; USER=root ; COMMAND=/usr/bin/systemctl restart ssh
May 20 15:21:39 ubuntuser sudo[1362]: pam_unix(sudo:session): session opened for user root(uid=0) by ariel(uid=1000)
May 20 15:21:39 ubuntuser sudo[1362]: pam_unix(sudo:session): session closed for user root
May 20 15:34:12 ubuntuser sudo[1433]:    ariel : TTY=pts/0 ; PWD=/home/ariel/lab-os/chapter10-services ; USER=root ; COMMAND=/usr/bin/cp /home/ariel/lab-os/chapter10-services/demo-web.service /etc/systemd/system/demo-web.service
May 20 15:34:12 ubuntuser sudo[1433]: pam_unix(sudo:session): session opened for user root(uid=0) by ariel(uid=1000)
May 20 15:34:12 ubuntuser sudo[1433]: pam_unix(sudo:session): session closed for user root
May 20 15:34:22 ubuntuser sudo[1436]:    ariel : TTY=pts/0 ; PWD=/home/ariel/lab-os/chapter10-services ; USER=root ; COMMAND=/usr/bin/systemctl daemon-reload
May 20 15:34:22 ubuntuser sudo[1436]: pam_unix(sudo:session): session opened for user root(uid=0) by ariel(uid=1000)
May 20 15:34:23 ubuntuser sudo[1436]: pam_unix(sudo:session): session closed for user root
May 20 15:36:20 ubuntuser sudo[1493]:    ariel : TTY=pts/0 ; PWD=/home/ariel/lab-os/chapter10-services ; USER=root ; COMMAND=/usr/bin/systemctl start demo-web
May 20 15:36:20 ubuntuser sudo[1493]: pam_unix(sudo:session): session opened for user root(uid=0) by ariel(uid=1000)
May 20 15:36:20 ubuntuser sudo[1493]: pam_unix(sudo:session): session closed for user root
May 20 15:36:54 ubuntuser sudo[1516]:    ariel : TTY=pts/0 ; PWD=/home/ariel/lab-os/chapter10-services ; USER=root ; COMMAND=/usr/bin/kill -9 0
May 20 15:36:54 ubuntuser sudo[1516]: pam_unix(sudo:session): session opened for user root(uid=0) by ariel(uid=1000)
May 20 15:36:54 ubuntuser sudo[1516]: pam_unix(sudo:session): session closed for user root
May 20 15:37:18 ubuntuser sudo[1530]:    ariel : TTY=pts/0 ; PWD=/home/ariel/lab-os/chapter10-services ; USER=root ; COMMAND=/usr/bin/systemctl disable --now demo-web
May 20 15:37:18 ubuntuser sudo[1530]: pam_unix(sudo:session): session opened for user root(uid=0) by ariel(uid=1000)
May 20 15:37:19 ubuntuser sudo[1530]: pam_unix(sudo:session): session closed for user root
May 20 15:37:25 ubuntuser sudo[1577]:    ariel : TTY=pts/0 ; PWD=/home/ariel/lab-os/chapter10-services ; USER=root ; COMMAND=/usr/bin/rm /etc/systemd/system/demo-web.service
May 20 15:37:25 ubuntuser sudo[1577]: pam_unix(sudo:session): session opened for user root(uid=0) by ariel(uid=1000)
May 20 15:37:25 ubuntuser sudo[1577]: pam_unix(sudo:session): session closed for user root
May 20 15:37:30 ubuntuser sudo[1580]:    ariel : TTY=pts/0 ; PWD=/home/ariel/lab-os/chapter10-services ; USER=root ; COMMAND=/usr/bin/systemctl daemon-reload
May 20 15:37:30 ubuntuser sudo[1580]: pam_unix(sudo:session): session opened for user root(uid=0) by ariel(uid=1000)
May 20 15:37:30 ubuntuser sudo[1580]: pam_unix(sudo:session): session closed for user root
ariel@ubuntuser:~/lab-os/chapter10-services$ journalctl -u cron --since "1 hour ago" --no-pager
Hint: You are currently not seeing messages from other users and the system.
      Users in groups 'adm', 'systemd-journal' can see all messages.
      Pass -q to turn off this notice.
-- No entries --
```
2. Filter log berprioritas error ke atas.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ journalctl -b -p err --no-pager
Hint: You are currently not seeing messages from other users and the system.
      Users in groups 'adm', 'systemd-journal' can see all messages.
      Pass -q to turn off this notice.
-- No entries --
```
3. Ikuti log secara real-time sambil memicu aktivitas.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ journalctl -u ssh -f
Hint: You are currently not seeing messages from other users and the system.
      Users in groups 'adm', 'systemd-journal' can see all messages.
      Pass -q to turn off this notice.
May 20 15:36:54 ubuntuser sudo[1516]: pam_unix(sudo:session): session closed for user root
May 20 15:37:18 ubuntuser sudo[1530]:    ariel : TTY=pts/0 ; PWD=/home/ariel/lab-os/chapter10-services ; USER=root ; COMMAND=/usr/bin/systemctl disable --now demo-web
May 20 15:37:18 ubuntuser sudo[1530]: pam_unix(sudo:session): session opened for user root(uid=0) by ariel(uid=1000)
May 20 15:37:19 ubuntuser sudo[1530]: pam_unix(sudo:session): session closed for user root
May 20 15:37:25 ubuntuser sudo[1577]:    ariel : TTY=pts/0 ; PWD=/home/ariel/lab-os/chapter10-services ; USER=root ; COMMAND=/usr/bin/rm /etc/systemd/system/demo-web.service
May 20 15:37:25 ubuntuser sudo[1577]: pam_unix(sudo:session): session opened for user root(uid=0) by ariel(uid=1000)
May 20 15:37:25 ubuntuser sudo[1577]: pam_unix(sudo:session): session closed for user root
May 20 15:37:30 ubuntuser sudo[1580]:    ariel : TTY=pts/0 ; PWD=/home/ariel/lab-os/chapter10-services ; USER=root ; COMMAND=/usr/bin/systemctl daemon-reload
May 20 15:37:30 ubuntuser sudo[1580]: pam_unix(sudo:session): session opened for user root(uid=0) by ariel(uid=1000)
May 20 15:37:30 ubuntuser sudo[1580]: pam_unix(sudo:session): session closed for user root
```
4. Ekstrak log ke berkas untuk analisis.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ cd ~/lab-os/chapter10-services
ariel@ubuntuser:~/lab-os/chapter10-services$ journalctl -u ssh --since today --no-pager > log-ssh-hari-ini.txt
Hint: You are currently not seeing messages from other users and the system.
      Users in groups 'adm', 'systemd-journal' can see all messages.
      Pass -q to turn off this notice.
ariel@ubuntuser:~/lab-os/chapter10-services$ wc -l log-ssh-hari-ini.txt
24 log-ssh-hari-ini.txt
ariel@ubuntuser:~/lab-os/chapter10-services$ grep -i "error\|failed" log-ssh-hari-ini.txt | head -20
```
## Praktek 10.5: Konfigurasi SSH Server
1. Periksa konfigurasi SSH saat ini.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo grep -n "^Port\|^#Port" /etc/ssh/sshd_config
1:Port 22
ariel@ubuntuser:~/lab-os/chapter10-services$ ss -tlnp | grep ssh
```
2. Buat backup dan ubah port SSH.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup.lab12
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo sed -i 's/^#Port 22/Port 2222/' /etc/ssh/sshd_config
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo sed -i 's/^Port 22/Port 2222/' /etc/ssh/sshd_config
ariel@ubuntuser:~/lab-os/chapter10-services$ grep "^Port" /etc/ssh/sshd_config
Port 2222
```
3. Validasi konfigurasi dan restart layanan.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo sshd -t
ariel@ubuntuser:~/lab-os/chapter10-services$ echo "Kode keluar sshd -t: $?"
Kode keluar sshd -t: 0
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo systemctl restart ssh
ariel@ubuntuser:~/lab-os/chapter10-services$ systemctl status ssh
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/usr/lib/systemd/system/ssh.service; enabled; preset: >
     Active: active (running) since Wed 2026-05-20 15:49:59 UTC; 8s ago
TriggeredBy: ● ssh.socket
       Docs: man:sshd(8)
             man:sshd_config(5)
    Process: 2118 ExecStartPre=/usr/sbin/sshd -t (code=exited, status=0/SUC>
   Main PID: 2120 (sshd)
      Tasks: 6 (limit: 4008)
     Memory: 51.1M (peak: 53.6M)
        CPU: 230ms
     CGroup: /system.slice/ssh.service
             ├─1169 "sshd: ariel [priv]"
             ├─1172 "sshd: ariel@pts/0"
             ├─1173 -bash
             ├─2120 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startup>
             ├─2121 systemctl status ssh
             └─2122 less

May 20 15:49:59 ubuntuser sudo[2113]: pam_unix(sudo:session): session close>
```
4. Verifikasi port baru dengan ss.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ ss -tlnp | grep ssh
ariel@ubuntuser:~/lab-os/chapter10-services$ ss -tlnp | grep ssh > ~/lab-os/chapter10-services/bukti-port-ssh.txt
ariel@ubuntuser:~/lab-os/chapter10-services$ cat ~/lab-os/chapter10-services/bukti-port-ssh.txt
```
5. Kembalikan port SSH ke 22 setelah praktek.
```
ariel@ubuntuser:~$ sudo cp /etc/ssh/sshd_config.backup.lab12 /etc/ssh/sshd_config
[sudo] password for ariel:
ariel@ubuntuser:~$ sudo sshd -t
ariel@ubuntuser:~$ sudo systemctl restart ssh
ariel@ubuntuser:~$ ss -tlnp | grep ssh
```
ini kenapa output punya saya tidak muncul. pas saya coba fix dengan bertanya kepada ai saya diminta install dan update pada openssh-server namun pada saat update keluar output 
```
Waiting for cache lock: Could not get lock /var/lib/dpkg/lock-frontend. It is held by process 2011 (unattended-upgr)... Waiting for cache lock: Could not get lock /var/lib/dpkg/lock-frontend. It is held by process 2011 (unattended-upgr)    
```
## 1.7 Latihan
## Latihan 10.1 Audit Layanan dan Analisis Boot
Lakukan audit menyeluruh terhadap layanan yang berjalan di sistem.
1. Jalankan systemctl list-units –type=service –state=running dan catat semua layanan aktif. Pilih tiga layanan yang kamu kenal, periksa status masing-masing dengan systemctl status, dan jelaskan fungsinya.
output:
```
ariel@ubuntuser:~$ systemctl list-units --type=service --state=running
  UNIT                           LOAD   ACTIVE SUB     DESCRIPTION
  cron.service                   loaded active running Regular background program processing daemon
  dbus.service                   loaded active running D-Bus System Message Bus
  fwupd.service                  loaded active running Firmware update daemon
  getty@tty1.service             loaded active running Getty on tty1
  ModemManager.service           loaded active running Modem Manager
  multipathd.service             loaded active running Device-Mapper Multipath Device Controller
  packagekit.service             loaded active running PackageKit Daemon
  polkit.service                 loaded active running Authorization Manager
  rsyslog.service                loaded active running System Logging Service
  ssh.service                    loaded active running OpenBSD Secure Shell server
  systemd-journald.service       loaded active running Journal Service
  systemd-logind.service         loaded active running User Login Management
  systemd-networkd.service       loaded active running Network Configuration
  systemd-resolved.service       loaded active running Network Name Resolution
  systemd-udevd.service          loaded active running Rule-based Manager for Device Events and Files
  udisks2.service                loaded active running Disk Manager
  unattended-upgrades.service    loaded active running Unattended Upgrades Shutdown
  upower.service                 loaded active running Daemon for power management
  user@1000.service              loaded active running User Manager for UID 1000
  virtualbox-guest-utils.service loaded active running Virtualbox guest utils

Legend: LOAD   → Reflects whether the unit definition was properly loaded.
        ACTIVE → The high-level unit activation state, i.e. generalization of SUB.
        SUB    → The low-level unit activation state, values depend on unit type.

20 loaded units listed.
```

a. ssh.service (OpenBSD Secure Shell server)   
Perintah Cek: systemctl status ssh
output:
```
ariel@ubuntuser:~$ systemctl status ssh
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/usr/lib/systemd/system/ssh.service; enabled; preset: enabled)
     Active: active (running) since Wed 2026-05-20 16:15:23 UTC; 4min 59s ago
TriggeredBy: ● ssh.socket
       Docs: man:sshd(8)
             man:sshd_config(5)
    Process: 2499 ExecStartPre=/usr/sbin/sshd -t (code=exited, status=0/SUCCESS)
   Main PID: 2500 (sshd)
      Tasks: 6 (limit: 4008)
     Memory: 176.0M (peak: 256.2M)
        CPU: 2.518s
     CGroup: /system.slice/ssh.service
             ├─2472 "sshd: ariel [priv]"
             ├─2474 "sshd: ariel@pts/0"
             ├─2475 -bash
             ├─2500 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups"
             ├─2794 systemctl status ssh
             └─2795 less

May 20 16:15:23 ubuntuser sudo[2494]: pam_unix(sudo:session): session closed for user root
May 20 16:16:04 ubuntuser sudo[2504]:    ariel : TTY=pts/0 ; PWD=/home/ariel ; USER=root ; COMMAND=/usr/bin/apt update
May 20 16:16:04 ubuntuser sudo[2504]: pam_unix(sudo:session): session opened for user root(uid=0) by ariel(uid=1000)
May 20 16:16:07 ubuntuser sudo[2504]: pam_unix(sudo:session): session closed for user root
May 20 16:16:07 ubuntuser sudo[2707]:    ariel : TTY=pts/0 ; PWD=/home/ariel ; USER=root ; COMMAND=/usr/bin/apt install>
May 20 16:16:07 ubuntuser sudo[2707]: pam_unix(sudo:session): session opened for user root(uid=0) by ariel(uid=1000)
May 20 16:16:18 ubuntuser sudo[2707]: pam_unix(sudo:session): session closed for user root
```
Fungsi: Menyediakan jalur komunikasi terenkripsi dan aman untuk mengontrol atau meremote Ubuntu Server dari jarak jauh melalui jaringan. Karena kamu menggunakan VirtualBox (virtualbox-guest-utils aktif), layanan ini sangat krusial agar kamu bisa masuk ke server lewat aplikasi seperti PuTTY atau Terminal SSH OS host.

b. cron.service (Regular background program processing daemon)   
Perintah Cek: systemctl status cron
output:
```
ariel@ubuntuser:~$ systemctl status cron
● cron.service - Regular background program processing daemon
     Loaded: loaded (/usr/lib/systemd/system/cron.service; enabled; preset: enabled)
     Active: active (running) since Wed 2026-05-20 14:59:51 UTC; 1h 20min ago
       Docs: man:cron(8)
   Main PID: 879 (cron)
      Tasks: 1 (limit: 4008)
     Memory: 456.0K (peak: 2.1M)
        CPU: 410ms
     CGroup: /system.slice/cron.service
             └─879 /usr/sbin/cron -f -P

Warning: some journal files were not opened due to insufficient permissions.
```
Fungsi: Mengatur eksekusi otomatis untuk tugas-tugas terjadwal (disebut cron jobs) di latar belakang. Layanan ini yang membaca berkas konfigurasi waktu untuk menjalankan skrip pemeliharaan sistem, backup, atau pembersihan berkas sampah secara berkala tanpa intervensi manual.

c. systemd-networkd.service (Network Configuration)   
Perintah Cek: systemctl status systemd-networkd
output:
```
ariel@ubuntuser:~$ systemctl status systemd-networkd
● systemd-networkd.service - Network Configuration
     Loaded: loaded (/usr/lib/systemd/system/systemd-networkd.service; enabled; preset: enabled)
     Active: active (running) since Wed 2026-05-20 14:59:49 UTC; 1h 21min ago
TriggeredBy: ● systemd-networkd.socket
       Docs: man:systemd-networkd.service(8)
             man:org.freedesktop.network1(5)
   Main PID: 446 (systemd-network)
     Status: "Processing requests..."
      Tasks: 1 (limit: 4008)
   FD Store: 0 (limit: 512)
     Memory: 3.7M (peak: 4.0M)
        CPU: 302ms
     CGroup: /system.slice/systemd-networkd.service
             └─446 /usr/lib/systemd/systemd-networkd

Warning: some journal files were not opened due to insufficient permissions.
```
Fungsi: Layanan bawaan sistem yang bertugas mendeteksi perangkat jaringan (seperti kartu LAN virtual di VirtualBox) dan mengatur konfigurasi jaringannya, baik itu pengisian IP Address secara otomatis (DHCP) maupun statis agar server bisa terhubung ke internet atau jaringan lokal.
2. Jalankan systemd-analyze blame dan identifikasi lima layanan dengan waktu inisialisasi terlama. Tampilkan hasilnya menggunakan pipeline: systemd-analyze blame | head -5.   
output:
```
ariel@ubuntuser:~$ systemd-analyze blame | head -5
46.298s fwupd-refresh.service
13.265s apt-daily-upgrade.service
 3.483s dev-sda2.device
 2.676s snapd.seeded.service
 2.408s snapd.service
```

3. Jalankan systemctl –failed dan dokumentasikan hasilnya. Jika ada layanan yang gagal, caritahu penyebabnya dengan journalctl -u nama-layanan -n 30.
```
ariel@ubuntuser:~$ systemctl --failed
  UNIT LOAD ACTIVE SUB DESCRIPTION

0 loaded units listed.
```
Tidak ada layanan yang error
## Latihan 10.2 Layanan Kustom dengan Restart Otomatis
1. Buat skrip Bash (referensi Bab 7) bernama monitor-disk.sh yang setiap 30 detik menuliskan penggunaan disk ke berkas log. Gunakan df -h dan date.
```
ariel@ubuntuser:~$ nano ~/lab-os/chapter10-services/monitor-disk.sh
```
isi file
```
#!/bin/bash
while true; do
    echo "--- Penggunaan Disk Akhir: $(date) ---"
    df -h /
    sleep 30
done
```
Hasil test
```
ariel@ubuntuser:~$ chmod +x ~/lab-os/chapter10-services/monitor-disk.sh
ariel@ubuntuser:~/lab-os/chapter10-services$ ./monitor-disk.sh
--- Penggunaan Disk Akhir: Wed May 20 16:42:19 UTC 2026 ---
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda2        50G  4.7G   43G  10% /
ariel@ubuntuser:~/lab-os/chapter10-services$
```
2. Buat berkas unit /etc/systemd/system/monitor-disk.service untuk menjalankan skrip tersebut dengan konfigurasi: Restart=always, RestartSec=5s, dan berjalan sebagai pengguna kamu sendiri.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo nano /etc/systemd/system/monitor-disk.service
```
isi file: 
```
Ini, TOML
[Unit]
Description=Layanan Monitor Penggunaan Disk 30 Detik
After=network.target

[Service]
Type=simple
User=ariel
ExecStart=/home/ariel/lab-os/chapter10-services/monitor-disk.sh
Restart=always
RestartSec=5s

[Install]
WantedBy=multi-user.target
```
3. Aktifkan dan jalankan layanan. Verifikasi dengan systemctl status dan pastikan log masuk ke journal.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo systemctl daemon-reload
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo systemctl enable --now monitor-disk.service
Created symlink /etc/systemd/system/multi-user.target.wants/monitor-disk.service → /etc/systemd/system/monitor-disk.service.
ariel@ubuntuser:~/lab-os/chapter10-services$ systemctl status monitor-disk.service
● monitor-disk.service - Layanan Monitor Penggunaan Disk 30 Detik
     Loaded: loaded (/etc/systemd/system/monitor-disk.service; enabled; preset: enabled)
     Active: active (running) since Wed 2026-05-20 16:45:14 UTC; 22s ago
   Main PID: 2950 (monitor-disk.sh)
      Tasks: 2 (limit: 4008)
     Memory: 564.0K (peak: 928.0K)
        CPU: 38ms
     CGroup: /system.slice/monitor-disk.service
             ├─2950 /bin/bash /home/ariel/lab-os/chapter10-services/monitor-disk.sh
             └─2953 sleep 30

May 20 16:45:14 ubuntuser monitor-disk.sh[2950]: --- Penggunaan Disk Akhir: Wed May 20 04:45:14 PM UTC 2026 ---
May 20 16:45:14 ubuntuser monitor-disk.sh[2952]: Filesystem      Size  Used Avail Use% Mounted on
May 20 16:45:14 ubuntuser monitor-disk.sh[2952]: /dev/sda2        50G  4.7G   43G  10% /
ariel@ubuntuser:~/lab-os/chapter10-services$ journalctl -u monitor-disk.service -n 20
Hint: You are currently not seeing messages from other users and the system.
      Users in groups 'adm', 'systemd-journal' can see all messages.
      Pass -q to turn off this notice.
May 20 16:45:14 ubuntuser monitor-disk.sh[2950]: --- Penggunaan Disk Akhir: Wed May 20 04:45:14 PM UTC 2026 ---
May 20 16:45:14 ubuntuser monitor-disk.sh[2952]: Filesystem      Size  Used Avail Use% Mounted on
May 20 16:45:14 ubuntuser monitor-disk.sh[2952]: /dev/sda2        50G  4.7G   43G  10% /
ariel@ubuntuser:~/lab-os/chapter10-services$
```
4. Simulasikan crash dengan membunuh proses secara paksa (kill -9), tunggu 10 detik, dan verifikasi bahwa layanan hidup kembali secara otomatis.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo kill -9 $(systemctl show monitor-disk.service --property=MainPID --value)
ariel@ubuntuser:~/lab-os/chapter10-services$ systemctl status monitor-disk.service
● monitor-disk.service - Layanan Monitor Penggunaan Disk 30 Detik
     Loaded: loaded (/etc/systemd/system/monitor-disk.service; enabled; preset: enabled)
     Active: active (running) since Wed 2026-05-20 16:46:29 UTC; 45s ago
   Main PID: 2970 (monitor-disk.sh)
      Tasks: 2 (limit: 4008)
     Memory: 564.0K (peak: 1012.0K)
        CPU: 57ms
     CGroup: /system.slice/monitor-disk.service
             ├─2970 /bin/bash /home/ariel/lab-os/chapter10-services/monitor-disk.sh
             └─2976 sleep 30

May 20 16:46:29 ubuntuser monitor-disk.sh[2970]: --- Penggunaan Disk Akhir: Wed May 20 04:46:29 PM UTC 2026 ---
May 20 16:46:29 ubuntuser monitor-disk.sh[2972]: Filesystem      Size  Used Avail Use% Mounted on
May 20 16:46:29 ubuntuser monitor-disk.sh[2972]: /dev/sda2        50G  4.7G   43G  10% /
May 20 16:46:59 ubuntuser monitor-disk.sh[2970]: --- Penggunaan Disk Akhir: Wed May 20 04:46:59 PM UTC 2026 ---
May 20 16:46:59 ubuntuser monitor-disk.sh[2975]: Filesystem      Size  Used Avail Use% Mounted on
May 20 16:46:59 ubuntuser monitor-disk.sh[2975]: /dev/sda2        50G  4.7G   43G  10% /
ariel@ubuntuser:~/lab-os/chapter10-services$
```
5. Bersihkan: nonaktifkan layanan dan hapus berkas unit setelah selesai.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo systemctl disable --now monitor-disk.service
Removed "/etc/systemd/system/multi-user.target.wants/monitor-disk.service".
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo rm /etc/systemd/system/monitor-disk.service
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo systemctl daemon-reload
```
## Latihan 10.3 Investigasi Log dan Keamanan SSH
Analisis log sistem dan tingkatkan keamanan konfigurasi SSH.
1. Gunakan journalctl -b -p err untuk menemukan semua error sejak boot terakhir. Simpanhasilnya ke berkas dan hitung jumlah baris dengan wc -l.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ journalctl -b -p err --no-pager > ~/lab-os/chapter10-services/log-error-boot.txt
Hint: You are currently not seeing messages from other users and the system.
      Users in groups 'adm', 'systemd-journal' can see all messages.
      Pass -q to turn off this notice.
ariel@ubuntuser:~/lab-os/chapter10-services$ wc -l ~/lab-os/chapter10-services/log-error-boot.txt
1 /home/ariel/lab-os/chapter10-services/log-error-boot.txt
```
2. Lakukan tiga perubahan keamanan pada /etc/ssh/sshd_config: tambahkan PermitRootLogin no, MaxAuthTries 3, dan LoginGraceTime 30. Ikuti alur aman: backup, edit, validasi sshd -t, reload.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup.latihan10
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo nano /etc/ssh/sshd_config
```
isi file
```
Port 22
Protocol 2
PermitRootLogin no
PasswordAuthentication yes
ChallengeResponseAuthentication yes
MaxAuthTries 3
LoginGraceTime 30
```
validasi dan reload untuk menerapkan konfigurasi baru
```
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo sshd -t
ariel@ubuntuser:~/lab-os/chapter10-services$ echo $?
0
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo systemctl reload ssh
```
3. Setelah reload, verifikasi tiga hal: layanan masih berjalan (systemctl status ssh), port masih mendengarkan (ss -tlnp | grep ssh), dan konfigurasi baru terbaca (grep -E "PermitRoot|MaxAuth|GraceTime" /etc/ssh/sshd_config).
```
ariel@ubuntuser:~/lab-os/chapter10-services$ systemctl status ssh
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/usr/lib/systemd/system/ssh.service; enabled; preset: enabled)
     Active: active (running) since Wed 2026-05-20 16:15:23 UTC; 38min ago
TriggeredBy: ● ssh.socket
       Docs: man:sshd(8)
             man:sshd_config(5)
    Process: 3104 ExecReload=/usr/sbin/sshd -t (code=exited, status=0/SUCCESS)
    Process: 3105 ExecReload=/bin/kill -HUP $MAINPID (code=exited, status=0/SUCCESS)
   Main PID: 2500 (sshd)
      Tasks: 6 (limit: 4008)
     Memory: 184.1M (peak: 256.2M)
        CPU: 11.158s
     CGroup: /system.slice/ssh.service
             ├─2472 "sshd: ariel [priv]"
             ├─2474 "sshd: ariel@pts/0"
             ├─2475 -bash
             ├─2500 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups"
             ├─3108 systemctl status ssh
             └─3109 less

May 20 16:49:48 ubuntuser sudo[3084]: pam_unix(sudo:session): session closed for user root
May 20 16:49:57 ubuntuser sudo[3088]:    ariel : TTY=pts/0 ; PWD=/home/ariel/lab-os/chapter10-services ; USER=root ; CO>
May 20 16:49:57 ubuntuser sudo[3088]: pam_unix(sudo:session): session opened for user root(uid=0) by ariel(uid=1000)
May 20 16:51:25 ubuntuser sudo[3088]: pam_unix(sudo:session): session closed for user root
May 20 16:52:07 ubuntuser sudo[3097]:    ariel : TTY=pts/0 ; PWD=/home/ariel/lab-os/chapter10-services ; USER=root ; CO>
May 20 16:52:07 ubuntuser sudo[3097]: pam_unix(sudo:session): session opened for user root(uid=0) by ariel(uid=1000)
May 20 16:52:07 ubuntuser sudo[3097]: pam_unix(sudo:session): session closed for user root
May 20 16:52:27 ubuntuser sudo[3100]:    ariel : TTY=pts/0 ; PWD=/home/ariel/lab-os/chapter10-services ; USER=root ; CO>
May 20 16:52:27 ubuntuser sudo[3100]: pam_unix(sudo:session): session opened for user root(uid=0) by ariel(uid=1000)
ariel@ubuntuser:~/lab-os/chapter10-services$ ss -tlnp | grep ssh
ariel@ubuntuser:~/lab-os/chapter10-services$ grep -E "PermitRoot|MaxAuth|GraceTime" /etc/ssh/sshd_config
PermitRootLogin no
MaxAuthTries 3
LoginGraceTime 30
```
4. Kembalikan konfigurasi SSH ke kondisi semula menggunakan berkas backup.
```
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo cp /etc/ssh/sshd_config.backup.latihan10 /etc/ssh/sshd_config
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo sshd -t
ariel@ubuntuser:~/lab-os/chapter10-services$ sudo systemctl reload ssh
```