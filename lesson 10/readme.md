# **Домашнее задание**

### Работа с журналами
### Цель:
  
>  уметь работать с журналами и контрольными точками
>  
>  уметь настраивать параметры журналов

## **Описание/Пошаговая инструкция выполнения домашнего задания:**


<details><summary>1. Настройте выполнение контрольной точки раз в 30 секунд.</summary>
  
```sql
demo=# SHOW checkpoint_timeout;
 checkpoint_timeout
--------------------
 5min
(1 row)

demo=# SHOW log_checkpoints;
 log_checkpoints
-----------------
 on
(1 row)

demo=# ALTER SYSTEM SET checkpoint_timeout = 30;
ALTER SYSTEM

demo=# SELECT pg_reload_conf();
 pg_reload_conf
----------------
 t
(1 row)

demo=# SHOW checkpoint_timeout;
 checkpoint_timeout
--------------------
 30s
(1 row)

demo=#
```
</details>
  
<details><summary>2. 10 минут c помощью утилиты pgbench подавайте нагрузку.</summary>

```shell
zetta55@ubuntu-vm2:~$ sudo su postgres
postgres@ubuntu-vm2:/home/zetta55$ pgbench -P 30 -T 600 -i demo
pgbench: error: some of the specified options cannot be used in initialization (-i) mode
postgres@ubuntu-vm2:/home/zetta55$ pgbench -P 30 -T 600 demo
pgbench (15.3 (Ubuntu 15.3-1.pgdg22.04+1))
starting vacuum...end.
progress: 30.0 s, 424.3 tps, lat 2.356 ms stddev 0.221, 0 failed
progress: 60.0 s, 424.4 tps, lat 2.356 ms stddev 0.221, 0 failed
progress: 90.0 s, 421.6 tps, lat 2.372 ms stddev 0.248, 0 failed
progress: 120.0 s, 413.4 tps, lat 2.419 ms stddev 0.261, 0 failed
progress: 150.0 s, 427.2 tps, lat 2.341 ms stddev 0.246, 0 failed
progress: 180.0 s, 417.2 tps, lat 2.397 ms stddev 0.259, 0 failed
progress: 210.0 s, 409.2 tps, lat 2.443 ms stddev 0.319, 0 failed
progress: 240.0 s, 415.9 tps, lat 2.404 ms stddev 0.235, 0 failed
progress: 270.0 s, 423.8 tps, lat 2.359 ms stddev 0.267, 0 failed
progress: 300.0 s, 427.8 tps, lat 2.337 ms stddev 0.227, 0 failed
progress: 330.0 s, 421.5 tps, lat 2.372 ms stddev 0.280, 0 failed
progress: 360.0 s, 412.8 tps, lat 2.422 ms stddev 0.276, 0 failed
progress: 390.0 s, 420.4 tps, lat 2.378 ms stddev 0.323, 0 failed
progress: 420.0 s, 418.1 tps, lat 2.391 ms stddev 0.267, 0 failed
progress: 450.0 s, 396.9 tps, lat 2.519 ms stddev 0.424, 0 failed
progress: 480.0 s, 398.8 tps, lat 2.507 ms stddev 0.331, 0 failed
progress: 510.0 s, 424.9 tps, lat 2.353 ms stddev 0.274, 0 failed
progress: 540.0 s, 423.4 tps, lat 2.362 ms stddev 0.242, 0 failed
progress: 570.0 s, 423.6 tps, lat 2.360 ms stddev 0.331, 0 failed
progress: 600.0 s, 428.1 tps, lat 2.336 ms stddev 0.215, 0 failed
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1
query mode: simple
number of clients: 1
number of threads: 1
maximum number of tries: 1
duration: 600 s
number of transactions actually processed: 251201
number of failed transactions: 0 (0.000%)
latency average = 2.388 ms
latency stddev = 0.281 ms
initial connection time = 1.635 ms
tps = 418.667927 (without initial connection time)
postgres@ubuntu-vm2:/home/zetta55$

```
</details>
<details><summary>3. Измерьте, какой объем журнальных файлов был сгенерирован за это время. Оцените, какой объем приходится в среднем на одну контрольную точку.</summary>

```shell
demo=# show data_directory;
  data_directory
------------------
 /mnt/10G/15/main
(1 row)

demo=#

zetta55@ubuntu-vm2:~$ sudo ls -lh  /mnt/10G/15/main/pg_wal | sort -d
drwx------ 2 postgres postgres 4,0K апр 24 15:55 archive_status
-rw------- 1 postgres postgres  16M июн 12 22:10 0000000100000002000000AC
-rw------- 1 postgres postgres  16M июн 12 22:14 0000000100000002000000AD
-rw------- 1 postgres postgres  16M июн 12 22:15 0000000100000002000000AE
-rw------- 1 postgres postgres  16M июн 12 22:15 0000000100000002000000AF
-rw------- 1 postgres postgres  16M июн 12 22:16 0000000100000002000000B0
-rw------- 1 postgres postgres  16M июн 12 22:16 0000000100000002000000B1
-rw------- 1 postgres postgres  16M июн 12 22:16 0000000100000002000000B2
-rw------- 1 postgres postgres  16M июн 12 22:17 0000000100000002000000B3
-rw------- 1 postgres postgres  16M июн 12 22:17 0000000100000002000000B4
-rw------- 1 postgres postgres  16M июн 12 22:18 0000000100000002000000B5
-rw------- 1 postgres postgres  16M июн 12 22:18 0000000100000002000000B6
-rw------- 1 postgres postgres  16M июн 12 22:19 0000000100000002000000B7
-rw------- 1 postgres postgres  16M июн 12 22:19 0000000100000002000000B8
-rw------- 1 postgres postgres  16M июн 12 22:19 0000000100000002000000B9
-rw------- 1 postgres postgres  16M июн 12 22:20 0000000100000002000000BA
-rw------- 1 postgres postgres  16M июн 12 22:20 0000000100000002000000BC
-rw------- 1 postgres postgres  16M июн 12 22:21 0000000100000002000000BB
-rw------- 1 postgres postgres  16M июн 12 22:21 0000000100000002000000BD
-rw------- 1 postgres postgres  16M июн 12 22:22 0000000100000002000000BE
-rw------- 1 postgres postgres  16M июн 12 22:22 0000000100000002000000BF
-rw------- 1 postgres postgres  16M июн 12 22:23 0000000100000002000000C0
-rw------- 1 postgres postgres  16M июн 12 22:23 0000000100000002000000C1
-rw------- 1 postgres postgres  16M июн 12 22:23 0000000100000002000000C2
-rw------- 1 postgres postgres  16M июн 12 22:24 0000000100000002000000C3
-rw------- 1 postgres postgres  16M июн 12 22:26 000000010000000200000042
-rw------- 1 postgres postgres  16M мая 14 18:47 000000010000000200000043
-rw------- 1 postgres postgres  16M мая 14 18:47 000000010000000200000046

```
</details>
<details><summary>4. Проверьте данные статистики: все ли контрольные точки выполнялись точно по расписанию. Почему так произошло?</summary>

```shell
zetta55@ubuntu-vm2:~$ tail -n 60 /var/log/postgresql/postgresql-15-main.log | grep checkpoint
2023-06-12 22:05:52.926 MSK [850] LOG:  parameter "checkpoint_timeout" changed to "30"
2023-06-12 22:09:23.151 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:09:26.971 MSK [881] LOG:  checkpoint complete: wrote 29 buffers (0.0%); 0 WAL file(s) added, 0 removed, 0 recycled; write=3.801 s, sync=0.011 s, total=3.820 s; sync files=13, longest=0.003 s, average=0.001 s; distance=147 kB, estimate=147 kB
2023-06-12 22:10:24.029 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:10:51.092 MSK [881] LOG:  checkpoint complete: wrote 1701 buffers (1.3%); 0 WAL file(s) added, 0 removed, 1 recycled; write=27.029 s, sync=0.024 s, total=27.063 s; sync files=38, longest=0.003 s, average=0.001 s; distance=12883 kB, estimate=12883 kB
2023-06-12 22:10:54.099 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:10:55.398 MSK [881] LOG:  checkpoint complete: wrote 9 buffers (0.0%); 0 WAL file(s) added, 0 removed, 0 recycled; write=1.283 s, sync=0.006 s, total=1.299 s; sync files=8, longest=0.003 s, average=0.001 s; distance=26 kB, estimate=11597 kB
2023-06-12 22:14:56.091 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:15:23.036 MSK [881] LOG:  checkpoint complete: wrote 1800 buffers (1.4%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.897 s, sync=0.021 s, total=26.945 s; sync files=26, longest=0.003 s, average=0.001 s; distance=15101 kB, estimate=15101 kB
2023-06-12 22:15:26.038 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:15:53.046 MSK [881] LOG:  checkpoint complete: wrote 1754 buffers (1.3%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.983 s, sync=0.008 s, total=27.008 s; sync files=7, longest=0.004 s, average=0.002 s; distance=19181 kB, estimate=19181 kB
2023-06-12 22:15:56.047 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:16:23.069 MSK [881] LOG:  checkpoint complete: wrote 1987 buffers (1.5%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.990 s, sync=0.016 s, total=27.022 s; sync files=18, longest=0.003 s, average=0.001 s; distance=18291 kB, estimate=19092 kB
2023-06-12 22:16:26.072 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:16:53.087 MSK [881] LOG:  checkpoint complete: wrote 1798 buffers (1.4%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.984 s, sync=0.008 s, total=27.015 s; sync files=6, longest=0.003 s, average=0.002 s; distance=18172 kB, estimate=19000 kB
2023-06-12 22:16:56.087 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:17:23.120 MSK [881] LOG:  checkpoint complete: wrote 2040 buffers (1.6%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.991 s, sync=0.018 s, total=27.033 s; sync files=18, longest=0.003 s, average=0.001 s; distance=19471 kB, estimate=19471 kB
2023-06-12 22:17:26.123 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:17:53.047 MSK [881] LOG:  checkpoint complete: wrote 1909 buffers (1.5%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.889 s, sync=0.009 s, total=26.925 s; sync files=7, longest=0.003 s, average=0.002 s; distance=20291 kB, estimate=20291 kB
2023-06-12 22:17:56.050 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:18:23.076 MSK [881] LOG:  checkpoint complete: wrote 2032 buffers (1.6%); 0 WAL file(s) added, 0 removed, 2 recycled; write=26.992 s, sync=0.018 s, total=27.027 s; sync files=17, longest=0.005 s, average=0.002 s; distance=20247 kB, estimate=20286 kB
2023-06-12 22:18:26.078 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:18:53.091 MSK [881] LOG:  checkpoint complete: wrote 1871 buffers (1.4%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.987 s, sync=0.010 s, total=27.013 s; sync files=6, longest=0.004 s, average=0.002 s; distance=19237 kB, estimate=20181 kB
2023-06-12 22:18:56.093 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:19:23.112 MSK [881] LOG:  checkpoint complete: wrote 1995 buffers (1.5%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.989 s, sync=0.014 s, total=27.019 s; sync files=15, longest=0.004 s, average=0.001 s; distance=18702 kB, estimate=20033 kB
2023-06-12 22:19:26.114 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:19:53.024 MSK [881] LOG:  checkpoint complete: wrote 1853 buffers (1.4%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.888 s, sync=0.008 s, total=26.911 s; sync files=6, longest=0.004 s, average=0.002 s; distance=18418 kB, estimate=19872 kB
2023-06-12 22:19:56.027 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:20:23.062 MSK [881] LOG:  checkpoint complete: wrote 2018 buffers (1.5%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.994 s, sync=0.018 s, total=27.036 s; sync files=17, longest=0.005 s, average=0.002 s; distance=18594 kB, estimate=19744 kB
2023-06-12 22:20:26.063 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:20:53.091 MSK [881] LOG:  checkpoint complete: wrote 1805 buffers (1.4%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.996 s, sync=0.008 s, total=27.029 s; sync files=6, longest=0.005 s, average=0.002 s; distance=18259 kB, estimate=19595 kB
2023-06-12 22:20:56.094 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:21:23.117 MSK [881] LOG:  checkpoint complete: wrote 1969 buffers (1.5%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.994 s, sync=0.015 s, total=27.023 s; sync files=14, longest=0.004 s, average=0.002 s; distance=18486 kB, estimate=19485 kB
2023-06-12 22:21:26.119 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:21:53.044 MSK [881] LOG:  checkpoint complete: wrote 1804 buffers (1.4%); 0 WAL file(s) added, 0 removed, 2 recycled; write=26.900 s, sync=0.007 s, total=26.925 s; sync files=6, longest=0.004 s, average=0.002 s; distance=18366 kB, estimate=19373 kB
2023-06-12 22:21:56.045 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:22:23.088 MSK [881] LOG:  checkpoint complete: wrote 2144 buffers (1.6%); 0 WAL file(s) added, 0 removed, 1 recycled; write=27.010 s, sync=0.014 s, total=27.043 s; sync files=15, longest=0.004 s, average=0.001 s; distance=18534 kB, estimate=19289 kB
2023-06-12 22:22:26.091 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:22:53.116 MSK [881] LOG:  checkpoint complete: wrote 1770 buffers (1.4%); 0 WAL file(s) added, 0 removed, 1 recycled; write=27.001 s, sync=0.009 s, total=27.025 s; sync files=6, longest=0.006 s, average=0.002 s; distance=17862 kB, estimate=19146 kB
2023-06-12 22:22:56.119 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:23:23.043 MSK [881] LOG:  checkpoint complete: wrote 1961 buffers (1.5%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.890 s, sync=0.013 s, total=26.925 s; sync files=15, longest=0.003 s, average=0.001 s; distance=18484 kB, estimate=19080 kB
2023-06-12 22:23:26.047 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:23:53.081 MSK [881] LOG:  checkpoint complete: wrote 1772 buffers (1.4%); 0 WAL file(s) added, 0 removed, 1 recycled; write=27.001 s, sync=0.007 s, total=27.035 s; sync files=6, longest=0.004 s, average=0.002 s; distance=18332 kB, estimate=19005 kB
2023-06-12 22:23:56.083 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:24:23.122 MSK [881] LOG:  checkpoint complete: wrote 2125 buffers (1.6%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.996 s, sync=0.018 s, total=27.040 s; sync files=18, longest=0.004 s, average=0.001 s; distance=18709 kB, estimate=18975 kB
2023-06-12 22:24:26.123 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:24:53.020 MSK [881] LOG:  checkpoint complete: wrote 1778 buffers (1.4%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.879 s, sync=0.007 s, total=26.897 s; sync files=6, longest=0.004 s, average=0.002 s; distance=18401 kB, estimate=18918 kB
2023-06-12 22:24:56.168 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:25:23.039 MSK [881] LOG:  checkpoint complete: wrote 1809 buffers (1.4%); 0 WAL file(s) added, 0 removed, 1 recycled; write=26.849 s, sync=0.011 s, total=26.871 s; sync files=16, longest=0.002 s, average=0.001 s; distance=17074 kB, estimate=18734 kB
2023-06-12 22:25:56.075 MSK [881] LOG:  checkpoint starting: time
2023-06-12 22:25:56.677 MSK [881] LOG:  checkpoint complete: wrote 4 buffers (0.0%); 0 WAL file(s) added, 0 removed, 0 recycled; write=0.589 s, sync=0.004 s, total=0.603 s; sync files=3, longest=0.003 s, average=0.002 s; distance=8 kB, estimate=16861 kB
zetta55@ubuntu-vm2:~$

```
</details>
<details><summary>5. Сравните tps в синхронном/асинхронном режиме утилитой pgbench. Объясните полученный результат.</summary>


```sql
demo=# SHOW synchronous_commit;
 synchronous_commit
--------------------
 on
(1 row)

demo=# ALTER SYSTEM SET synchronous_commit = off;
ALTER SYSTEM
demo=# SELECT pg_reload_conf();
 pg_reload_conf
----------------
 t
(1 row)

demo=# SHOW synchronous_commit;
 synchronous_commit
--------------------
 off
(1 row)

demo=#
```
```shell
zetta55@ubuntu-vm2:~$ sudo su postgres
postgres@ubuntu-vm2:/home/zetta55$ pgbench -P 60 -T 600 demo
pgbench (15.3 (Ubuntu 15.3-1.pgdg22.04+1))
starting vacuum...end.
progress: 60.0 s, 665.6 tps, lat 1.502 ms stddev 0.432, 0 failed
progress: 120.0 s, 672.9 tps, lat 1.486 ms stddev 0.477, 0 failed
progress: 180.0 s, 690.0 tps, lat 1.449 ms stddev 0.499, 0 failed
progress: 240.0 s, 685.2 tps, lat 1.459 ms stddev 0.515, 0 failed
progress: 300.0 s, 681.8 tps, lat 1.466 ms stddev 0.468, 0 failed
progress: 360.0 s, 671.5 tps, lat 1.489 ms stddev 0.530, 0 failed
progress: 420.0 s, 685.9 tps, lat 1.458 ms stddev 0.489, 0 failed
progress: 480.0 s, 716.4 tps, lat 1.395 ms stddev 0.471, 0 failed
progress: 540.0 s, 696.5 tps, lat 1.436 ms stddev 0.450, 0 failed
progress: 600.0 s, 694.7 tps, lat 1.439 ms stddev 0.439, 0 failed
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1
query mode: simple
number of clients: 1
number of threads: 1
maximum number of tries: 1
duration: 600 s
number of transactions actually processed: 411632
number of failed transactions: 0 (0.000%)
latency average = 1.457 ms
latency stddev = 0.479 ms
initial connection time = 1.659 ms
tps = 686.052986 (without initial connection time)
postgres@ubuntu-vm2:/home/zetta55$

```

</details>
<details><summary>6. Создайте новый кластер с включенной контрольной суммой страниц. Создайте таблицу. Вставьте несколько значений. Выключите кластер. Измените пару байт в таблице. Включите кластер и сделайте выборку из таблицы. Что и почему произошло? как проигнорировать ошибку и продолжить работу?</summary>
  
```shell
zetta55@ubuntu-vm2:~$ pg_lsclusters
Ver Cluster Port Status Owner    Data directory   Log file
15  main    5432 online postgres /mnt/10G/15/main /var/log/postgresql/postgresql-15-main.log
zetta55@ubuntu-vm2:~$

zetta55@ubuntu-vm2:~$ sudo pg_createcluster 15 main1 -- --data-checksums
Creating new PostgreSQL cluster 15/main1 ...
/usr/lib/postgresql/15/bin/initdb -D /var/lib/postgresql/15/main1 --auth-local peer --auth-host scram-sha-256 --no-instructions --data-checksums
The files belonging to this database system will be owned by user "postgres".
This user must also own the server process.

The database cluster will be initialized with this locale configuration:
  provider:    libc
  LC_COLLATE:  en_US.UTF-8
  LC_CTYPE:    en_US.UTF-8
  LC_MESSAGES: en_US.UTF-8
  LC_MONETARY: ru_RU.UTF-8
  LC_NUMERIC:  ru_RU.UTF-8
  LC_TIME:     ru_RU.UTF-8
The default database encoding has accordingly been set to "UTF8".
The default text search configuration will be set to "english".

Data page checksums are enabled.

fixing permissions on existing directory /var/lib/postgresql/15/main1 ... ok
creating subdirectories ... ok
selecting dynamic shared memory implementation ... posix
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting default time zone ... Europe/Moscow
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... ok
syncing data to disk ... ok
Ver Cluster Port Status Owner    Data directory               Log file
15  main1   5433 down   postgres /var/lib/postgresql/15/main1 /var/log/postgresql/postgresql-15-main1.log
zetta55@ubuntu-vm2:~$ sudo pg_ctlcluster 15 main1 start
zetta55@ubuntu-vm2:~$ pg_lsclusters
Ver Cluster Port Status Owner    Data directory               Log file
15  main    5432 online postgres /mnt/10G/15/main             /var/log/postgresql/postgresql-15-main.log
15  main1   5433 online postgres /var/lib/postgresql/15/main1 /var/log/postgresql/postgresql-15-main1.log
zetta55@ubuntu-vm2:~$
zetta55@ubuntu-vm2:~$ sudo -u postgres psql -p 5433
could not change directory to "/home/zetta55": Permission denied
psql (15.3 (Ubuntu 15.3-1.pgdg22.04+1))
Type "help" for help.

postgres=# show data_directory;
        data_directory
------------------------------
 /var/lib/postgresql/15/main1
(1 row)

postgres=# \c postgres
You are now connected to database "postgres" as user "postgres".
postgres=# SHOW data_checksums;
 data_checksums
----------------
 on
(1 row)

postgres=#
postgres=# CREATE DATABASE demo1;
CREATE DATABASE
postgres=# \c demo1;
You are now connected to database "demo1" as user "postgres".
demo1=# CREATE TABLE my_frends (id int, name text);
CREATE TABLE
demo1=# \dt+
                                       List of relations
 Schema |   Name    | Type  |  Owner   | Persistence | Access method |    Size    | Description
--------+-----------+-------+----------+-------------+---------------+------------+-------------
 public | my_frends | table | postgres | permanent   | heap          | 8192 bytes |
(1 row)

demo1=# SELECT * FROM my_frends;
 id | name
----+------
(0 rows)

demo1=# INSERT INTO my_frends (id, name) VALUES (1, 'Chip'), (2, 'Dale');
INSERT 0 2
demo1=# SELECT * FROM my_frends;
 id | name
----+------
  1 | Chip
  2 | Dale
(2 rows)

demo1=# \dt+
                                     List of relations
 Schema |   Name    | Type  |  Owner   | Persistence | Access method | Size  | Description
--------+-----------+-------+----------+-------------+---------------+-------+-------------
 public | my_frends | table | postgres | permanent   | heap          | 16 kB |
(1 row)

demo1=# SELECT pg_relation_filepath('my_frends');
 pg_relation_filepath
----------------------
 base/16388/16389
(1 row)

demo1=# \q
```
```shell
zetta55@ubuntu-vm2:~$ sudo pg_ctlcluster 15 main1 stop
[sudo] password for zetta55:
zetta55@ubuntu-vm2:~$ pg_lsclusters
Ver Cluster Port Status Owner    Data directory               Log file
15  main    5432 online postgres /mnt/10G/15/main             /var/log/postgresql/postgresql-15-main.log
15  main1   5433 down   postgres /var/lib/postgresql/15/main1 /var/log/postgresql/postgresql-15-main1.log
zetta55@ubuntu-vm2:~$

ppostgres@ubuntu-vm2:~/15/main1/base/16388$ dd if=/dev/zero of=/var/lib/postgresql/15/main1/base/16388/16389 oflag=dsync conv=notrunc bs=1 count=8
8+0 records in
8+0 records out
8 bytes copied, 0,00606197 s, 1,3 kB/s
postgres@ubuntu-vm2:~/15/main1/base/16388$ ls -la 16389
-rw------- 1 postgres postgres 8192 июн 15 23:43 16389
postgres@ubuntu-vm2:~/15/main1/base/16388$
postgres@ubuntu-vm2:~/15/main1/base/16388$
exit
zetta55@ubuntu-vm2:~$ sudo pg_ctlcluster 15 main1 start
zetta55@ubuntu-vm2:~$ pg_lsclusters
Ver Cluster Port Status Owner    Data directory               Log file
15  main    5432 online postgres /mnt/10G/15/main             /var/log/postgresql/postgresql-15-main.log
15  main1   5433 online postgres /var/lib/postgresql/15/main1 /var/log/postgresql/postgresql-15-main1.log
zetta55@ubuntu-vm2:~$ sudo -u postgres psql -p 5433 demo1
could not change directory to "/home/zetta55": Permission denied
psql (15.3 (Ubuntu 15.3-1.pgdg22.04+1))
Type "help" for help.

demo1=# SELECT * FROM my_frends;
WARNING:  page verification failed, calculated checksum 22553 but expected 30898
ERROR:  invalid page in block 0 of relation base/16388/16389
demo1=# SHOW ignore_checksum_failure;
 ignore_checksum_failure
-------------------------
 off
(1 row)

demo1=# SET ignore_checksum_failure = on;
SET
demo1=# SHOW ignore_checksum_failure;
 ignore_checksum_failure
-------------------------
 on
(1 row)

demo1=# SELECT * FROM my_frends;
WARNING:  page verification failed, calculated checksum 22553 but expected 30898
 id | name
----+------
  1 | Chip
  2 | Dale
(2 rows)

```

  </details>

