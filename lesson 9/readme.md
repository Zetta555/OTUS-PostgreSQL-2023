# **Домашнее задание**

### Механизм блокировок
### Цель:
  
> понимать как работает механизм блокировок объектов и строк

## **Описание/Пошаговая инструкция выполнения домашнего задания:**

<details><summary>• Настройте сервер так, чтобы в журнал сообщений сбрасывалась информация о блокировках, удерживаемых более 200 миллисекунд. Воспроизведите ситуацию, при которой в журнале появятся такие сообщения.</summary>

```shell
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
```shell
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
```shell
demo=# BEGIN;
BEGIN
demo=*#  UPDATE tmp_demo_1 SET col = 'col_1_1_1' WHERE id = 1;

```
Выполнение транзакции подвисает.
Делают COMMIT в первой консоли и во второй, проверяю лог
  ```shell
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

```shell

```
</details>

<details><summary>• Воспроизведите взаимоблокировку трех транзакций. Можно ли разобраться в ситуации постфактум, изучая журнал сообщений?</summary>

```shell

```
</details>

<details><summary>• Могут ли две транзакции, выполняющие единственную команду UPDATE одной и той же таблицы (без where), заблокировать друг друга?</summary>

```shell

```
</details>
