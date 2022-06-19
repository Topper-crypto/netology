# Домашнее задание к занятию "6.6. Troubleshooting"
### Задача 1
> Перед выполнением задания ознакомьтесь с документацией по администрированию MongoDB.
> 
> Пользователь (разработчик) написал в канал поддержки, что у него уже 3 минуты происходит CRUD операция в MongoDB и её нужно прервать.
> 
> Вы как инженер поддержки решили произвести данную операцию:
> 
> * напишите список операций, которые вы будете производить для остановки запроса пользователя
> * предложите вариант решения проблемы с долгими (зависающими) запросами в MongoDB
### Ответ:

Определим текущую операци командой:  
       ```db.currentOp()```
Завершим операцию по opid
       ```db.killOp()```

Чтобы не было долгих запросов, необходимо постороить/перестроить соответствующий индекс.

### Задача 2
> Перед выполнением задания познакомьтесь с документацией по Redis latency troobleshooting.
> 
> Вы запустили инстанс Redis для использования совместно с сервисом, который использует механизм TTL. Причем отношение количества записанных key-value значений к количеству истёкших значений есть величина постоянная и увеличивается пропорционально количеству реплик сервиса.
> 
> При масштабировании сервиса до N реплик вы увидели, что:
> 
> * сначала рост отношения записанных значений к истекшим
> * Redis блокирует операции записи
> 
> Как вы думаете, в чем может быть проблема?

### Ответ:

Проблема связана с исчерпанием ресурсов оперативной памяти

### Задача 3
> Перед выполнением задания познакомьтесь с документацией по Common Mysql errors.
> 
> Вы подняли базу данных MySQL для использования в гис-системе. При росте количества записей, в таблицах базы, пользователи начали жаловаться на ошибки вида:
> 
```
InterfaceError: (InterfaceError) 2013: Lost connection to MySQL server during query u'SELECT..... '
```
> Как вы думаете, почему это начало происходить и как локализовать проблему?
> 
> Какие пути решения данной проблемы вы можете предложить?

### Ответ:

Предположительно ошибки начали возникать из-за роста нагрузки. 

В качестве решения можно предложить:

- Увеличить ресурсы машины 
- Увеличить значение параметров : connect_timeout, interactive_timeout, wait_timeout
- Создать индексы для ускорения запросов
- Увеличить net_read_timeout при возникновении сетевых сбоев 

### Задача 4
> Перед выполнением задания ознакомтесь со статьей Common PostgreSQL errors из блога Percona.
> 
> Вы решили перевести гис-систему из задачи 3 на PostgreSQL, так как прочитали в документации, что эта СУБД работает с большим объемом данных лучше, чем MySQL.
> 
> После запуска пользователи начали жаловаться, что СУБД время от времени становится недоступной. В dmesg вы видите, что:
```
postmaster invoked oom-killer
```
> Как вы думаете, что происходит?
> 
> Как бы вы решили данную проблему?

### Ответ:

Причина в недостатке ресурсов оперативной памяти, в результате ОС завершает процессы утилизирующие память, чтобы предотвратить падение всей системы.

Для предотвращения сбоев необходимо выставить ограничение в настройках PostgreSQL на использование ресурсов хоста, 
чтобы оптимизировать потребление ресурсов на машине, также желательно увеличить объем оперативной памяти.