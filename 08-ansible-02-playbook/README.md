# Домашнее задание к занятию "2. Работа с Playbook"

## Подготовка к выполнению

1. (Необязательно) Изучите, что такое [clickhouse](https://www.youtube.com/watch?v=fjTNS2zkeBs) и [vector](https://www.youtube.com/watch?v=CgEhyffisLY)
2. Создайте свой собственный (или используйте старый) публичный репозиторий на github с произвольным именем.
3. Скачайте [playbook](./playbook/) из репозитория с домашним заданием и перенесите его в свой репозиторий.
4. Подготовьте хосты в соответствии с группами из предподготовленного playbook.

## Основная часть

1. Приготовьте свой собственный inventory файл `prod.yml`.
2. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает [vector](https://vector.dev).
3. При создании tasks рекомендую использовать модули: `get_url`, `template`, `unarchive`, `file`.
4. Tasks должны: скачать нужной версии дистрибутив, выполнить распаковку в выбранную директорию, установить vector.
5. Запустите `ansible-lint site.yml` и исправьте ошибки, если они есть.
6. Попробуйте запустить playbook на этом окружении с флагом `--check`. -  Падает с ошибкой, т.к. при опции check не производится скачивание файла
7. Запустите playbook на `prod.yml` окружении с флагом `--diff`. Убедитесь, что изменения на системе произведены.
[gkublock@fedora pycharm-community-2022.3.2]$ ls /opt/vector/vector-x86_64-unknown-linux-gnu/
bin  config  etc  LICENSE  README.md
[gkublock@fedora playbook]$ ansible-playbook -i inventory/prod.yml -K vector.yml --diff --tags install
BECOME password: 

PLAY [get base url] ********************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *****************************************************************************************************************************************************************************************************************
ok: [vector-01]

TASK [Get tar] *************************************************************************************************************************************************************************************************************************
ok: [vector-01]

TASK [Extract archive] *****************************************************************************************************************************************************************************************************************
ok: [vector-01]

TASK [Configure] ***********************************************************************************************************************************************************************************************************************
--- before: /opt/vector/vector-x86_64-unknown-linux-gnu/config/vector.toml
+++ after: /home/gkublock/.ansible/tmp/ansible-local-8940cg9r5y6o/tmpz2i5j5ws/vector.toml
@@ -1,44 +1,7 @@
-#                                    __   __  __
-#                                    \ \ / / / /
-#                                     \ V / / /
-#                                      \_/  \/
-#
-#                                    V E C T O R
-#                                   Configuration
-#
-# ------------------------------------------------------------------------------
-# Website: https://vector.dev
-# Docs: https://vector.dev/docs
-# Chat: https://chat.vector.dev
-# ------------------------------------------------------------------------------
+[sources.in]
+type = "stdin"
 
-# Change this to use a non-default directory for Vector data storage:
-# data_dir = "/var/lib/vector"
-
-# Random Syslog-formatted logs
-[sources.dummy_logs]
-type = "demo_logs"
-format = "syslog"
-interval = 1
-
-# Parse Syslog logs
-# See the Vector Remap Language reference for more info: https://vrl.dev
-[transforms.parse_logs]
-type = "remap"
-inputs = ["dummy_logs"]
-source = '''
-. = parse_syslog!(string!(.message))
-'''
-
-# Print parsed logs to stdout
-[sinks.print]
+[sinks.out]
+inputs = ["in"]
 type = "console"
-inputs = ["parse_logs"]
-encoding.codec = "json"
-
-# Vector's GraphQL API (disabled by default)
-# Uncomment to try it out with the `vector top` command or
-# in your browser at http://localhost:8686
-#[api]
-#enabled = true
-#address = "127.0.0.1:8686"
+encoding.codec = "text"

changed: [vector-01]

PLAY RECAP *****************************************************************************************************************************************************************************************************************************
vector-01                  : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   


8. Повторно запустите playbook с флагом `--diff` и убедитесь, что playbook идемпотентен.
[gkublock@fedora playbook]$ ansible-playbook -i inventory/prod.yml -K vector.yml --diff --tags install
BECOME password: 

PLAY [get base url] ********************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *****************************************************************************************************************************************************************************************************************
ok: [vector-01]

TASK [Get tar] *************************************************************************************************************************************************************************************************************************
ok: [vector-01]

TASK [Extract archive] *****************************************************************************************************************************************************************************************************************
ok: [vector-01]

TASK [Configure] ***********************************************************************************************************************************************************************************************************************
--- before: /opt/vector/vector-x86_64-unknown-linux-gnu/config/vector.toml
+++ after: /home/gkublock/.ansible/tmp/ansible-local-8940cg9r5y6o/tmpz2i5j5ws/vector.toml
@@ -1,44 +1,7 @@
-#                                    __   __  __
-#                                    \ \ / / / /
-#                                     \ V / / /
-#                                      \_/  \/
-#
-#                                    V E C T O R
-#                                   Configuration
-#
-# ------------------------------------------------------------------------------
-# Website: https://vector.dev
-# Docs: https://vector.dev/docs
-# Chat: https://chat.vector.dev
-# ------------------------------------------------------------------------------
+[sources.in]
+type = "stdin"
 
-# Change this to use a non-default directory for Vector data storage:
-# data_dir = "/var/lib/vector"
-
-# Random Syslog-formatted logs
-[sources.dummy_logs]
-type = "demo_logs"
-format = "syslog"
-interval = 1
-
-# Parse Syslog logs
-# See the Vector Remap Language reference for more info: https://vrl.dev
-[transforms.parse_logs]
-type = "remap"
-inputs = ["dummy_logs"]
-source = '''
-. = parse_syslog!(string!(.message))
-'''
-
-# Print parsed logs to stdout
-[sinks.print]
+[sinks.out]
+inputs = ["in"]
 type = "console"
-inputs = ["parse_logs"]
-encoding.codec = "json"
-
-# Vector's GraphQL API (disabled by default)
-# Uncomment to try it out with the `vector top` command or
-# in your browser at http://localhost:8686
-#[api]
-#enabled = true
-#address = "127.0.0.1:8686"
+encoding.codec = "text"

changed: [vector-01]

PLAY RECAP *****************************************************************************************************************************************************************************************************************************
vector-01                  : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   


9. Подготовьте README.md файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.
10. Готовый playbook выложите в свой репозиторий, поставьте тег `08-ansible-02-playbook` на фиксирующий коммит, в ответ предоставьте ссылку на него.

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
