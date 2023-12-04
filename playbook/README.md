## Установка clickhouse и vector

Playbook:
С помощью данного плейбука устанавливается clickhouse на систему семейства redhat/centos и vector на систему ubuntu/debian 
 - в clickhouse/vars.yml прописаны пакеты для установки clickhouse
 - в vector.yml мы указываем откуда брать данные и куда транслировать (clickhouse).
 - prod.yml описываеются ip адреса хостов, тип авторизации и путь до ключей 
 - site.yml собственно сама процедура установки
 - в каталоге templates находится шаблон настройки vector и настройки юнита

Чтобы работал playbook:
 - Необходимо предоставить доступы для работы ansible на хостах через ssh, указать пользователя и добавить его в sudoers без пароля.
 - Добавить ip-адрес серверов в inventory
- В файлах vars clickhouse и vector указать версии дистрибутивов
 - Запустить playbook:

```bash
$ ansible-playbook -i inventory/prod.yml site.yml
```