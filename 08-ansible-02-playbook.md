# Домашнее задание к занятию "08.02 Работа с Playbook"
### Подготовка к выполнению
1. Создайте свой собственный (или используйте старый) публичный репозиторий на github с произвольным именем.
2. Скачайте playbook из репозитория с домашним заданием и перенесите его в свой репозиторий.
3. Подготовьте хосты в соотвтествии с группами из предподготовленного playbook.
4. Скачайте дистрибутив java и положите его в директорию `playbook/files/`.
### Основная часть
1. Приготовьте свой собственный inventory файл `prod.yml`.
2. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает `kibana`.
3. При создании tasks рекомендую использовать модули: `get_url`, `template`, `unarchive`, `file`.
4. Tasks должны: скачать нужной версии дистрибутив, выполнить распаковку в выбранную директорию, сгенерировать конфигурацию с параметрами.
5. Запустите `ansible-lint site.yml` и исправьте ошибки, если они есть.
6. Попробуйте запустить `playbook` на этом окружении с флагом `--check`.
7. Запустите playbook на `prod.yml` окружении с флагом `--diff`. Убедитесь, что изменения на системе произведены.
8. Повторно запустите `playbook` с флагом `--diff` и убедитесь, что playbook идемпотентен.
9. Подготовьте README.md файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.
10. Готовый `playbook` выложите в свой репозиторий, в ответ предоставьте ссылку на него.
### Необязательная часть
1. Приготовьте дополнительный хост для установки logstash.
2. Пропишите данный хост в prod.yml в новую группу logstash.
3. Дополните playbook ещё одним play, который будет исполнять установку logstash только на выделенный для него хост.
4. Все переменные для нового play определите в отдельный файл group_vars/logstash/vars.yml.
5. Logstash конфиг должен конфигурироваться в части ссылки на elasticsearch (можно взять, например его IP из facts или определить через vars).
6. Дополните README.md, протестируйте playbook, выложите новую версию в github. В ответ предоставьте ссылку на репозиторий.
### Решение:

Запуск `playbook` с флагом `--check`

```yml
PLAY [Install Java] **********************************************************************************************************
TASK [Gathering Facts] *******************************************************************************************************
ok: [elastic]

TASK [Set facts for Java 11 vars] ********************************************************************************************
ok: [elastic]
ok: [kibana]

TASK [Upload .tar.gz file containing binaries from local storage] ************************************************************
changed: [kibana]
changed: [elastic]

TASK [Ensure installation dir exists] ****************************************************************************************
changed: [elastic]
changed: [kibana]

TASK [Extract java in the installation directory] ****************************************************************************
An exception occurred during task execution. To see the full traceback, use -vvv. The error was: NoneType: None
fatal: [elastic]: FAILED! => {"changed": false, "msg": "dest '/opt/jdk/11.0.16' must be an existing dir"}
An exception occurred during task execution. To see the full traceback, use -vvv. The error was: NoneType: None
fatal: [kibana]: FAILED! => {"changed": false, "msg": "dest '/opt/jdk/11.0.16' must be an existing dir"}

PLAY RECAP *******************************************************************************************************************
elastic                    : ok=4    changed=2    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0   
kibana                     : ok=4    changed=2    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0
```

Запуск `playbook` с флагом `--diff`
```yml
...
PLAY RECAP *******************************************************************************************************************
elastic                 : ok=11   changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
kibana                  : ok=11   changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```

Повторный запуск `playbook` с флагом `--diff`
```yml
...
PLAY RECAP *******************************************************************************************************************
elastic                 : ok=9    changed=0    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0   
kibana                  : ok=9    changed=0    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0   
```

Запуск `playbook` с дополнительным заданием
```yml
...
PLAY RECAP ********************************************************************************************************************
elastic                    : ok=11   changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
kibana                     : ok=11   changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
localhost                  : ok=5    changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
logstash                   : ok=11   changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```

[Описание playbook](https://github.com/Topper-crypto/netology/blob/main/playbook/README.md) 

[playbook](https://github.com/Topper-crypto/netology/tree/main/playbook)
