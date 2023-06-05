# **Домашнее задание**

### Механизм блокировок
### Цель:
  
> понимать как работает механизм блокировок объектов и строк

## **Описание/Пошаговая инструкция выполнения домашнего задания:**

<details><summary>• Настройте сервер так, чтобы в журнал сообщений сбрасывалась информация о блокировках, удерживаемых более 200 миллисекунд. Воспроизведите ситуацию, при которой в журнале появятся такие сообщения.</summary>

```sql
demo=# SHOW log_lock_waits;         #логирование блокировок по тайм-ауту отключено
 log_lock_waits
----------------
 off
(1 row)
  
demo=# SHOW deadlock_timeout;       #по-умолчание в лог идут блокировки "висящие" более 1сек.
 deadlock_timeout
------------------
 1s
(1 row)

demo=# ALTER SYSTEM SET log_lock_waits = on;      #в настройках указываю активацию логирования блокировок
ALTER SYSTEM
demo=# SELECT pg_reload_conf();                   #применяю изменённую конфигурацию
 pg_reload_conf
----------------
 t
(1 row)

demo=# SHOW log_lock_waits;                       #проверяю применение изменённых настроек
 log_lock_waits
----------------
 on
(1 row)

demo=#  ALTER SYSTEM SET deadlock_timeout = 200;        #измению тайм-аут по истечении которого будут логироваться блокировки
ALTER SYSTEM
demo=# SELECT pg_reload_conf();                         #применяю изменённую конфигурацию
 pg_reload_conf
----------------
 t
(1 row)

demo=# SHOW deadlock_timeout;                            #проверяю применение изменённых настроек
 deadlock_timeout
------------------
 200ms
(1 row)

demo=#
```
  В первой консоли выполняю:
```sql
demo=# CREATE TABLE tmp_demo_1(id int, col text);
CREATE TABLE
demo=#  INSERT INTO tmp_demo_1(id, col) values (1, 'col_1');
INSERT 0 1
demo=# SELECT * FROM tmp_demo_1;
 id |  col
----+-------
  1 | col_1
(1 row)

demo=# begin;
BEGIN
demo=*#  UPDATE tmp_demo_1 SET col = 'col_1_1' WHERE id = 1;
UPDATE 1
demo=*#
```
  Во второй:
```sql
demo=# BEGIN;
BEGIN
demo=*#  UPDATE tmp_demo_1 SET col = 'col_1_1_1' WHERE id = 1;

```
Выполнение транзакции подвисает.
Делают COMMIT в первой консоли и во второй, проверяю лог
  ```sql
2023-06-05 16:36:40.108 MSK [20956] postgres@demo LOG:  process 20956 still waiting for ShareLock on transaction 272185 after 201.239 ms
2023-06-05 16:36:40.108 MSK [20956] postgres@demo DETAIL:  Process holding the lock: 15156. Wait queue: 20956.
2023-06-05 16:36:40.108 MSK [20956] postgres@demo CONTEXT:  while updating tuple (0,1) in relation "tmp_demo_1"
2023-06-05 16:36:40.108 MSK [20956] postgres@demo STATEMENT:  UPDATE tmp_demo_1 SET col = 'col_1_1_1' WHERE id = 1;
2023-06-05 16:36:54.027 MSK [20956] postgres@demo LOG:  process 20956 acquired ShareLock on transaction 272185 after 14121.135 ms
2023-06-05 16:36:54.027 MSK [20956] postgres@demo CONTEXT:  while updating tuple (0,1) in relation "tmp_demo_1"
2023-06-05 16:36:54.027 MSK [20956] postgres@demo STATEMENT:  UPDATE tmp_demo_1 SET col = 'col_1_1_1' WHERE id = 1;

```
  В логе наблюдаю сообщение о возникшей блокировке ShareLock
</details>

<details><summary>• Смоделируйте ситуацию обновления одной и той же строки тремя командами UPDATE в разных сеансах. Изучите возникшие блокировки в представлении pg_locks и убедитесь, что все они понятны. Пришлите список блокировок и объясните, что значит каждая.</summary>

  Запустил транзакции в трёх сеансах.
  
  в первом:
```sql
demo=# begin;
BEGIN
demo=*# UPDATE tmp_demo_1 SET col = 'col1' WHERE id = 1;

```
  во втором:
  ```sql
demo=# begin;
BEGIN
demo=*# UPDATE tmp_demo_1 SET col = 'col11' WHERE id = 1;       #выполнение транзакции подвисло

```
  в третьем:
  ```sql
demo=# begin;
BEGIN
demo=*# UPDATE tmp_demo_1 SET col = 'col111' WHERE id = 1;       #выполнение транзакции подвисло

```
  Наблюдаю информацию о возникших блокировках:
```sql
demo=*# SELECT locktype, mode, granted, pid, pg_blocking_pids(pid) AS wait_for FROM pg_locks WHERE relation = 'tmp_demo_1'::regclass;
 locktype |       mode       | granted |  pid  | wait_for
----------+------------------+---------+-------+----------
 relation | RowExclusiveLock | t       | 31858 | {20956}
 relation | RowExclusiveLock | t       | 20956 | {15156}
 relation | RowExclusiveLock | t       | 15156 | {}
 tuple    | ExclusiveLock    | f       | 31858 | {20956}
 tuple    | ExclusiveLock    | t       | 20956 | {15156}
(5 rows)

demo=*#
```
Выполнение транзакции с pid 15156 прошло, не ожидает выполнения никаких других процессов.
во второй сессии транзакция с pid 20956 ожидает выполнения транзакции с pid {15156} первой сессии.
в третьей сессии транзакция с pid 31858 ожидает соответственно выполнения транцзакции с pid {20956} второй сессии.
 
Делаю COMMIT в первой сессии и проверяю информацию о блокировках:
  
  ```sql
demo=*# COMMIT;
COMMIT
demo=# SELECT locktype, mode, granted, pid, pg_blocking_pids(pid) AS wait_for FROM pg_locks WHERE relation = 'tmp_demo_1'::regclass;
 locktype |       mode       | granted |  pid  | wait_for
----------+------------------+---------+-------+----------
 relation | RowExclusiveLock | t       | 31858 | {20956}
 relation | RowExclusiveLock | t       | 20956 | {}
(2 rows)
```
  Во второй сессии транзакция с pid 20956 выполнилась(но не завершена), блокирует транзакцию c pid 31858 из третьей сессии.
  Делаю COMMIT во второй сессии и проверяю информацию о блокировках:
  ```sql
demo=# SELECT locktype, mode, granted, pid, pg_blocking_pids(pid) AS wait_for FROM pg_locks WHERE relation = 'tmp_demo_1'::regclass;
 locktype |       mode       | granted |  pid  | wait_for
----------+------------------+---------+-------+----------
 relation | RowExclusiveLock | t       | 31858 | {}
(1 row)

```
видно, что теперь выполнение транзакции в третьей сессии ничто не блокирует.
</details>

<details><summary>• Воспроизведите взаимоблокировку трех транзакций. Можно ли разобраться в ситуации постфактум, изучая журнал сообщений?</summary>
  _
  Запускаю три транзакции в разных сессиях и закольцовываю блокировки.
```sql
demo=# SELECT * FROM tmp_demo_1;
 id | col
----+------
  1 | col1
  2 | col2
  3 | col3
(3 rows)

demo=# BEGIN;
BEGIN
demo=*# UPDATE tmp_demo_1 set col = col||'1' WHERE id = 1;
UPDATE 1
demo=*# UPDATE tmp_demo_1 set col = col||'1' WHERE id = 2;
UPDATE 1
demo=*# COMMIT;
COMMIT
demo=#
```
  
  ```sql
demo=# BEGIN;
BEGIN
demo=*#  UPDATE tmp_demo_1 set col = col||'2' WHERE id = 2;
UPDATE 1
demo=*#  UPDATE tmp_demo_1 set col = col||'2' WHERE id = 3;
UPDATE 1
demo=*# COMMIT;
COMMIT
demo=#

```
  
  ```sql
demo=# BEGIN;
BEGIN
demo=*#  UPDATE tmp_demo_1 set col = col||'3' WHERE id = 3;
UPDATE 1
demo=*#  UPDATE tmp_demo_1 set col = col||'3' WHERE id = 1;
ERROR:  deadlock detected
DETAIL:  Process 31858 waits for ShareLock on transaction 272193; blocked by process 15156.
Process 15156 waits for ShareLock on transaction 272194; blocked by process 20956.
Process 20956 waits for ShareLock on transaction 272195; blocked by process 31858.
HINT:  See server log for query details.
CONTEXT:  while updating tuple (0,7) in relation "tmp_demo_1"
demo=!# COMMIT;
ROLLBACK
demo=#

```
  
  ```shell
zetta55@ubuntu-vm2:~$ tail -n 30 /var/log/postgresql/postgresql-15-main.log
2023-06-05 18:55:42.482 MSK [873] LOG:  checkpoint starting: time
2023-06-05 18:55:42.599 MSK [873] LOG:  checkpoint complete: wrote 2 buffers (0.0%); 0 WAL file(s) added, 0 removed, 0 recycled; write=0.105 s, sync=0.004 s, total=0.117 s; sync files=2, longest=0.003 s, average=0.002 s; distance=1 kB, estimate=71 kB
2023-06-05 18:58:24.313 MSK [15156] postgres@demo LOG:  process 15156 still waiting for ShareLock on transaction 272194 after 207.358 ms
2023-06-05 18:58:24.313 MSK [15156] postgres@demo DETAIL:  Process holding the lock: 20956. Wait queue: 15156.
2023-06-05 18:58:24.313 MSK [15156] postgres@demo CONTEXT:  while updating tuple (0,8) in relation "tmp_demo_1"
2023-06-05 18:58:24.313 MSK [15156] postgres@demo STATEMENT:  UPDATE tmp_demo_1 set col = col||'1' WHERE id = 2;
2023-06-05 18:58:35.341 MSK [20956] postgres@demo LOG:  process 20956 still waiting for ShareLock on transaction 272195 after 209.512 ms
2023-06-05 18:58:35.341 MSK [20956] postgres@demo DETAIL:  Process holding the lock: 31858. Wait queue: 20956.
2023-06-05 18:58:35.341 MSK [20956] postgres@demo CONTEXT:  while updating tuple (0,9) in relation "tmp_demo_1"
2023-06-05 18:58:35.341 MSK [20956] postgres@demo STATEMENT:  UPDATE tmp_demo_1 set col = col||'2' WHERE id = 3;
2023-06-05 18:58:51.413 MSK [31858] postgres@demo LOG:  process 31858 detected deadlock while waiting for ShareLock on transaction 272193 after 200.260 ms
2023-06-05 18:58:51.413 MSK [31858] postgres@demo DETAIL:  Process holding the lock: 15156. Wait queue: .
2023-06-05 18:58:51.413 MSK [31858] postgres@demo CONTEXT:  while updating tuple (0,7) in relation "tmp_demo_1"
2023-06-05 18:58:51.413 MSK [31858] postgres@demo STATEMENT:  UPDATE tmp_demo_1 set col = col||'3' WHERE id = 1;
2023-06-05 18:58:51.413 MSK [31858] postgres@demo ERROR:  deadlock detected
2023-06-05 18:58:51.413 MSK [31858] postgres@demo DETAIL:  Process 31858 waits for ShareLock on transaction 272193; blocked by process 15156.
        Process 15156 waits for ShareLock on transaction 272194; blocked by process 20956.
        Process 20956 waits for ShareLock on transaction 272195; blocked by process 31858.
        Process 31858: UPDATE tmp_demo_1 set col = col||'3' WHERE id = 1;
        Process 15156: UPDATE tmp_demo_1 set col = col||'1' WHERE id = 2;
        Process 20956: UPDATE tmp_demo_1 set col = col||'2' WHERE id = 3;
2023-06-05 18:58:51.413 MSK [31858] postgres@demo HINT:  See server log for query details.
2023-06-05 18:58:51.413 MSK [31858] postgres@demo CONTEXT:  while updating tuple (0,7) in relation "tmp_demo_1"
2023-06-05 18:58:51.413 MSK [31858] postgres@demo STATEMENT:  UPDATE tmp_demo_1 set col = col||'3' WHERE id = 1;
2023-06-05 18:58:51.413 MSK [20956] postgres@demo LOG:  process 20956 acquired ShareLock on transaction 272195 after 16281.768 ms
2023-06-05 18:58:51.413 MSK [20956] postgres@demo CONTEXT:  while updating tuple (0,9) in relation "tmp_demo_1"
2023-06-05 18:58:51.413 MSK [20956] postgres@demo STATEMENT:  UPDATE tmp_demo_1 set col = col||'2' WHERE id = 3;
2023-06-05 18:59:35.596 MSK [15156] postgres@demo LOG:  process 15156 acquired ShareLock on transaction 272194 after 71490.872 ms
2023-06-05 18:59:35.596 MSK [15156] postgres@demo CONTEXT:  while updating tuple (0,8) in relation "tmp_demo_1"
2023-06-05 18:59:35.596 MSK [15156] postgres@demo STATEMENT:  UPDATE tmp_demo_1 set col = col||'1' WHERE id = 2;
zetta55@ubuntu-vm2:~$
  

```
  Последовательность зафикисрованных событий позволяет понять, что привело к ошибке взаимоблокировки (deadlock).
</details>

<details><summary>• Могут ли две транзакции, выполняющие единственную команду UPDATE одной и той же таблицы (без where), заблокировать друг друга?</summary>

  При равных условиях выполнения транзакций в сессиях, одна транзакция может заблокировать другую, в том случае, если вторая обратится к строке, обрабатываемой первой транзакцией, с блокировкой RowExclusiveLock. 
</details>
