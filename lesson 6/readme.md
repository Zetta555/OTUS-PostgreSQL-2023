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

```shell
```
</details>

<details><summary>• проверьте что кластер запущен через sudo -u postgres pg_lsclusters</summary>

```shell
```
</details>

<details><summary>• зайдите из под пользователя postgres в psql и сделайте произвольную таблицу с произвольным содержимым</summary>
postgres=# create table test(c1 text);
postgres=# insert into test values('1');
\q</summary>

```shell
```
</details>


<details><summary>• остановите postgres например через sudo -u postgres pg_ctlcluster 15 main stop</summary>

```shell
```
</details>


<details><summary>• создайте новый диск к ВМ размером 10GB</summary>

```shell
```
</details>


<details><summary>• добавьте свеже-созданный диск к виртуальной машине - надо зайти в режим ее редактирования и дальше выбрать пункт attach existing disk</summary>

```shell
```
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
