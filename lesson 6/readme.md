# **Домашнее задание**

### Установка и настройка PostgreSQL
### Цель:
  
> создавать дополнительный диск для уже существующей виртуальной машины, размечать его и делать на нем файловую систему
>
> переносить содержимое базы данных PostgreSQL на дополнительный диск
>
> переносить содержимое БД PostgreSQL между виртуальными машинами


## **Описание/Пошаговая инструкция выполнения домашнего задания:**

<details><summary>• создайте виртуальную машину c Ubuntu 20.04/22.04 LTS в GCE/ЯО/Virtual Box/докере</summary>
  
  Развёрнута ВМ Ubuntu 
  ```bash
zetta55@ubuntu-vm1:~$ cat /etc/os-release 
PRETTY_NAME="Ubuntu 22.04.2 LTS"
NAME="Ubuntu"
VERSION_ID="22.04"
VERSION="22.04.2 LTS (Jammy Jellyfish)"
VERSION_CODENAME=jammy
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=jammy
zetta55@ubuntu-vm1:~$ sudo dmidecode -s system-manufacturer
[sudo] пароль для zetta55: 
VMware, Inc.
zetta55@ubuntu-vm1:~$ 
  ```
  </details>
  
<details><summary>• поставьте на нее PostgreSQL 15 через sudo apt</summary>

  Подключаю репозиторий и устанавливаю postgresql.
```shell
zetta55@ubuntu-vm1:~$ sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
zetta55@ubuntu-vm1:~$ wget -qO- https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo tee /etc/apt/trusted.gpg.d/pgdg.asc &>/dev/null
zetta55@ubuntu-vm1:~$ sudo apt update
zetta55@ubuntu-vm1:~$ sudo apt install postgresql postgresql-client -y
```
  Проверяю результат установки
```shell
zetta55@ubuntu-vm1:~$ sudo systemctl status postgresql
● postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor preset: enabled)
     Active: active (exited) since Mon 2023-04-24 15:55:19 MSK; 52s ago
   Main PID: 7080 (code=exited, status=0/SUCCESS)
        CPU: 1ms

апр 24 15:55:19 ubuntu-vm1 systemd[1]: Starting PostgreSQL RDBMS...
апр 24 15:55:19 ubuntu-vm1 systemd[1]: Finished PostgreSQL RDBMS.
zetta55@ubuntu-vm1:~$ sudo pg_config --version
PostgreSQL 15.2 (Ubuntu 15.2-1.pgdg22.04+1)
zetta55@ubuntu-vm1:~$

```
</details>

<details><summary>• проверьте что кластер запущен через sudo -u postgres pg_lsclusters</summary>

```shell
zetta55@ubuntu-vm1:~$ sudo -u postgres pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
15  main    5432 online postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
zetta55@ubuntu-vm1:~$
```
</details>

<details><summary>• зайдите из под пользователя postgres в psql и сделайте произвольную таблицу с произвольным содержимым
postgres=# create table test(c1 text);
postgres=# insert into test values('1');
\q</summary>

  Зашёл пользователем postgres в psql, создал таблицу с произвольным содержимым.
```shell
zetta55@ubuntu-vm1:~$ sudo -u postgres psql
could not change directory to "/home/zetta55": Отказано в доступе
psql (15.2 (Ubuntu 15.2-1.pgdg22.04+1))
Type "help" for help.

postgres=# create table test(c1 text);
CREATE TABLE
postgres=# insert into test values('1');
INSERT 0 1
postgres=#

```
</details>


<details><summary>• остановите postgres например через sudo -u postgres pg_ctlcluster 15 main stop</summary>

  Стопаю postgres и проверяю результат выполнения остановки.
```shell
zetta55@ubuntu-vm1:~$ sudo -u postgres pg_ctlcluster 15 main stop
Warning: stopping the cluster using pg_ctlcluster will mark the systemd unit as failed. Consider using systemctl:
  sudo systemctl stop postgresql@15-main
  
zetta55@ubuntu-vm1:~$ sudo -u postgres pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
15  main    5432 down   postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
zetta55@ubuntu-vm1:~$
  
```
</details>


<details><summary>• создайте новый диск к ВМ размером 10GB</summary>

  Исходное состояние дисковой подсистемы VM
```shell
  zetta55@ubuntu-vm1:~$ sudo lsblk
NAME                MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda                   8:0    0    40G  0 disk
├─sda1                8:1    0     1M  0 part
├─sda2                8:2    0   513M  0 part /boot/efi
└─sda3                8:3    0  39,5G  0 part
  ├─vgubuntu-root   253:0    0  37,6G  0 lvm  /var/snap/firefox/common/host-hunspell
  │                                           /
  └─vgubuntu-swap_1 253:1    0   1,9G  0 lvm  [SWAP]
sr0                  11:0    1  1024M  0 rom
zetta55@ubuntu-vm1:~$ df -h
Файл.система              Размер Использовано  Дост Использовано% Cмонтировано в
tmpfs                       795M         1,6M  794M            1% /run
/dev/mapper/vgubuntu-root    37G          12G   24G           34% /
tmpfs                       3,9G            0  3,9G            0% /dev/shm
tmpfs                       5,0M         4,0K  5,0M            1% /run/lock
/dev/sda2                   512M         6,1M  506M            2% /boot/efi
tmpfs                       795M          96K  795M            1% /run/user/1000
zetta55@ubuntu-vm1:~$
```
Создал новый виртуальный диск.
<p align="center">
<image src="/lesson 6/new_vdi.png" alt="New vdi">
</p>
</details>

<details><summary>• добавьте свеже-созданный диск к виртуальной машине - надо зайти в режим ее редактирования и дальше выбрать пункт attach existing disk</summary>


  Подключил созданный диск к VM.
<p align="center">
<image src="/lesson 6/add_new_vdi.png" alt="New vdi">
</p>
<p align="center">
<image src="/lesson 6/add_new_vdi_1.png" alt="New vdi">
</p>
</details>

<details><summary>• проинициализируйте диск согласно инструкции и подмонтировать файловую систему, только не забывайте менять имя диска на актуальное, в вашем случае это скорее всего будет /dev/sdb - https://www.digitalocean.com/community/tutorials/how-to-partition-and-format-storage-devices-in-linux</summary>

```shell
zetta55@ubuntu-vm1:~$ sudo parted -l | grep Error
[sudo] пароль для zetta55:
Ошибка: /dev/sdb: метка диска не определена
zetta55@ubuntu-vm1:~$ lsblk
NAME                MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda                   8:0    0    40G  0 disk
├─sda1                8:1    0     1M  0 part
├─sda2                8:2    0   513M  0 part /boot/efi
└─sda3                8:3    0  39,5G  0 part
  ├─vgubuntu-root   253:0    0  37,6G  0 lvm  /var/snap/firefox/common/host-hunspell
  │                                           /
  └─vgubuntu-swap_1 253:1    0   1,9G  0 lvm  [SWAP]
sdb                   8:16   0    10G  0 disk
sr0                  11:0    1  1024M  0 rom
zetta55@ubuntu-vm1:~$ sudo parted /dev/sdb mklabel gpt
Информация: Не забудьте обновить /etc/fstab.

zetta55@ubuntu-vm1:~$ lsblk
NAME                MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda                   8:0    0    40G  0 disk
├─sda1                8:1    0     1M  0 part
├─sda2                8:2    0   513M  0 part /boot/efi
└─sda3                8:3    0  39,5G  0 part
  ├─vgubuntu-root   253:0    0  37,6G  0 lvm  /var/snap/firefox/common/host-hunspell
  │                                           /
  └─vgubuntu-swap_1 253:1    0   1,9G  0 lvm  [SWAP]
sdb                   8:16   0    10G  0 disk
sr0                  11:0    1  1024M  0 rom
zetta55@ubuntu-vm1:~$ sudo parted -a opt /dev/sdb mkpart primary ext4 0% 100%
Информация: Не забудьте обновить /etc/fstab.

zetta55@ubuntu-vm1:~$ sudo mkfs.ext4 -L 10G /dev/sdb1
mke2fs 1.46.5 (30-Dec-2021)
Creating filesystem with 2620928 4k blocks and 655360 inodes
Filesystem UUID: 885dd9db-3a5b-4b50-a5f9-e198b4a17d63
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632

Allocating group tables: done
Сохранение таблицы inod'ов: done
Создание журнала (16384 блоков): готово
Writing superblocks and filesystem accounting information: готово

zetta55@ubuntu-vm1:~$ sudo lsblk --fs
NAME                FSTYPE      FSVER    LABEL UUID                                   FSAVAIL FSUSE% MOUNTPOINTS
sda
├─sda1
├─sda2              vfat        FAT32          6BDB-C223                               505,9M     1% /boot/efi
└─sda3              LVM2_member LVM2 001       XTxL2s-Kenh-vV71-IwQJ-LUZB-KIdd-Kt169x
  ├─vgubuntu-root   ext4        1.0            f5346c10-26d6-4733-9fae-f20d1a9d6a54     23,3G    31% /var/snap/firefox/common/host-hunspell
  │                                                                                                  /
  └─vgubuntu-swap_1 swap        1              08baf801-2f0b-42f6-8064-33db93e7bb1d                  [SWAP]
sdb
└─sdb1              ext4        1.0      10G   885dd9db-3a5b-4b50-a5f9-e198b4a17d63
sr0
zetta55@ubuntu-vm1:~$

zetta55@ubuntu-vm1:~$ sudo mkdir -p /mnt/10G
[sudo] пароль для zetta55:
  
zetta55@ubuntu-vm1:~$ sudo mount -o defaults /dev/sdb1 /mnt/10G
  
zetta55@ubuntu-vm1:~$ cat /etc/fstab
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
/dev/mapper/vgubuntu-root /               ext4    errors=remount-ro 0       1
# /boot/efi was on /dev/sda2 during installation
UUID=6BDB-C223  /boot/efi       vfat    umask=0077      0       1
/dev/mapper/vgubuntu-swap_1 none            swap    sw              0       0
  
zetta55@ubuntu-vm1:~$ sudo sh -c "echo 'LABEL=10G /mnt/10G ext4 defaults 0 2' >> /etc/fstab"
  
zetta55@ubuntu-vm1:~$ cat /etc/fstab
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
/dev/mapper/vgubuntu-root /               ext4    errors=remount-ro 0       1
# /boot/efi was on /dev/sda2 during installation
UUID=6BDB-C223  /boot/efi       vfat    umask=0077      0       1
/dev/mapper/vgubuntu-swap_1 none            swap    sw              0       0
LABEL=10G /mnt/10G ext4 defaults 0 2
zetta55@ubuntu-vm1:~$

```
</details>


<details><summary>• перезагрузите инстанс и убедитесь, что диск остается примонтированным (если не так смотрим в сторону fstab)</summary>

```shell
zetta55@ubuntu-vm1:~$ sudo reboot

  Welcome to Ubuntu 22.04.2 LTS (GNU/Linux 5.19.0-40-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

Расширенное поддержание безопасности (ESM) для Applications выключено.

0 обновлений может быть применено немедленно.

7 дополнительных обновлений безопасности могут быть применены с помощью ESM Apps.
Подробнее о включении службы ESM Apps at https://ubuntu.com/esm

Last login: Mon Apr 24 17:11:39 2023 from 172.16.0.125

zetta55@ubuntu-vm1:~$ mount | grep dev/sd
/dev/sda2 on /boot/efi type vfat (rw,relatime,fmask=0077,dmask=0077,codepage=437,iocharset=iso8859-1,shortname=mixed,errors=remount-ro)
/dev/sdb1 on /mnt/10G type ext4 (rw,relatime)
zetta55@ubuntu-vm1:~$
  
zetta55@ubuntu-vm1:~$ lsblk | grep sd
sda                   8:0    0    40G  0 disk
├─sda1                8:1    0     1M  0 part
├─sda2                8:2    0   513M  0 part /boot/efi
└─sda3                8:3    0  39,5G  0 part
sdb                   8:16   0    10G  0 disk
└─sdb1                8:17   0    10G  0 part /mnt/10G
zetta55@ubuntu-vm1:~$
```
  как видно после ребута приаттаченый диск успешно смонтировался.
  
</details>

<details><summary>• сделайте пользователя postgres владельцем /mnt/data - chown -R postgres:postgres /mnt/data/</summary>

```shell
zetta55@ubuntu-vm1:~$ sudo chown -R postgres:postgres /mnt/10G/
[sudo] пароль для zetta55:
zetta55@ubuntu-vm1:~$ ls -la /mnt/10G/
итого 24
drwxr-xr-x 3 postgres postgres  4096 апр 24 17:14 .
drwxr-xr-x 3 root     root      4096 апр 24 17:48 ..
drwx------ 2 postgres postgres 16384 апр 24 17:14 lost+found
zetta55@ubuntu-vm1:~$

```
</details>


<details><summary>• перенесите содержимое /var/lib/postgres/15 в /mnt/data - mv /var/lib/postgresql/15 /mnt/data</summary>

```shell
zetta55@ubuntu-vm1:~$ sudo mv /var/lib/postgresql/15 /mnt/10G

zetta55@ubuntu-vm1:~$ sudo ls -la /mnt/10G/15/main/
итого 92
drwx------ 19 postgres postgres 4096 апр 24 18:04 .
drwxr-xr-x  3 postgres postgres 4096 апр 24 15:55 ..
drwx------  5 postgres postgres 4096 апр 24 15:55 base
drwx------  2 postgres postgres 4096 апр 24 18:05 global
drwx------  2 postgres postgres 4096 апр 24 15:55 pg_commit_ts
drwx------  2 postgres postgres 4096 апр 24 15:55 pg_dynshmem
drwx------  4 postgres postgres 4096 апр 24 18:09 pg_logical
drwx------  4 postgres postgres 4096 апр 24 15:55 pg_multixact
drwx------  2 postgres postgres 4096 апр 24 15:55 pg_notify
drwx------  2 postgres postgres 4096 апр 24 15:55 pg_replslot
drwx------  2 postgres postgres 4096 апр 24 15:55 pg_serial
drwx------  2 postgres postgres 4096 апр 24 15:55 pg_snapshots
drwx------  2 postgres postgres 4096 апр 24 18:04 pg_stat
drwx------  2 postgres postgres 4096 апр 24 15:55 pg_stat_tmp
drwx------  2 postgres postgres 4096 апр 24 15:55 pg_subtrans
drwx------  2 postgres postgres 4096 апр 24 15:55 pg_tblspc
drwx------  2 postgres postgres 4096 апр 24 15:55 pg_twophase
-rw-------  1 postgres postgres    3 апр 24 15:55 PG_VERSION
drwx------  3 postgres postgres 4096 апр 24 15:55 pg_wal
drwx------  2 postgres postgres 4096 апр 24 15:55 pg_xact
-rw-------  1 postgres postgres   88 апр 24 15:55 postgresql.auto.conf
-rw-------  1 postgres postgres  130 апр 24 18:04 postmaster.opts
-rw-------  1 postgres postgres  107 апр 24 18:04 postmaster.pid
zetta55@ubuntu-vm1:~$

```
</details>


<details><summary>• попытайтесь запустить кластер - sudo -u postgres pg_ctlcluster 15 main start</summary>

```shell
  zetta55@ubuntu-vm1:~$ sudo -u postgres pg_ctlcluster 15 main start
Error: /var/lib/postgresql/15/main is not accessible or does not exist
zetta55@ubuntu-vm1:~$

```
</details>


<details><summary>• напишите получилось или нет и почему</summary>

  Кластер запустить не получилось, т.к. данные postgres-инстанса были перемещены. Перемещение не было зафиксировано в настройках postgres
</details>


<details><summary>• задание: найти конфигурационный параметр в файлах раположенных в /etc/postgresql/15/main который надо поменять и поменяйте его</summary>

  перехожу в директорию, содержащую конфиги и смотрю текущие настройки, определяющие расположение инстанса
```shell
  zetta55@ubuntu-vm1:~$ cd /etc/postgresql/15/main
zetta55@ubuntu-vm1:/etc/postgresql/15/main$ ls
conf.d  environment  pg_ctl.conf  pg_hba.conf  pg_ident.conf  postgresql.conf  start.conf
zetta55@ubuntu-vm1:/etc/postgresql/15/main$ cat postgresql.conf | grep data
data_directory = '/var/lib/postgresql/15/main'          # use data in another directory
#fsync = on                             # flush data to disk for crash safety
                                        # unrecoverable data corruption)
                                        #   open_datasync
                                        #   fdatasync (default on Linux and FreeBSD)
# Set these on the primary and on any standby that will send replication data.
                                        #   %d = database name
#client_encoding = sql_ascii            # actually, defaults to database
#data_sync_retry = off                  # retry or panic on failure to fsync
                                        # data?
zetta55@ubuntu-vm1:/etc/postgresql/15/main$ 
```
  

</details>


<details><summary>• напишите что и почему поменяли</summary>

    Меняю настройки на новое расположение инстанса
```shell
zetta55@ubuntu-vm1:/etc/postgresql/15/main$ sed -i 's#^\(data_directory\s*=\s*\).*$#\1/mnt/10G/15/main#' /etc/postgresql/15/main/postgresql.conf
sed: невозможно открыть временный файл /etc/postgresql/15/main/sed9btjuA: Отказано в доступе
zetta55@ubuntu-vm1:/etc/postgresql/15/main$ sudo sed -i 's#^\(data_directory\s*=\s*\).*$#\1/mnt/10G/15/main#' /etc/postgresql/15/main/postgresql.conf
zetta55@ubuntu-vm1:/etc/postgresql/15/main$ cat postgresql.conf | grep data
data_directory = /mnt/10G/15/main
#fsync = on                             # flush data to disk for crash safety
                                        # unrecoverable data corruption)
                                        #   open_datasync
                                        #   fdatasync (default on Linux and FreeBSD)
# Set these on the primary and on any standby that will send replication data.
                                        #   %d = database name
#client_encoding = sql_ascii            # actually, defaults to database
#data_sync_retry = off                  # retry or panic on failure to fsync
                                        # data?
zetta55@ubuntu-vm1:/etc/postgresql/15/main$ sudo -u postgres pg_ctlcluster 15 main start
Error: invalid line 42 in /etc/postgresql/15/main/postgresql.conf: data_directory = /mnt/10G/15/main
zetta55@ubuntu-vm1:/etc/postgresql/15/main$
```  
  
  Что-то пошло не так.
  Выснил. Надо было добавить одинарные кавычки и экранировать их.
```shell
zetta55@ubuntu-vm1:/etc/postgresql/15/main$ sudo sed -i 's#^\(data_directory\s*=\s*\).*$#\1'\''/mnt/10G/15/main'\''#' /etc/postgresql/15/main/postgresql.conf
zetta55@ubuntu-vm1:/etc/postgresql/15/main$ sudo -u postgres pg_lsclusters
Ver Cluster Port Status Owner    Data directory   Log file
15  main    5432 down   postgres /mnt/10G/15/main /var/log/postgresql/postgresql-15-main.log
```
  Done. Инстанс сервера стартанул с новым расположением.

</details>


<details><summary>• попытайтесь запустить кластер - sudo -u postgres pg_ctlcluster 15 main start</summary>

```shell
zetta55@ubuntu-vm1:/etc/postgresql/15/main$ sudo -u postgres pg_ctlcluster 15 main start
Warning: the cluster will not be running as a systemd service. Consider using systemctl:
  sudo systemctl start postgresql@15-main
Removed stale pid file.
zetta55@ubuntu-vm1:/etc/postgresql/15/main$ sudo -u postgres pg_lsclusters
Ver Cluster Port Status Owner    Data directory   Log file
15  main    5432 online postgres /mnt/10G/15/main /var/log/postgresql/postgresql-15-main.log
zetta55@ubuntu-vm1:/etc/postgresql/15/main$

```
</details>


<details><summary>• напишите получилось или нет и почему</summary>

```shell
```
</details>


<details><summary>• зайдите через через psql и проверьте содержимое ранее созданной таблицы</summary>

```shell
```
</details>


<details><summary>• задание со звездочкой *: не удаляя существующий инстанс ВМ сделайте новый, поставьте на его PostgreSQL, удалите файлы с данными из /var/lib/postgres, перемонтируйте внешний диск который сделали ранее от первой виртуальной машины ко второй и запустите PostgreSQL на второй машине так чтобы он работал с данными на внешнем диске, расскажите как вы это сделали и что в итоге получилось.</summary>

```shell
```
</details>
