## Установка clickhouse и vector

Playbook:
С помощью данного плейбука устанавливается clickhouse и lighthouse на систему семейства redhat/centos и vector на систему ubuntu/debian 
 - в clickhouse/vars.yml прописаны пакеты для установки clickhouse
 - в vector/vector.yml мы указываем откуда брать данные и куда транслировать (clickhouse).
 - в lighthouse/lighthouse.yml указываем откуда брать статику, рабочую директорию и пользователя под которым запускается
 - prod.yml описываеются ip адреса хостов, тип авторизации и путь до ключей 
 - site.yml собственно сама процедура установки
 - в каталоге templates находится шаблон настройки vector, nginx и lighthouse

Чтобы работал playbook:
 - Необходимо предоставить доступы для работы ansible на хостах через ssh, указать пользователя и добавить его в sudoers без пароля.
 - Добавить ip-адрес серверов в inventory
- В файлах vars clickhouse и vector указать версии дистрибутивов
 - Запустить playbook:

```bash
$ ansible-playbook -i inventory/prod.yml site.yml
```