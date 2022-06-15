# Домашнее задание к занятию "6.5. Elasticsearch"

### Задача 1

> В этом задании вы потренируетесь в:
> - установке elasticsearch
> - первоначальном конфигурировании elastcisearch
> - запуске elasticsearch в docker
> 
> Используя докер образ [elasticsearch:7](https://hub.docker.com/_/elasticsearch) как базовый:
> 
> - составьте Dockerfile-манифест для elasticsearch
> - соберите docker-образ и сделайте `push` в ваш docker.io репозиторий
> - запустите контейнер из получившегося образа и выполните запрос пути `/` c хост-машины
> 
> Требования к `elasticsearch.yml`:
> - данные `path` должны сохраняться в `/var/lib` 
> - имя ноды должно быть `netology_test`
> 
> В ответе приведите:
> - текст Dockerfile манифеста
> - ссылку на образ в репозитории dockerhub
> - ответ `elasticsearch` на запрос пути `/` в json виде
> 
> Подсказки:
> - при сетевых проблемах внимательно изучите кластерные и сетевые настройки в elasticsearch.yml
> - при некоторых проблемах вам поможет docker директива ulimit
> - elasticsearch в логах обычно описывает проблему и пути ее решения
> - обратите внимание на настройки безопасности такие как `xpack.security.enabled` 
> - если докер образ не запускается и падает с ошибкой 137 в этом случае может помочь настройка `-e ES_HEAP_SIZE`
> - при настройке `path` возможно потребуется настройка прав доступа на директорию
> 
> Далее мы будем работать с данным экземпляром elasticsearch.

### Решение:

![](https://github.com/Topper-crypto/netology/blob/main/assets/elastic.png)

> ## Задача 2
> 
> В этом задании вы научитесь:
> - создавать и удалять индексы
> - изучать состояние кластера
> - обосновывать причину деградации доступности данных
> 
> Ознакомтесь с [документацией](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-create-index.html) и добавьте в `elasticsearch` 3 индекса, в соответствии со таблицей:
> 
| Имя | Количество реплик | Количество шард |
|-----|-------------------|-----------------|
| ind-1| 0 | 1 |
| ind-2 | 1 | 2 |
| ind-3 | 2 | 4 |
> 
> Получите список индексов и их статусов, используя API и **приведите в ответе** на задание.
> 
> Получите состояние кластера `elasticsearch`, используя API.
> 
> Как вы думаете, почему часть индексов и кластер находится в состоянии yellow?
> 
> Удалите все индексы.
> 
> **Важно**
> 
> При проектировании кластера elasticsearch нужно корректно рассчитывать количество реплик и шард, иначе возможна потеря данных индексов, вплоть до полной, при деградации системы.

### Решение:

Добавление индексов
```
curl -XPUT http://localhost:9200/ind-1 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 1,  "number_of_replicas": 0 }}'
```
```
curl -XPUT http://localhost:9200/ind-2 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 2,  "number_of_replicas": 1 }}'
```
```
curl -XPUT http://localhost:9200/ind-3 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 4,  "number_of_replicas": 2 }}'
```
Cписок индексов и их статусов:
![](https://github.com/Topper-crypto/netology/blob/main/assets/elastic_2.png)

Cостояние кластера elasticsearch:
![](https://github.com/Topper-crypto/netology/blob/main/assets/elastic_3.png)

Удаление индексов
```
curl -XDELETE http://localhost:9200/ind-1
```
```
curl -XDELETE http://localhost:9200/ind-2
```
```
curl -XDELETE http://localhost:9200/ind-3
```
![](https://github.com/Topper-crypto/netology/blob/main/assets/elastic_4.png)

### Ответ:
В настройках индекса указано количество реплик. Так как кластер однонодовый, реплицировать некуда. Кластер таким образом уведомляет о том, что заданные условия не работают в полной мере.

## Задача 3

> В данном задании вы научитесь:
> - создавать бэкапы данных
> - восстанавливать индексы из бэкапов
> 
> Создайте директорию `{путь до корневой директории с elasticsearch в образе}/snapshots`.
> 
> Используя API [зарегистрируйте](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-register-repository.html#snapshots-register-repository) данную директорию как `snapshot repository` c именем `netology_backup`.
> 
> **Приведите в ответе** запрос API и результат вызова API для создания репозитория.
> 
> Создайте индекс `test` с 0 реплик и 1 шардом и **приведите в ответе** список индексов.
> 
> [Создайте `snapshot`](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-take-snapshot.html) состояния кластера `elasticsearch`.
> 
> **Приведите в ответе** список файлов в директории со `snapshot`ами.
> 
> Удалите индекс `test` и создайте индекс `test-2`. **Приведите в ответе** список индексов.
> 
> [Восстановите](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-restore-snapshot.html) состояние кластера `elasticsearch` из `snapshot`, созданного ранее. 
> 
> **Приведите в ответе** запрос к API восстановления и итоговый список индексов.
> 
> Подсказки:
> - возможно вам понадобится доработать `elasticsearch.yml` в части директивы `path.repo` и перезапустить `elasticsearch`

### Решение:

Пересобираем образ с необходимой папкой
```curl -XPUT http://localhost:9200/_snapshot/netology_backup?pretty -H 'content-type: application/json' -d'{ "type": "fs", "settings": { "location": "/usr/elasticsearch/elasticsearch-7.17.3/bin/snapshots"}}'
```
```
    FROM topper80/elastic:1.0

    USER root

    RUN mkdir /usr/elasticsearch/elasticsearch-7.17.3/bin/snapshots && \
        chown -R elasticsearch:elasticsearch /usr/elasticsearch/elasticsearch-7.17.3/bin/snapshots && \
        echo "path.repo: /usr/elasticsearch/elasticsearch-7.17.3/bin/snapshots" >> /usr/elasticsearch/elasticsearch-7.17.3/config/elasticsearch.yml

    USER elasticsearch
```
Используя API зарегистрируем  директорию как snapshot repository c именем netology_backup
```
curl -XPUT http://localhost:9200/_snapshot/netology_backup?pretty -H 'content-type: application/json' -d'{ "type": "fs", "settings": { "location": "/usr/elasticsearch/elasticsearch-7.17.3/bin/snapshots"}}'

{
  "acknowledged" : true
}

curl -XGET http://localhost:9200/_snapshot/netology_backup?pretty

{
  "netology_backup" : {
    "type" : "fs",
    "settings" : {
      "location" : "/usr/elasticsearch/elasticsearch-7.17.3/bin/snapshots"
    }
  }
}

```
Создаём индекс test с 0 реплик и 1 шардом
```
curl -XPUT http://localhost:9200/test -H 'Content-Type: application/json' -d'{ "settings": { "number_of_sha
rds": 1,  "number_of_replicas": 0 }}'
```
```
curl -XGET 'http://localhost:9200/_cat/indices?v'

health status index            uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   .geoip_databases AX9pLLuVTaWYC0KZQAhSkg   1   0         41            0     38.8mb         38.8mb
green  open   test             qp8R_5vETX6uzoPtXNW9zA   1   0          0            0       226b           226b
```

Создаём snapshot состояния кластера elasticsearch
```
curl -XPUT http://localhost:9200/_snapshot/netology_backup/first_snapshot?wait_for_completion=true

{"snapshot":{"snapshot":"first_snapshot","uuid":"cqIIRM4YQ0OTK5GwQg8h9g","repository":"netology_backup","version_id":7170399,"version":"7.17.3","indices":[".ds-.logs-deprecation.elasticsearch-default-2022.06.09-000001","test",".ds-ilm-history-5-2022.06.09-000001",".geoip_databases"],"data_streams":["ilm-history-5",".logs-deprecation.elasticsearch-default"],"include_global_state":true,"state":"SUCCESS","start_time":"2022-06-09T11:03:18.463Z","start_time_in_millis":1654772598463,"end_time":"2022-06-09T11:03:19.664Z","end_time_in_millis":1654772599664,"duration_in_millis":1201,"failures":[],"shards":{"total":4,"failed":0,"successful":4},"feature_states":[{"feature_name":"geoip","indices":[".geoip_databases"]}]}}
```
```
curl -XGET 'http://localhost:9200/_snapshot?pretty'
{
  "netology_backup" : {
    "type" : "fs",
    "uuid" : "NRoWvyegRgqOkw2pgaNZcw",
    "settings" : {
      "location" : "/usr/elasticsearch/elasticsearch-7.17.3/bin/snapshots"
    }
  }
}
```
Cписок файлов в директории со snapshotами
```
ll /usr/elasticsearch/elasticsearch-7.17.3/bin/snapshots
total 48
-rw-r--r-- 1 elasticsearch elasticsearch  1426 Jun  9 11:03 index-0
-rw-r--r-- 1 elasticsearch elasticsearch     8 Jun  9 11:03 index.latest
drwxr-xr-x 6 elasticsearch elasticsearch  4096 Jun  9 11:03 indices
-rw-r--r-- 1 elasticsearch elasticsearch 29251 Jun  9 11:03 meta-cqIIRM4YQ0OTK5GwQg8h9g.dat
-rw-r--r-- 1 elasticsearch elasticsearch   713 Jun  9 11:03 snap-cqIIRM4YQ0OTK5GwQg8h9g.dat
```
Удаляем индекс test и создайте индекс test-2
```
curl -XGET 'http://localhost:9200/_cat/indices?v'
health status index            uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   .geoip_databases AX9pLLuVTaWYC0KZQAhSkg   1   0         41            0     38.8mb         38.8mb
green  open   test-2           mFpoqhi0SpC2KHRPejd04g   1   0          0            0       226b           226b
```
Восстанавливаем состояние кластера elasticsearch из snapshot, созданного ранее.

```
curl -XPOST localhost:9200/_snapshot/netology_backup/first_snapshot/_restore?pretty -H 'content-type: application/json' -d'{"indices": "test"}'
```
```
curl -XGET 'http://localhost:9200/_cat/indices?v'
health status index            uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   .geoip_databases AX9pLLuVTaWYC0KZQAhSkg   1   0         41            0     38.8mb         38.8mb
green  open   test-2           mFpoqhi0SpC2KHRPejd04g   1   0          0            0       226b           226b
green  open   test             jc74Le3ASEWme4SpZjLdRw   1   0          0            0       226b           226b
```
