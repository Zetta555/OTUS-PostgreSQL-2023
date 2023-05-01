# **Домашнее задание**

### Настройка autovacuum с учетом особеностей производительности
### Цель:
  
> 
>    запустить нагрузочный тест pgbench
>    
>    настроить параметры autovacuum
>
>    проверить работу autovacuum

## **Описание/Пошаговая инструкция выполнения домашнего задания:**

<details><summary>• Создать инстанс ВМ с 2 ядрами и 4 Гб ОЗУ и SSD 10GB</summary>

```shell
```
</details>

<details><summary>• Установить на него PostgreSQL 15 с дефолтными настройками</summary>

```shell
```
</details>

<details><summary>• Создать БД для тестов: выполнить pgbench -i postgres</summary>

```shell
```
</details>

<details><summary>• Запустить pgbench -c8 -P 6 -T 60 -U postgres postgres</summary>

```shell
```
</details>

<details><summary>• Применить параметры настройки PostgreSQL из прикрепленного к материалам занятия файла</summary>

```shell
```
</details>

<details><summary>• Протестировать заново</summary>

```shell
```
</details>

<details><summary>• Что изменилось и почему?</summary>

```shell
```
</details>

<details><summary>• Создать таблицу с текстовым полем и заполнить случайными или сгенерированными данным в размере 1млн строк</summary>

```shell
```
</details>

<details><summary>• Посмотреть размер файла с таблицей</summary>

```shell
```
</details>

<details><summary>• 5 раз обновить все строчки и добавить к каждой строчке любой символ</summary>

```shell
```
</details>

<details><summary>• Посмотреть количество мертвых строчек в таблице и когда последний раз приходил автовакуум</summary>

```shell
```
</details>

<details><summary>• Подождать некоторое время, проверяя, пришел ли автовакуум</summary>

```shell
```
</details>

<details><summary>• 5 раз обновить все строчки и добавить к каждой строчке любой символ</summary>

```shell
```
</details>

<details><summary>• Посмотреть размер файла с таблицей</summary>

```shell
```
</details>

<details><summary>• Отключить Автовакуум на конкретной таблице</summary>

```shell
```
</details>

<details><summary>• 10 раз обновить все строчки и добавить к каждой строчке любой символ</summary>

```shell
```
</details>

<details><summary>• Посмотреть размер файла с таблицей</summary>

```shell
```
</details>

<details><summary>• Объясните полученный результат</summary>

```shell
```
</details>

<details><summary>• Не забудьте включить автовакуум)</summary>

```shell
```
</details>

<details><summary>• Задание со *:</summary>

```shell
```
</details>

<details><summary>• Написать анонимную процедуру, в которой в цикле 10 раз обновятся все строчки в искомой таблице. Не забыть вывести номер шага цикла.</summary>

```shell
```
</details>
