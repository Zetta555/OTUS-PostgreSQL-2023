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
```
</details>

<details><summary>• зайдите в созданный кластер под пользователем postgres</summary>

```shell
```
</details>

<details><summary>• создайте новую базу данных testdb</summary>

```shell
```
</details>

<details><summary>• зайдите в созданную базу данных под пользователем postgres</summary>

```shell
```
</details>

<details><summary>• создайте новую схему testnm</summary>

```shell
```
</details>

<details><summary>• создайте новую таблицу t1 с одной колонкой c1 типа integer</summary>

```shell
```
</details>

<details><summary>• вставьте строку со значением c1=1</summary>

```shell
```
</details>

<details><summary>• создайте новую роль readonly</summary>

```shell
```
</details>

<details><summary>• дайте новой роли право на подключение к базе данных testdb</summary>

```shell
```
</details>

<details><summary>• дайте новой роли право на использование схемы testnm</summary>

```shell
```
</details>

<details><summary>• дайте новой роли право на select для всех таблиц схемы testnm</summary>

```shell
```
</details>

<details><summary>• создайте пользователя testread с паролем test123</summary>

```shell
```
</details>

<details><summary>• дайте роль readonly пользователю testread</summary>

```shell
```
</details>

<details><summary>• зайдите под пользователем testread в базу данных testdb</summary>

```shell
```
</details>

<details><summary>• сделайте select * from t1;</summary>

```shell
```
</details>

<details><summary>• получилось? (могло если вы делали сами не по шпаргалке и не упустили один существенный момент про который позже)</summary>

```shell
```
</details>

<details><summary>• напишите что именно произошло в тексте домашнего задания</summary>

```shell
```
</details>

<details><summary>• у вас есть идеи почему? ведь права то дали?</summary>

```shell
```
</details>

<details><summary>• посмотрите на список таблиц</summary>

```shell
```
</details>

<details><summary>• подсказка в шпаргалке под пунктом 20</summary>

```shell
```
</details>

<details><summary>• а почему так получилось с таблицей (если делали сами и без шпаргалки то может у вас все нормально)</summary>

```shell
```
</details>

<details><summary>• вернитесь в базу данных testdb под пользователем postgres</summary>

```shell
```
</details>

<details><summary>• удалите таблицу t1</summary>

```shell
```
</details>

<details><summary>• создайте ее заново но уже с явным указанием имени схемы testnm</summary>

```shell
```
</details>

<details><summary>• вставьте строку со значением c1=1</summary>

```shell
```
</details>

<details><summary>• зайдите под пользователем testread в базу данных testdb</summary>

```shell
```
</details>

<details><summary>• сделайте select * from testnm.t1;</summary>

```shell
```
</details>

<details><summary>• получилось?</summary>

```shell
```
</details>

<details><summary>• есть идеи почему? если нет - смотрите шпаргалку</summary>

```shell
```
</details>

<details><summary>• как сделать так чтобы такое больше не повторялось? если нет идей - смотрите шпаргалку</summary>

```shell
```
</details>

<details><summary>• сделайте select * from testnm.t1;</summary>

```shell
```
</details>

<details><summary>• получилось?</summary>

```shell
```
</details>

<details><summary>• есть идеи почему? если нет - смотрите шпаргалку</summary>

```shell
```
</details>

<details><summary>• сделайте select * from testnm.t1;</summary>

```shell
```
</details>

<details><summary>• получилось?</summary>

```shell
```
</details>

<details><summary>• ура!</summary>

```shell
```
</details>

<details><summary>• теперь попробуйте выполнить команду create table t2(c1 integer); insert into t2 values (2);</summary>

```shell
```
</details>

<details><summary>• а как так? нам же никто прав на создание таблиц и insert в них под ролью readonly?</summary>

```shell
```
</details>

<details><summary>• есть идеи как убрать эти права? если нет - смотрите шпаргалку</summary>

```shell
```
</details>

<details><summary>• если вы справились сами то расскажите что сделали и почему, если смотрели шпаргалку - объясните что сделали и почему выполнив указанные в ней команды</summary>

```shell
```
</details>

<details><summary>• теперь попробуйте выполнить команду create table t3(c1 integer); insert into t2 values (2);</summary>

```shell
```
</details>

<details><summary>• расскажите что получилось и почему </summary>

```shell
```
</details>
