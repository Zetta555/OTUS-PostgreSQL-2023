# **Домашнее задание**

### Установка и настройка PostgteSQL в контейнере Docker
### Цель:
  
> установить PostgreSQL в Docker контейнере
> 
> настроить контейнер для внешнего подключения


## **Описание/Пошаговая инструкция выполнения домашнего задания:**

<details><summary>• создать ВМ с Ubuntu 20.04/22.04 или развернуть докер любым удобным способом</summary>
  
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
<details><summary>• поставить на нем Docker Engine</summary>
  
  По чудесному мануалу ["Install Docker Engine on Ubuntu"](https://docs.docker.com/engine/install/ubuntu/) произвожу установку Docker.
  ```shell
  zetta55@ubuntu-vm1:~$ sudo apt-get install ca-certificates curl gnupg
  zetta55@ubuntu-vm1:~$ sudo mkdir -m 0755 -p /etc/apt/keyrings
  zetta55@ubuntu-vm1:~$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
  zetta55@ubuntu-vm1:~$ echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  zetta55@ubuntu-vm1:~$
  zetta55@ubuntu-vm1:~$ sudo chmod a+r /etc/apt/keyrings/docker.gpg
  zetta55@ubuntu-vm1:~$ sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
Чтение списков пакетов… Готово
Построение дерева зависимостей… Готово
Чтение информации о состоянии… Готово         
Будут установлены следующие дополнительные пакеты:
  docker-ce-rootless-extras git git-man liberror-perl libslirp0 pigz slirp4netns
Предлагаемые пакеты:
  aufs-tools cgroupfs-mount | cgroup-lite git-daemon-run | git-daemon-sysvinit git-doc git-email git-gui gitk gitweb git-cvs git-mediawiki git-svn
Следующие НОВЫЕ пакеты будут установлены:
  containerd.io docker-buildx-plugin docker-ce docker-ce-cli docker-ce-rootless-extras docker-compose-plugin git git-man liberror-perl libslirp0 pigz slirp4netns
Обновлено 0 пакетов, установлено 12 новых пакетов, для удаления отмечено 0 пакетов, и 0 пакетов не обновлено.
Необходимо скачать 113 MB архивов.
После данной операции объём занятого дискового пространства возрастёт на 416 MB.
Хотите продолжить? [Д/н] y
   ```
  Проверяю работоспособность Docker-a
   ```shell
zetta55@ubuntu-vm1:~$ sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
2db29710123e: Pull complete 
Digest: sha256:ffb13da98453e0f04d33a6eee5bb8e46ee50d08ebe17735fc0779d0349e889e9
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

zetta55@ubuntu-vm1:~$ docker -v
Docker version 23.0.3, build 3e7cbfd
zetta55@ubuntu-vm1:~$
  ```
  </details>
<details><summary>• сделать каталог /var/lib/postgres</summary>

  ```shell
  zetta55@ubuntu-vm1:~$ sudo mkdir /var/lib/postgres
  zetta55@ubuntu-vm1:~$ cd /var/lib/postgres/
  zetta55@ubuntu-vm1:/var/lib/postgres$ pwd
  /var/lib/postgres
  zetta55@ubuntu-vm1:/var/lib/postgres$ 
  ```
  </details>
<details><summary>• развернуть контейнер с PostgreSQL 15 смонтировав в него /var/lib/postgresql</summary>

  Предварительно создам docker-сеть: 
  ```shell  
zetta55@ubuntu-vm1:~$ docker network create pg-net
28f583a590246ded271ef911e3d236b7092fdffdd13ebc16b7f579f9764baabf
zetta55@ubuntu-vm1:~$
  ```
  
  Далее разворачиваю контейнер, подмонтировав в него локальный(с хоста) каталог /var/lib/postgresql
  ```shell
zetta55@ubuntu-vm1:~$ docker run --name pg-server --network pg-net -e POSTGRES_PASSWORD=postgres -d -p 5432:5432 -v /var/lib/postgres:/var/lib/postgresql/data postgres:15.2
Unable to find image 'postgres:15.2' locally
15.2: Pulling from library/postgres
26c5c85e47da: Pull complete
1c30a4c3f519: Pull complete
d5c0f1ae682d: Pull complete
1b1b2890ec0f: Pull complete
391087799df7: Pull complete
b413b4057e31: Pull complete
4fa4edfeab8b: Pull complete
b0a4d596bc61: Pull complete
f6d73cd87199: Pull complete
62b0bb33c69b: Pull complete
bb0ddb7e7f1a: Pull complete
583ec94d38ee: Pull complete
efdf2a922e82: Pull complete
Digest: sha256:6cc97262444f1c45171081bc5a1d4c28b883ea46a6e0d1a45a8eac4a7f4767ab
Status: Downloaded newer image for postgres:15.2
2ece1f883c820f3ebd1d4e8d826b0defce99ab3835aeb86cf2f52f9438c78154
zetta55@ubuntu-vm1:~$ 
  ```
 
 Проверяю работу контейнера:
 ```shell
 zetta55@ubuntu-vm1:~$ docker ps
CONTAINER ID   IMAGE           COMMAND                  CREATED          STATUS         PORTS                                       NAMES
2ece1f883c82   postgres:15.2   "docker-entrypoint.s…"   10 seconds ago   Up 8 seconds   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   pg-server
zetta55@ubuntu-vm1:~$ 
 ```
  </details>
<details><summary>• развернуть контейнер с клиентом postgres</summary>
  
  ```shell  
zetta55@ubuntu-vm1:~$ docker run -it --rm --network pg-net --name pg-client postgres:15.2 psql -h pg-server -U postgres
Password for user postgres:
psql: error: connection to server at "pg-server" (172.19.0.2), port 5432 failed: FATAL:  password authentication failed for user "postgres"
zetta55@ubuntu-vm1:~$ docker run -it --rm --network pg-net --name pg-client postgres:15.2 psql -h pg-server -U postgres
Password for user postgres:
psql (15.2 (Debian 15.2-1.pgdg110+1))
Type "help" for help.

postgres=# SELECT version();
                                                           version
-----------------------------------------------------------------------------------------------------------------------------
 PostgreSQL 15.2 (Debian 15.2-1.pgdg110+1) on x86_64-pc-linux-gnu, compiled by gcc (Debian 10.2.1-6) 10.2.1 20210110, 64-bit
(1 row)

postgres=#   
  ```
  </details>
<details><summary>• подключится из контейнера с клиентом к контейнеру с сервером и сделать таблицу с парой строк</summary>

  Проверяемся, что запущены два контейнера с сервером и клиентом:
  ```shell  
zetta55@ubuntu-vm1:~$ docker ps -a
CONTAINER ID   IMAGE           COMMAND                  CREATED          STATUS          PORTS                                       NAMES
90cdcdebdcd4   postgres:15.2   "docker-entrypoint.s…"   3 minutes ago    Up 3 minutes    5432/tcp                                    pg-client
2ece1f883c82   postgres:15.2   "docker-entrypoint.s…"   11 minutes ago   Up 11 minutes   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   pg-server
zetta55@ubuntu-vm1:~$
  
  ```
  
  Смотрю состав свежеподнятого кластера postgresql в контейнере.
  ```shell  
  postgres=# SELECT * FROM pg_database;
 oid |  datname  | datdba | encoding | datlocprovider | datistemplate | datallowconn | datconnlimit | datfrozenxid | datminmxid | dattablespace | datcollate |  datctype  | daticulocale | datcollversi
on |               datacl
-----+-----------+--------+----------+----------------+---------------+--------------+--------------+--------------+------------+---------------+------------+------------+--------------+-------------
---+-------------------------------------
   5 | postgres  |     10 |        6 | c              | f             | t            |           -1 |          717 |          1 |          1663 | en_US.utf8 | en_US.utf8 |              | 2.31
   |
   1 | template1 |     10 |        6 | c              | t             | t            |           -1 |          717 |          1 |          1663 | en_US.utf8 | en_US.utf8 |              | 2.31
   | {=c/postgres,postgres=CTc/postgres}
   4 | template0 |     10 |        6 | c              | t             | f            |           -1 |          717 |          1 |          1663 | en_US.utf8 | en_US.utf8 |              |
   | {=c/postgres,postgres=CTc/postgres}
(3 rows)

postgres=#
  ```
  
  Создаю таблицу, добавляю строки.
  ```shell
  
postgres=# CREATE TABLE students (FirstName CHARACTER VARYING(30), LastName CHARACTER VARYING(30));
CREATE TABLE
postgres=# 
postgres=# \dt
          List of relations
 Schema |   Name   | Type  |  Owner
--------+----------+-------+----------
 public | students | table | postgres
(1 row)

postgres=# \dt+
                                     List of relations
 Schema |   Name   | Type  |  Owner   | Persistence | Access method |  Size   | Description
--------+----------+-------+----------+-------------+---------------+---------+-------------
 public | students | table | postgres | permanent   | heap          | 0 bytes |
(1 row)

postgres=# \d students
                      Table "public.students"
  Column   |         Type          | Collation | Nullable | Default
-----------+-----------------------+-----------+----------+---------
 firstname | character varying(30) |           |          |
 lastname  | character varying(30) |           |          |

postgres=# INSERT INTO students VALUES ('Vasya', 'Pupkin');
INSERT 0 1
postgres=# SELECT * FROM students;
 firstname | lastname
-----------+----------
 Vasya     | Pupkin
(1 row)

postgres=# INSERT INTO students VALUES ('Feodosij', 'Krynkin');
INSERT 0 1
postgres=# SELECT * FROM students;
 firstname | lastname
-----------+----------
 Vasya     | Pupkin
 Feodosij  | Krynkin
(2 rows)

postgres=#
  ```
  </details>
<details><summary>• подключится к контейнеру с сервером с ноутбука/компьютера извне инстансов GCP/ЯО/места установки докера</summary>
  
  Скачиваю [DBeaver](https://dbeaver.io/download/), устанавливаю на рабочую станцию:
  
  <p align="center">
  <image src="/lesson 3/about_DBeaver.png">
  </p>

  подключаюсь к кластеру postgresql:
  <p align="center">
  <image src="/lesson 3/DBeaver_connect2db.png ">
  </p>
  </details>
<details><summary>• удалить контейнер с сервером</summary>
  
  Выхожу из контейнера с клиентом postgres, останавливаю контейнер с сервером, удаляю контейнер с сервером:
  
  ```shell  
postgres=# \q
zetta55@ubuntu-vm1:~$ docker ps
CONTAINER ID   IMAGE           COMMAND                  CREATED             STATUS             PORTS                                       NAMES
2ece1f883c82   postgres:15.2   "docker-entrypoint.s…"   About an hour ago   Up About an hour   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   pg-server
zetta55@ubuntu-vm1:~$ docker stop pg-server
pg-server
zetta55@ubuntu-vm1:~$ docker ps -a
CONTAINER ID   IMAGE           COMMAND                  CREATED             STATUS                      PORTS     NAMES
2ece1f883c82   postgres:15.2   "docker-entrypoint.s…"   About an hour ago   Exited (0) 37 seconds ago             pg-server
zetta55@ubuntu-vm1:~$ docker rm 2ece1f883c82
2ece1f883c82
zetta55@ubuntu-vm1:~$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
zetta55@ubuntu-vm1:~$

  ```
  </details>
<details><summary>• создать его заново</summary>
  
  ```shell  
  
  ```
  </details>
<details><summary>• подключится снова из контейнера с клиентом к контейнеру с сервером</summary>

  ```shell  
  
  ```
  </details>
<details><summary>• проверить, что данные остались на месте</summary>

  ```shell  
  
  ```
  </details>
<details><summary>• оставляйте в ЛК ДЗ комментарии что и как вы делали и как боролись с проблемами</summary>

  ```shell  
  
  ```
  </details>
