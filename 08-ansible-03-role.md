# Домашнее задание к занятию "08.03 Работа с Roles"
### Подготовка к выполнению
1. Создайте два пустых публичных репозитория в любом своём проекте: elastic-role и kibana-role.
2. Скачайте role из репозитория с домашним заданием и перенесите его в свой репозиторий elastic-role.
3. Скачайте дистрибутив java и положите его в директорию `playbook/files/`.
4. Установите molecule: pip3 install molecule
5. Добавьте публичную часть своего ключа к своему профилю в github.
### Основная часть

Наша основная цель - разбить наш playbook на отдельные roles. Задача: сделать roles для elastic, kibana и написать playbook для использования этих ролей. Ожидаемый результат: существуют два ваших репозитория с roles и один репозиторий с playbook.

1. Создать в старой версии playbook файл `requirements.yml` и заполнить его следующим содержимым:
```
---
  - src: git@github.com:netology-code/mnt-homeworks-ansible.git
    scm: git
    version: "1.0.1"
    name: java 
```
2. При помощи `ansible-galaxy` скачать себе эту роль. Запустите `molecule test`, посмотрите на вывод команды.
3. Перейдите в каталог с ролью elastic-role и создайте сценарий тестирования по умолчаню при помощи `molecule init scenario --driver-name docker`.
4. Добавьте несколько разных дистрибутивов (centos:8, ubuntu:latest) для инстансов и протестируйте роль, исправьте найденные ошибки, если они есть.
5. Создайте новый каталог с ролью при помощи `molecule init role --driver-name docker kibana-role`. Можете использовать другой драйвер, который более удобен вам.
6. На основе tasks из старого playbook заполните новую role. Разнесите переменные между `vars` и `default`. Проведите тестирование на разных дистрибитивах (centos:7, centos:8, ubuntu).
7. Выложите все roles в репозитории. Проставьте тэги, используя семантическую нумерацию.
8. Добавьте roles в `requirements.yml` в playbook.
9. Переработайте playbook на использование roles.
10. Выложите playbook в репозиторий.
11. В ответ приведите ссылки на оба репозитория с roles и одну ссылку на репозиторий с playbook.
### Необязательная часть
1. Проделайте схожие манипуляции для создания роли logstash.
2. Создайте дополнительный набор tasks, который позволяет обновлять стек ELK.
3. В ролях добавьте тестирование в раздел `verify.yml`. Данный раздел должен проверять, что elastic запущен и возвращает успешный статус по API, web-интерфейс kibana отвечает без кодов ошибки, `logstash через команду logstash -e 'input { stdin { } } output { stdout {} }'`.
4. Убедитесь в работоспособности своего стека. Возможно, потребуется тестировать все роли одновременно.
5. Выложите свои roles в репозитории. В ответ приведите ссылки.

### Решение:

1. Файл создан
2. Роль скачана
```yaml
topper@netology:~/netology/ansible_roles/playbook$ ansible-galaxy install -r requirements.yml --roles-path ./
- extracting java to /home/topper/netology/ansible_roles/playbook/java
- java (1.0.1) was installed successfully
```
```yaml
topper@netology:~/netology/ansible_roles/playbook/java$ molecule test
INFO     default scenario test matrix: dependency, lint, cleanup, destroy, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO     Performing prerun...
WARNING  Computed fully qualified role name of java does not follow current galaxy requirements.
Please edit meta/main.yml and assure we can correctly determine full role name:

galaxy_info:
role_name: my_name  # if absent directory name hosting role is used instead
namespace: my_galaxy_namespace  # if absent, author is used instead

Namespace: https://galaxy.ansible.com/docs/contributing/namespaces.html#galaxy-namespace-limitations
Role: https://galaxy.ansible.com/docs/contributing/creating_role.html#role-names

As an alternative, you can add 'role-name' to either skip_list or warn_list.

INFO     Using ../../../../../.cache/roles/java symlink to current repository in order to enable Ansible to find the role using its expected full name.

<skip some data>

TASK [Delete docker network(s)] ****************************************************************************************************************

PLAY RECAP *************************************************************************************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Pruning extra files from scenario ephemeral directory
```
3. Сценарии созданы
```yaml
topper@netology:~/netology/ansible_roles/elastic-role$ molecule init scenario -d docker
INFO     Initializing new scenario default...
INFO     Initialized scenario in /home/topper/netology/ansible_roles/elastic-role/molecule/default successfully.
```
4. Добавлены дистрибутивы Ubuntu:latest, centors:7, 8
```yaml
topper@netology:~/netology/ansible_roles/elastic-role$ molecule test
INFO     default scenario test matrix: dependency, lint, cleanup, destroy, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO     Performing prerun...
WARNING  Computed fully qualified role name of elastic-role does not follow current galaxy requirements.
Please edit meta/main.yml and assure we can correctly determine full role name:

<skip some data>

PLAY [Converge] ********************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************
ok: [ubuntu]
ok: [centos8]
ok: [centos7]

<skip some data>

PLAY RECAP *************************************************************************************************************************************
centos7                    : ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
centos8                    : ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

<skip some data>

PLAY RECAP *************************************************************************************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Pruning extra files from scenario ephemeral directory
```
5. Создана новая роли kibana_role
```yaml
topper@netology:~/netology/ansible_roles$ molecule init role -d docker kibana-role
INFO     Initializing new role kibana-role...
Using /etc/ansible/ansible.cfg as config file
- Role kibana-role was created successfully
INFO     Initialized role in /home/topper/netology/ansible_roles/kibana-role successfully.
```
Тестирование роли
```yaml
topper@netology:~/netology/ansible_roles/kibana-role$ molecule test
INFO     default scenario test matrix: dependency, lint, cleanup, destroy, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO     Performing prerun...
WARNING  Computed fully qualified role name of kibana-role does not follow current galaxy requirements.
Please edit meta/main.yml and assure we can correctly determine full role name:

<skip some data>

PLAY [Converge] ********************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************
ok: [centos7]
ok: [centos8]
ok: [ubuntu]

TASK [Include kibana-role] ******************************************************************************************************************

TASK [kibana-role : Upload tar.gz Kibana from remote URL] ***********************************************************************************
changed: [centos8]
changed: [ubuntu]
changed: [centos7]

TASK [kibana-role : Create directrory for Kibana (/opt/kibana/7.16.3)] **********************************************************************
changed: [centos7]
changed: [ubuntu]
changed: [centos8]

TASK [kibana-role : Extract Kibana in the installation directory] ***************************************************************************
changed: [ubuntu]
changed: [centos8]
changed: [centos7]

TASK [kibana-role : Set environment Kibana] *************************************************************************************************
changed: [ubuntu]
changed: [centos7]
changed: [centos8]

PLAY RECAP *************************************************************************************************************************************
centos7                    : ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
centos8                    : ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

<skip some data>

INFO     Idempotence completed successfully.
INFO     Running default > side_effect
WARNING  Skipping, side effect playbook not configured.
INFO     Running default > verify
INFO     Running Ansible Verifier

PLAY [Verify] **********************************************************************************************************************************

TASK [Example assertion] ***********************************************************************************************************************
ok: [centos8] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [centos7] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [ubuntu] => {
    "changed": false,
    "msg": "All assertions passed"
}

PLAY RECAP *************************************************************************************************************************************
centos7                    : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
centos8                    : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Verifier completed successfully.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy

PLAY [Destroy] *********************************************************************************************************************************

TASK [Destroy molecule instance(s)] ************************************************************************************************************
changed: [localhost] => (item=centos8)
changed: [localhost] => (item=centos7)
changed: [localhost] => (item=ubuntu)

TASK [Wait for instance(s) deletion to complete] ***********************************************************************************************
FAILED - RETRYING: Wait for instance(s) deletion to complete (300 retries left).
FAILED - RETRYING: Wait for instance(s) deletion to complete (299 retries left).
FAILED - RETRYING: Wait for instance(s) deletion to complete (298 retries left).
changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '914470095176.89836', 'results_file': '/home/topper/.ansible_async/914470095176.89836', 'changed': True, 'failed': False, 'item': {'image': 'docker.io/pycontribs/centos:8', 'name': 'centos8', 'pre_build_image': True}, 'ansible_loop_var': 'item'})
changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '48492610677.89861', 'results_file': '/home/topper/.ansible_async/48492610677.89861', 'changed': True, 'failed': False, 'item': {'image': 'docker.io/pycontribs/centos:7', 'name': 'centos7', 'pre_build_image': True}, 'ansible_loop_var': 'item'})
changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '604199252728.89886', 'results_file': '/home/topper/.ansible_async/604199252728.89886', 'changed': True, 'failed': False, 'item': {'image': 'docker.io/pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True}, 'ansible_loop_var': 'item'})

TASK [Delete docker network(s)] ****************************************************************************************************************

PLAY RECAP *************************************************************************************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Pruning extra files from scenario ephemeral directory
```
6. Разнесены переменные между `vars` и `default`

7. Репозетории выложены

8. Добавлены roles в `requirements.yml` в playbook
```yaml
---
  - src: git@github.com:Topper-crypto/kibana-role.git
    scm: git
    version: "1.0.1"
    name: kibana-role 
  - src: git@github.com:Topper-crypto/elastic-role.git
    scm: git
    version: "1.0.1"
    name: elastic-role
  - src: git@github.com:Topper-crypto/java-role.git
    scm: git
    version: "1.0.1"
    name: java-role 
```
9. playbook переработан на использование roles
```yaml
/netology/ansible_roles/playbook$ ansible-playbook -i inventory/prod.yml site.yml

PLAY [all] *************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************
ok: [elastic]
ok: [kibana]

TASK [java-role : Upload .tar.gz file containing binaries from local storage] ******************************************************************
changed: [elastic]
changed: [kibana]

TASK [java-role : Upload .tar.gz file conaining binaries from remote storage] ******************************************************************
skipping: [elastic]
skipping: [kibana]

TASK [java-role : Ensure installation dir exists] **********************************************************************************************
changed: [elastic]
changed: [kibana]

TASK [java-role : Extract java in the installation directory] **********************************************************************************
changed: [elastic]
changed: [kibana]

TASK [java-role : Export environment variables] ************************************************************************************************
changed: [elastic]
changed: [kibana]

PLAY [elasticsearch] ***************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************
ok: [elastic]

TASK [elastic-role : Upload tar.gz Elasticsearch from remote URL] ******************************************************************************
changed: [elastic]

TASK [elastic-role : Create directrory for Elasticsearch] **************************************************************************************
changed: [elastic]

TASK [elastic-role : Extract Elasticsearch in the installation directory] **********************************************************************
changed: [elastic]

TASK [elastic-role : Set environment Elastic] **************************************************************************************************
changed: [elastic]

PLAY [kibana] **********************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************
ok: [kibana]

TASK [kibana-role : Upload tar.gz Kibana from remote URL] **************************************************************************************
changed: [kibana]

TASK [kibana-role : Create directrory for Kibana (/opt/kibana/7.16.3)] *************************************************************************
changed: [kibana]

TASK [kibana-role : Extract Kibana in the installation directory] ******************************************************************************
changed: [kibana]

TASK [kibana-role : Set environment Kibana] ****************************************************************************************************
changed: [kibana]

PLAY RECAP *************************************************************************************************************************************
elastic                 : ok=10   changed=8    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
kibana                  : ok=10   changed=8    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
```

10. playbook выложен в репозиторий
11. Ссылки на репозитории

https://github.com/Topper-crypto/Playbook

https://github.com/Topper-crypto/java-role

https://github.com/Topper-crypto/kibana-role

https://github.com/Topper-crypto/elastic-role
