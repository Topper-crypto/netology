# Самоконтроль выполненения задания

1. Где расположен файл с some_fact из второго пункта задания?

group_vars/all/examp.yml

Какая команда нужна для запуска вашего playbook на окружении test.yml?

ansible-playbook -i ./invetory/test.yml site.yml

Какой командой можно зашифровать файл?

ansible-vault encrypt data

Какой командой можно расшифровать файл?

ansible-vault decrypt data

Можно ли посмотреть содержимое зашифрованного файла без команды расшифровки файла? Если можно, то как?

Можно ansible-vault view data

Как выглядит команда запуска playbook, если переменные зашифрованы?

ansible-playbook -i ./invetory/test.yml site.yml --ask-vault-pass

ansible-playbook -i ./invetory/test.yml site.yml --ask-vault-password

Чем два ключа отличаются?

Как называется модуль подключения к host на windows?

winrm

Приведите полный текст команды для поиска информации в документации ansible для модуля подключений ssh

ansible-doc -t connection ssh

Какой параметр из модуля подключения ssh необходим для того, чтобы определить пользователя, под которым необходимо совершать подключение?

remote_user ( set via: cli:,env:,ini:,vars:)
