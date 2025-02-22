# Домашнее задание к занятию "1. Введение в Ansible"

## Подготовка к выполнению
1. Установите ansible версии 2.10 или выше.
[gkublock@fedora mnt-homeworks]$ ansible --version
ansible [core 2.14.1]
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/gkublock/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3.11/site-packages/ansible
  ansible collection location = /home/gkublock/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/bin/ansible
  python version = 3.11.1 (main, Jan  6 2023, 00:00:00) [GCC 12.2.1 20221121 (Red Hat 12.2.1-4)] (/usr/bin/python3)
  jinja version = 3.0.3
  libyaml = True

2. Создайте свой собственный публичный репозиторий на github с произвольным именем.  https://github.com/Gkublock/mnt-homeworks
3. Скачайте [playbook](./playbook/) из репозитория с домашним заданием и перенесите его в свой репозиторий.

## Основная часть
1. Попробуйте запустить playbook на окружении из `test.yml`, зафиксируйте какое значение имеет факт `some_fact` для указанного хоста при выполнении playbook'a.
TASK [Print fact] **********************************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": 12
}



2. Найдите файл с переменными (group_vars) в котором задаётся найденное в первом пункте значение и поменяйте его на 'all default fact'.
3. Воспользуйтесь подготовленным (используется `docker`) или создайте собственное окружение для проведения дальнейших испытаний.
4. Проведите запуск playbook на окружении из `prod.yml`. Зафиксируйте полученные значения `some_fact` для каждого из `managed host`.

TASK [Print fact] *****************************************************************************************************************************************************
ok: [centos] => {
    "msg": "el"
}
ok: [loving_sinoussi] => {
    "msg": "deb"
}


5. Добавьте факты в `group_vars` каждой из групп хостов так, чтобы для `some_fact` получились следующие значения: для `deb` - 'deb default fact', для `el` - 'el default fact'.


6.  Повторите запуск playbook на окружении `prod.yml`. Убедитесь, что выдаются корректные значения для всех хостов.

TASK [Print fact] *****************************************************************************************************************************************************
ok: [centos] => {
    "msg": "el default fac"
}
ok: [loving_sinoussi] => {
    "msg": "deb default fac"
}


7. При помощи `ansible-vault` зашифруйте факты в `group_vars/deb` и `group_vars/el` с паролем `netology`.  
8. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь в работоспособности.
[gkublock@fedora playbook]$ ansible-playbook -i inventory/prod.yml --ask-vault-password -c docker site.yml 
Vault password: 

PLAY [Print os facts] *************************************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************************************
ok: [loving_sinoussi]
ok: [centos]

TASK [Print OS] *******************************************************************************************************************************************************
ok: [centos] => {
    "msg": "CentOS"
}
ok: [loving_sinoussi] => {
    "msg": "Ubuntu"
}

TASK [Print fact] *****************************************************************************************************************************************************
ok: [centos] => {
    "msg": "el default fac"
}
ok: [loving_sinoussi] => {
    "msg": "deb default fac"
}

PLAY RECAP ************************************************************************************************************************************************************
centos                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
loving_sinoussi            : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   


9. Посмотрите при помощи `ansible-doc` список плагинов для подключения. Выберите подходящий для работы на `control node`. ansible-doc -t inventory -l
10. В `prod.yml` добавьте новую группу хостов с именем  `local`, в ней разместите localhost с необходимым типом подключения.
11. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь что факты `some_fact` для каждого из хостов определены из верных `group_vars`.
[gkublock@fedora playbook]$ ansible-playbook -i inventory/prod.yml -c docker --ask-vault-password -k site.yml
SSH password: 
Vault password: 

PLAY [Print os facts] *************************************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************************************
ok: [loving_sinoussi]
ok: [centos]
ok: [localhost]

TASK [Print OS] *******************************************************************************************************************************************************
ok: [centos] => {
    "msg": "CentOS"
}
ok: [loving_sinoussi] => {
    "msg": "Ubuntu"
}
ok: [localhost] => {
    "msg": "Fedora"
}

TASK [Print fact] *****************************************************************************************************************************************************
ok: [centos] => {
    "msg": "el default fac"
}
ok: [loving_sinoussi] => {
    "msg": "deb default fac"
}
ok: [localhost] => {
    "msg": "all default fact"
}

PLAY RECAP ************************************************************************************************************************************************************
centos                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
loving_sinoussi            : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

12. Заполните `README.md` ответами на вопросы. Сделайте `git push` в ветку `master`. В ответе отправьте ссылку на ваш открытый репозиторий с изменённым `playbook` и заполненным `README.md`.

## Необязательная часть

1. При помощи `ansible-vault` расшифруйте все зашифрованные файлы с переменными.
2. Зашифруйте отдельное значение `PaSSw0rd` для переменной `some_fact` паролем `netology`. Добавьте полученное значение в `group_vars/all/exmp.yml`.
3. Запустите `playbook`, убедитесь, что для нужных хостов применился новый `fact`.
4. Добавьте новую группу хостов `fedora`, самостоятельно придумайте для неё переменную. В качестве образа можно использовать [этот](https://hub.docker.com/r/pycontribs/fedora).
5. Напишите скрипт на bash: автоматизируйте поднятие необходимых контейнеров, запуск ansible-playbook и остановку контейнеров.
6. Все изменения должны быть зафиксированы и отправлены в вашей личный репозиторий.

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
