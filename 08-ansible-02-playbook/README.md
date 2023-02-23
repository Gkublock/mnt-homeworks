# Домашнее задание к занятию "2. Работа с Playbook"

## Подготовка к выполнению

1. ***(Необязательно) Изучите, что такое [clickhouse](https://www.youtube.com/watch?v=fjTNS2zkeBs)***
   и [vector](https://www.youtube.com/watch?v=CgEhyffisLY)
2. ***Создайте свой собственный (или используйте старый) публичный репозиторий на github с произвольным именем.***
3.***Скачайте [playbook](./playbook/) из репозитория с домашним заданием и перенесите его в свой репозиторий.***
4. ***Подготовьте хосты в соответствии с группами из предподготовленного playbook.***

## Основная часть

1. ***Приготовьте свой собственный inventory файл `prod.yml`.***
2. ***Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает [vector](https://vector.dev).***
3. ***При создании tasks рекомендую использовать модули: `get_url`, `template`, `unarchive`, `file`.***
4. ***Tasks должны: скачать нужной версии дистрибутив, выполнить распаковку в выбранную директорию, установить vector.***
5. ***Запустите `ansible-lint site.yml` и исправьте ошибки, если они есть.***
===========
   ___Исправлено, единственную ошибку отбивает на имена___
   [gkublock@fedora playbook]$ ansible-lint site.yml
   WARNING Overriding detected file kind 'yaml' with 'playbook' for given positional argument: site.yml
   WARNING Listing 4 violation(s) that are fatal
   name[missing]: All tasks should be named.
   ___Но с ними все вроде как ОК, плейбук отрабатывает___
6. ***Попробуйте запустить playbook на этом окружении с флагом `--check`.*** 
___Падает с ошибкой, т.к. при опции check не производится скачивание файла___
7. ***Запустите playbook на `prod.yml` окружении с флагом `--diff`. Убедитесь, что изменения на системе произведены.***

 [gkublock@fedora pycharm-community-2022.3.2]$ ls /opt/vector/vector-x86_64-unknown-linux-gnu/
 ____
   bin config etc LICENSE README.md

[gkublock@fedora playbook]$ ansible-playbook -i inventory/prod.yml -K vector.yml --diff --tags install
____
BECOME password:

PLAY [get base url] 
TASK [Gathering Facts]

ok: [vector-01]
____
TASK [Get tar] 

ok: [vector-01]
____
TASK [Extract archive] 

ok: [vector-01]
____
TASK [Configure] 
--- before: /opt/vector/vector-x86_64-unknown-linux-gnu/config/vector.toml
+++ after: /home/gkublock/.ansible/tmp/ansible-local-8940cg9r5y6o/tmpz2i5j5ws/vector.toml
changed: [vector-01]
____
PLAY
RECAP 
vector-01                  : ok=4 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
____

8. Повторно запустите playbook с флагом `--diff` и убедитесь, что playbook идемпотентен.

   [gkublock@fedora playbook]$ ansible-playbook -i inventory/prod.yml -K vector.yml --diff --tags install
   BECOME password:
____
PLAY [get base url] 
TASK [Gathering Facts] 

ok: [vector-01]
____
TASK [Get tar] 

ok: [vector-01]
____
TASK [Extract archive] 

ok: [vector-01]
____

TASK [Configure] 
--- before: /opt/vector/vector-x86_64-unknown-linux-gnu/config/vector.toml

changed: [vector-01]
____
PLAY
RECAP 
vector-01                  : ok=4 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
____

9. ***Подготовьте README.md файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть
   параметры и теги.***
___Readme расположен в playbook/Readme_vector.md___
10. ***Готовый playbook выложите в свой репозиторий, поставьте тег `08-ansible-02-playbook` на фиксирующий коммит, в ответ
    предоставьте ссылку на него.***

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
