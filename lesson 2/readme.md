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
