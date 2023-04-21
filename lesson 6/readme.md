# **Домашнее задание**

### Установка и настройка PostgreSQL
### Цель:
  
> создавать дополнительный диск для уже существующей виртуальной машины, размечать его и делать на нем файловую систему
> переносить содержимое базы данных PostgreSQL на дополнительный диск
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
