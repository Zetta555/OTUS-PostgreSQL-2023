# **Домашнее задание**

### Работа с базами данных, пользователями и правами
### Цель:
  
> 
>    создание новой базы данных, схемы и таблицы
>    
>    создание роли для чтения данных из созданной схемы созданной базы данных
>
>    создание роли для чтения и записи из созданной схемы созданной базы данных

## **Описание/Пошаговая инструкция выполнения домашнего задания:**

<details><summary>• создайте новый кластер PostgresSQL 14</summary>

```shell
zetta55@ubuntu-vm3:~$ sudo apt install postgresql-14
...
...
fixing permissions on existing directory /var/lib/postgresql/14/main ... ok
creating subdirectories ... ok
selecting dynamic shared memory implementation ... posix
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting default time zone ... Europe/Moscow
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... ok
syncing data to disk ... ok
update-alternatives: using /usr/share/postgresql/14/man/man1/postmaster.1.gz to provide /usr/share/man/man1/postmaster.1.gz (postmaster.1.gz) in auto mode
Processing triggers for man-db (2.10.2-1) ...
Processing triggers for libc-bin (2.35-0ubuntu3.1) ...
zetta55@ubuntu-vm3:~$
```
Проверяю установку postgres-a
```shell
zetta55@ubuntu-vm3:~$ sudo systemctl status postgresql
● postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor preset: enabled)
     Active: active (exited) since Mon 2023-05-01 15:26:26 MSK; 3min 21s ago
    Process: 35449 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
   Main PID: 35449 (code=exited, status=0/SUCCESS)
        CPU: 1ms
мая 01 15:26:26 ubuntu-vm3 systemd[1]: Starting PostgreSQL RDBMS...
мая 01 15:26:26 ubuntu-vm3 systemd[1]: Finished PostgreSQL RDBMS.

zetta55@ubuntu-vm3:~$ sudo pg_config --version
PostgreSQL 14.7 (Ubuntu 14.7-0ubuntu0.22.04.1)

zetta55@ubuntu-vm3:~$ sudo -u postgres pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
14  main    5432 online postgres /var/lib/postgresql/14/main /var/log/postgresql/postgresql-14-main.log
zetta55@ubuntu-vm3:~$
```
ок
</details>

<details><summary>• зайдите в созданный кластер под пользователем postgres</summary>

```shell
zetta55@ubuntu-vm3:~$ sudo -u postgres psql
could not change directory to "/home/zetta55": Permission denied
psql (14.7 (Ubuntu 14.7-0ubuntu0.22.04.1))
Type "help" for help.

postgres=#
```
</details>

<details><summary>• создайте новую базу данных testdb</summary>

```shell
postgres=# CREATE DATABASE testdb;
CREATE DATABASE
postgres=#

```
</details>

<details><summary>• зайдите в созданную базу данных под пользователем postgres</summary>

```shell
zetta55@ubuntu-vm3:~$ sudo -u postgres psql testdb
[sudo] password for zetta55:
could not change directory to "/home/zetta55": Permission denied
psql (14.7 (Ubuntu 14.7-0ubuntu0.22.04.1))
Type "help" for help.

testdb=#

```
</details>

<details><summary>• создайте новую схему testnm</summary>

```shell
testdb=# CREATE SCHEMA testnm;
CREATE SCHEMA
testdb=#

```
</details>

<details><summary>• создайте новую таблицу t1 с одной колонкой c1 типа integer</summary>

```shell
testdb=# CREATE TABLE t1(c1 integer);
CREATE TABLE
testdb=#

```
</details>

<details><summary>• вставьте строку со значением c1=1</summary>

```shell
testdb=# INSERT INTO t1 values(1);
INSERT 0 1
testdb=#

```
</details>

<details><summary>• создайте новую роль readonly</summary>

```shell
testdb=# CREATE ROLE readonly;
CREATE ROLE
testdb=#

```
</details>

<details><summary>• дайте новой роли право на подключение к базе данных testdb</summary>

```shell
testdb=# GRANT CONNECT ON DATABASE testdb TO readonly;
GRANT
testdb=#

```
</details>

<details><summary>• дайте новой роли право на использование схемы testnm</summary>

```shell
testdb=# GRANT USAGE ON SCHEMA testnm TO readonly;
GRANT
testdb=#

```
</details>

<details><summary>• дайте новой роли право на select для всех таблиц схемы testnm</summary>

```shell
testdb=# GRANT SELECT ON ALL TABLES IN SCHEMA testnm TO readonly;
GRANT
testdb=#

```
</details>

<details><summary>• создайте пользователя testread с паролем test123</summary>

```shell
testdb=# CREATE USER testread WITH PASSWORD 'test123';
CREATE ROLE
testdb=#

```
</details>

<details><summary>• дайте роль readonly пользователю testread</summary>

```shell
testdb=# GRANT readonly TO testread;
GRANT ROLE
testdb=#

```
</details>

<details><summary>• зайдите под пользователем testread в базу данных testdb</summary>

```shell
testdb=# \c testdb testread;
connection to server on socket "/var/run/postgresql/.s.PGSQL.5432" failed: FATAL:  Peer authentication failed for user "testread"
Previous connection kept
testdb=# \q
zetta55@ubuntu-vm3:~$
```
явно подключаюсь
```shell
zetta55@ubuntu-vm3:~$ psql -h 127.0.0.1 -U testread -d testdb -W
Password:
psql (14.7 (Ubuntu 14.7-0ubuntu0.22.04.1))
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
Type "help" for help.

testdb=>
```
</details>

<details><summary>• сделайте select * from t1;</summary>

```shell
testdb=> SELECT * FROM t1;
ERROR:  permission denied for table t1
testdb=>

```
</details>

<details><summary>• получилось? (могло если вы делали сами не по шпаргалке и не упустили один существенный момент про который позже)</summary>

```shell
Нет, не получилось.
```
</details>

<details><summary>• напишите что именно произошло в тексте домашнего задания</summary>
  
  У пользователя testread нет прав использовать схему public, к которой принадлежит таблица t1
</details>

<details><summary>• у вас есть идеи почему? ведь права то дали?</summary>

  Права пользователю testread дали на схему readonly. Целевая таблица в пространстве схемы public. Не был скорректирован параметр search_path. А при созданни таблицы t1 не указали схему.
</details>

<details><summary>• посмотрите на список таблиц</summary>

```shell
testdb=> \dt
        List of relations
 Schema | Name | Type  |  Owner
--------+------+-------+----------
 public | t1   | table | postgres
(1 row)

testdb=>

```
о чём и речь.
</details>

<details><summary>• подсказка в шпаргалке под пунктом 20</summary>

  Таблица создана в схеме public а не testnm и прав на public для роли readonly не давали
</details>

<details><summary>• а почему так получилось с таблицей (если делали сами и без шпаргалки то может у вас все нормально)</summary>

  Потому что в search_path скорее всего "$user", public при том что схемы $USER нет то таблица по умолчанию создалась в public
```shell  
  testdb=> SHOW search_path;
   search_path
-----------------
 "$user", public
(1 row)

testdb=>
```
</details>

<details><summary>• вернитесь в базу данных testdb под пользователем postgres</summary>

```shell
testdb=> \q
zetta55@ubuntu-vm3:~$ sudo -u postgres psql testdb
[sudo] password for zetta55:
could not change directory to "/home/zetta55": Permission denied
psql (14.7 (Ubuntu 14.7-0ubuntu0.22.04.1))
Type "help" for help.

testdb=#

```
</details>

<details><summary>• удалите таблицу t1</summary>

```shell
testdb=# DROP TABLE t1;
DROP TABLE
testdb=#

```
</details>

<details><summary>• создайте ее заново но уже с явным указанием имени схемы testnm</summary>

```shell
testdb=# CREATE TABLE testnm.t1(c1 integer);
CREATE TABLE
testdb=#

```
</details>

<details><summary>• вставьте строку со значением c1=1</summary>

```shell
testdb=# INSERT INTO testnm.t1 values(1);
INSERT 0 1
testdb=#

```
</details>

<details><summary>• зайдите под пользователем testread в базу данных testdb</summary>

```shell
zetta55@ubuntu-vm3:~$ psql -h 127.0.0.1 -U testread -d testdb -W
Password:
psql (14.7 (Ubuntu 14.7-0ubuntu0.22.04.1))
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
Type "help" for help.

testdb=>

```
</details>

<details><summary>• сделайте select * from testnm.t1;</summary>

```shell
testdb=> SELECT * FROM testnm.t1;
ERROR:  permission denied for table t1
testdb=>

```
</details>

<details><summary>• получилось?</summary>

Нет.
</details>

<details><summary>• есть идеи почему? если нет - смотрите шпаргалку</summary>

  Потому что grant SELECT on all TABLEs in SCHEMA testnm TO readonly дал доступ только для существующих на тот момент времени таблиц а t1 пересоздавалась
</details>

<details><summary>• как сделать так чтобы такое больше не повторялось? если нет идей - смотрите шпаргалку</summary>

```shell
zetta55@ubuntu-vm3:~$ sudo -u postgres psql testdb
could not change directory to "/home/zetta55": Permission denied
psql (14.7 (Ubuntu 14.7-0ubuntu0.22.04.1))
Type "help" for help.

testdb=# ALTER default privileges in SCHEMA testnm grant SELECT on TABLEs to readonly;
ALTER DEFAULT PRIVILEGES
testdb=#

```
</details>

<details><summary>• сделайте select * from testnm.t1;</summary>

```shell
testdb=> select * from testnm.t1;
ERROR:  permission denied for table t1
testdb=>

```
</details>

<details><summary>• получилось?</summary>

  Нет.
</details>

<details><summary>• есть идеи почему? если нет - смотрите шпаргалку</summary>

  Потому что ALTER default будет действовать для новых таблиц а grant SELECT on all TABLEs in SCHEMA testnm TO readonly отработал только для существующих на тот момент времени. надо сделать снова или grant SELECT или пересоздать таблицу
</details>

<details><summary>• сделайте select * from testnm.t1;</summary>

```shell
zetta55@ubuntu-vm3:~$ sudo -u postgres psql testdb
could not change directory to "/home/zetta55": Permission denied
psql (14.7 (Ubuntu 14.7-0ubuntu0.22.04.1))
Type "help" for help.

testdb=# grant SELECT on all TABLEs in SCHEMA testnm TO readonly;
GRANT
testdb=# \q
zetta55@ubuntu-vm3:~$ psql -h 127.0.0.1 -U testread -d testdb -W
Password:
psql (14.7 (Ubuntu 14.7-0ubuntu0.22.04.1))
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
Type "help" for help.

testdb=> select * from testnm.t1;
 c1
----
  1
(1 row)

testdb=>

```
</details>

<details><summary>• получилось?</summary>
  Да.
</details>

<details><summary>• ура!</summary></details>

<details><summary>• теперь попробуйте выполнить команду create table t2(c1 integer); insert into t2 values (2);</summary>

```shell
testdb=> create table t2(c1 integer); insert into t2 values (2);
CREATE TABLE
INSERT 0 1
testdb=>

```
</details>

<details><summary>• а как так? нам же никто прав на создание таблиц и insert в них под ролью readonly?</summary>

  Это все потому что search_path указывает в первую очередь на схему public. А схема public создается в каждой базе данных по умолчанию. И grant на все действия в этой схеме дается роли public. А роль public добавляется всем новым пользователям. Соответсвенно каждый пользователь может по умолчанию создавать объекты в схеме public любой базы данных, ес-но если у него есть право на подключение к этой базе данных. 
  ```shell
testdb=> \dt
        List of relations
 Schema | Name | Type  |  Owner
--------+------+-------+----------
 public | t2   | table | testread
(1 row)

testdb=>
  ```
</details>

<details><summary>• есть идеи как убрать эти права? если нет - смотрите шпаргалку</summary>

  Чтобы раз и навсегда забыть про роль public - а в продакшн базе данных про нее лучше забыть - выполню следующие действия 

```shell
zetta55@ubuntu-vm3:~$ sudo -u postgres psql testdb
could not change directory to "/home/zetta55": Permission denied
psql (14.7 (Ubuntu 14.7-0ubuntu0.22.04.1))
Type "help" for help.

testdb=# revoke CREATE on SCHEMA public FROM public;
REVOKE
testdb=# revoke all on DATABASE testdb FROM public;
REVOKE
testdb=#

```
</details>

<details><summary>• если вы справились сами то расскажите что сделали и почему, если смотрели шпаргалку - объясните что сделали и почему выполнив указанные в ней команды</summary>

</details>

<details><summary>• теперь попробуйте выполнить команду create table t3(c1 integer); insert into t2 values (2);</summary>

```shell
testdb=> create table t3(c1 integer); insert into t2 values (2);
ERROR:  permission denied for schema public
LINE 1: create table t3(c1 integer);
                     ^
INSERT 0 1

```
</details>

<details><summary>• расскажите что получилось и почему </summary>

Создать таблицу без явного указания схемы не удалось, т.к. ранее отозвали у схемы public права на создание для базы testdb. А public - является схемой по-умолчанию.
Добавил значение в таблицу t2, т.к. были отозваны права для схемы public для новых объектов, но не были отозваны для таблицы t2.
</details>
