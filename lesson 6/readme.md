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
```
</details>


<details><summary>• перезагрузите инстанс и убедитесь, что диск остается примонтированным (если не так смотрим в сторону fstab)</summary>

```shell
```
</details>

<details><summary>• сделайте пользователя postgres владельцем /mnt/data - chown -R postgres:postgres /mnt/data/</summary>

```shell
```
</details>


<details><summary>• перенесите содержимое /var/lib/postgres/14 в /mnt/data - mv /var/lib/postgresql/15/mnt/data</summary>

```shell
```
</details>


<details><summary>• попытайтесь запустить кластер - sudo -u postgres pg_ctlcluster 15 main start</summary>

```shell
```
</details>


<details><summary>• напишите получилось или нет и почему</summary>

```shell
```
</details>


<details><summary>• задание: найти конфигурационный параметр в файлах раположенных в /etc/postgresql/15/main который надо поменять и поменяйте его</summary>

```shell
```
</details>


<details><summary>• напишите что и почему поменяли</summary>

```shell
```
</details>


<details><summary>• попытайтесь запустить кластер - sudo -u postgres pg_ctlcluster 15 main start</summary>

```shell
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
