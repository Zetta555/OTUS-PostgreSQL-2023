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

  ```shell  
  
  ```
  </details>
<details><summary>• развернуть контейнер с клиентом postgres</summary>
  
  ```shell  
  
  ```
  </details>
<details><summary>• подключится из контейнера с клиентом к контейнеру с сервером и сделать таблицу с парой строк</summary>
  
  ```shell  
  
  ```
  </details>
<details><summary>• подключится к контейнеру с сервером с ноутбука/компьютера извне инстансов GCP/ЯО/места установки докера</summary>
  
  ```shell  
  
  ```
  </details>
<details><summary>• удалить контейнер с сервером</summary>
  
  ```shell  
  
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
