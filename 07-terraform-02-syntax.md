# Домашнее задание к занятию "7.2. Облачные провайдеры и синтаксис Terraform."
> Зачастую разбираться в новых инструментах гораздо интересней понимая то, как они работают изнутри. Поэтому в рамках первого необязательного задания предлагается завести свою учетную запись в AWS (Amazon Web Services) или Yandex.Cloud. Идеально будет познакомится с обоими облаками, потому что они отличаются.

### Задача 1 (Вариант с Yandex.Cloud). Регистрация в ЯО и знакомство с основами (необязательно, но крайне желательно).
> 1. Подробная инструкция на русском языке содержится здесь.
> 2. Обратите внимание на период бесплатного использования после регистрации аккаунта.
> 3. Используйте раздел "Подготовьте облако к работе" для регистрации аккаунта. Далее раздел "Настройте провайдер" для подготовки базового терраформ конфига.
> 4. Воспользуйтесь инструкцией на сайте терраформа, что бы не указывать авторизационный токен в коде, а терраформ провайдер брал его из переменных окружений.

### Ответ:

Что бы не указывать авторизационный токен в коде:
```
yc iam create-token
```
```
export IAM_TOKEN=`yc iam create-token`
curl -H "Authorization: Bearer ${IAM_TOKEN}" \
  https://resource-manager.api.cloud.yandex.net/resource-manager/v1/clouds
```

### Задача 2. Создание yandex_compute_instance через терраформ.

> 1. В каталоге ```terraform``` вашего основного репозитория, который был создан в начале курсе, создайте файл ```main.tf``` и ```versions.tf```.
> 2. Зарегистрируйте провайдер
> 
> 2.1. для aws. В файл ```main.tf``` добавьте блок provider, а в versions.tf блок terraform с вложенным блоком ```required_providers```. Укажите любой выбранный вами регион внутри блока ```provider```.
> 
> 2.2. либо для yandex.cloud. Подробную инструкцию можно найти здесь.
> 
> 3. Внимание! В гит репозиторий нельзя пушить ваши личные ключи доступа к аккаунту. Поэтому в предыдущем задании мы указывали их в виде переменных окружения.
> 4. В файле ```main.tf``` воспользуйтесь блоком ```data "aws_ami``` для поиска ami образа последнего Ubuntu.
> 5. В файле ```main.tf``` создайте рессурс
> 
> 5.1. либо ec2 instance. Постарайтесь указать как можно больше параметров для его определения. Минимальный набор параметров указан в первом блоке ```Example Usage```, но желательно, указать большее количество параметров.
> 
> 5.2. либо yandex_compute_image.
> 
> 6. Также в случае использования aws:
> 
> 6.1. Добавьте data-блоки ```aws_caller_identity``` и ```aws_region```.
> 
> 6.2. В файл ```outputs.tf``` поместить блоки output с данными об используемых в данный момент:
> 
> * AWS account ID,
> * AWS user ID,
> * AWS регион, который используется в данный момент,
> * Приватный IP ec2 инстансы,
> * Идентификатор подсети в которой создан инстанс.
> 7. Если вы выполнили первый пункт, то добейтесь того, что бы команда ```terraform plan выполнялась без ошибок.
> 
> В качестве результата задания предоставьте:
> 
> Ответ на вопрос: при помощи какого инструмента (из разобранных на прошлом занятии) можно создать свой образ ami?
> 
> Ссылку на репозиторий с исходной конфигурацией терраформа.
