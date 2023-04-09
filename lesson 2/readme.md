## **Домашнее задание (ДЗ)**

Работа с уровнями изоляции транзакции в PostgreSQL

Цель:

* научиться управлять уровнем изолции транзации в PostgreSQL и понимать особенность работы уровней read commited и repeatable read

Ход выполнения ДЗ:
  
  
<details><summary>создать новый проект в Google Cloud Platform, Яндекс облако или на любых ВМ, докере</summary>

Для целей ДЗ развёрнута VM "Ubuntu 22.04.2 LTS"
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
zetta55@ubuntu-vm1:~$ uname -a
Linux ubuntu-vm1 5.19.0-38-generic #39~22.04.1-Ubuntu SMP PREEMPT_DYNAMIC Fri Mar 17 21:16:15 UTC 2 x86_64 x86_64 x86_64 GNU/Linux
zetta55@ubuntu-vm1:~$ 
```

</details>
<details><summary>добавить свой ssh ключ в metadata ВМ</summary>

генерируем пару ключей, забрасываем публичный ключ на сервер, подключаемся к серверу.

</details>

<details><summary>зайти удаленным ssh (первая сессия), не забывайте про ssh-add</summary>
  
  ```shell
zetta55@ubuntu-vm1:~$ who
zetta55  :0           2023-04-09 17:37 (:0) #локальная сессия
zetta55  pts/2        2023-04-09 23:14 (172.16.0.125) #подключение по SSH
```
</details>

<details><summary>поставить PostgreSQL</summary>
  
  ```shell
zetta55@ubuntu-vm1:~$ sudo apt update && sudo apt upgrade -y && sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list' && wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add - && sudo apt-get update && sudo apt-get -y install postgresql-15
  
установлен пакет postgresql-15 самой новой версии (15.2-1.pgdg22.04+1)
  
zetta55@ubuntu-vm1:~$ pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
15  main    5432 online postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
zetta55@ubuntu-vm1:~$

```
</details>

<details><summary>зайти вторым ssh (вторая сессия)</summary>
  
```shell
zetta55@ubuntu-vm1:~$ who
zetta55  :0           2023-04-09 17:37 (:0)
zetta55  pts/2        2023-04-09 23:22 (172.16.0.125)
zetta55  pts/3        2023-04-09 23:26 (172.16.0.125) #вторая SSH-сессия
zetta55@ubuntu-vm1:~$
```
</details>

<details><summary>запустить везде psql из под пользователя postgres</summary>

Для дальнейших манипуляций буду использовать postgresql-14
```shell
zetta55@ubuntu-vm1:~$ pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
15  main    5432 online postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
zetta55@ubuntu-vm1:~$ sudo pg_ctlcluster 15 main stop
zetta55@ubuntu-vm1:~$ sudo pg_dropcluster 15 main
zetta55@ubuntu-vm1:~$ pg_lsclusters
Ver Cluster Port Status Owner Data directory Log file
zetta55@ubuntu-vm1:~$ sudo -u postgres pg_createcluster 14 main
Error: no initdb program for version 14 found
zetta55@ubuntu-vm1:~$ sudo apt update && sudo apt upgrade -y && sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list' && wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add - && sudo apt-get update && sudo apt-get -y install postgresql-14
  
...
  
zetta55@ubuntu-vm1:~$ pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
14  main    5432 online postgres /var/lib/postgresql/14/main /var/log/postgresql/postgresql-14-main.log
zetta55@ubuntu-vm1:~$
zetta55@ubuntu-vm1:~$ pg_ctlcluster 14 main start
Warning: the cluster will not be running as a systemd service. Consider using systemctl:
  sudo systemctl start postgresql@14-main
Error: You must run this program as the cluster owner (postgres) or root
zetta55@ubuntu-vm1:~$ sudo systemctl start postgresql@14-main
zetta55@ubuntu-vm1:~$ pg_ctlcluster 14 main start
Warning: the cluster will not be running as a systemd service. Consider using systemctl:
  sudo systemctl start postgresql@14-main
Error: You must run this program as the cluster owner (postgres) or root
zetta55@ubuntu-vm1:~$ sudo -u postgres pg_ctlcluster 14 main start
Warning: the cluster will not be running as a systemd service. Consider using systemctl:
  sudo systemctl start postgresql@14-main
Cluster is already running.
zetta55@ubuntu-vm1:~$


```

в каждой ssh-сессии делаю:
```shell
zetta55@ubuntu-vm1:~$ sudo -u postgres psql
[sudo] пароль для zetta55:
could not change directory to "/home/zetta55": Отказано в доступе
psql (15.2 (Ubuntu 15.2-1.pgdg22.04+1), server 14.7 (Ubuntu 14.7-1.pgdg22.04+1))
Type "help" for help.

postgres=#
```
</details>

<details><summary>выключить auto commit</summary>
  
Создаю базу iso и отключаю auto commit  
```shell
postgres=# CREATE DATABASE iso;
CREATE DATABASE
postgres=# \c iso
psql (15.2 (Ubuntu 15.2-1.pgdg22.04+1), server 14.7 (Ubuntu 14.7-1.pgdg22.04+1))
You are now connected to database "iso" as user "postgres".
iso=# \l
                                                 List of databases
   Name    |  Owner   | Encoding |   Collate   |    Ctype    | ICU Locale | Locale Provider |   Access privileges
-----------+----------+----------+-------------+-------------+------------+-----------------+-----------------------
 iso       | postgres | UTF8     | ru_RU.UTF-8 | ru_RU.UTF-8 |            | libc            |
 postgres  | postgres | UTF8     | ru_RU.UTF-8 | ru_RU.UTF-8 |            | libc            |
 template0 | postgres | UTF8     | ru_RU.UTF-8 | ru_RU.UTF-8 |            | libc            | =c/postgres          +
           |          |          |             |             |            |                 | postgres=CTc/postgres
 template1 | postgres | UTF8     | ru_RU.UTF-8 | ru_RU.UTF-8 |            | libc            | =c/postgres          +
           |          |          |             |             |            |                 | postgres=CTc/postgres
(4 rows)

iso=# SELECT current_database();
 current_database
------------------
 iso
(1 row)

iso=# CREATE TABLE test (i serial, amount int);
CREATE TABLE
iso=# INSERT INTO test(amount) VALUES (100);
INSERT 0 1
iso=# INSERT INTO test(amount) VALUES (500);
INSERT 0 1
iso=# SELECT * FROM test;
 i | amount
---+--------
 1 |    100
 2 |    500
(2 rows)

iso=# \echo :AUTOCOMMIT
on
iso=# \set AUTOCOMMIT OFF
iso=# \echo :AUTOCOMMIT
OFF
iso=#
```
</details>

<details><summary>сделать в первой сессии новую таблицу и наполнить ее данными create table persons(id serial, first_name text, second_name text); insert into persons(first_name, second_name) values('ivan', 'ivanov'); insert into persons(first_name, second_name) values('petr', 'petrov'); commit;</summary>
</details>

<details><summary>посмотреть текущий уровень изоляции: show transaction isolation level</summary>
</details>

<details><summary>начать новую транзакцию в обоих сессиях с дефолтным (не меняя) уровнем изоляции</summary>
</details>

<details><summary>в первой сессии добавить новую запись insert into persons(first_name, second_name) values('sergey', 'sergeev');</summary>
</details>

<details><summary>сделать select * from persons во второй сессии</summary>
</details>

<details><summary>видите ли вы новую запись и если да то почему?</summary>
</details>

<details><summary>завершить первую транзакцию - commit;</summary>
</details>

<details><summary>сделать select * from persons во второй сессии</summary>
</details>

<details><summary>видите ли вы новую запись и если да то почему?</summary>
</details>

<details><summary>завершите транзакцию во второй сессии</summary>
</details>

<details><summary>начать новые но уже repeatable read транзации - set transaction isolation level repeatable read;</summary>
</details>

<details><summary>в первой сессии добавить новую запись insert into persons(first_name, second_name) values('sveta', 'svetova');</summary>
</details>

<details><summary>сделать select * from persons во второй сессии</summary>
</details>

<details><summary>видите ли вы новую запись и если да то почему?</summary>
</details>

<details><summary>завершить первую транзакцию - commit;</summary>
</details>

<details><summary>сделать select * from persons во второй сессии</summary>
</details>

<details><summary>видите ли вы новую запись и если да то почему?</summary>
</details>

<details><summary>завершить вторую транзакцию</summary>
</details>

<details><summary>сделать select * from persons во второй сессии</summary>
</details>

<details><summary>видите ли вы новую запись и если да то почему?</summary>
</details>
