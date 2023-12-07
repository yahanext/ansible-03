# Домашнее задание к занятию 3 «Использование Ansible»

## Подготовка к выполнению

1. Подготовьте в Yandex Cloud три хоста: для `clickhouse`, для `vector` и для `lighthouse`.
2. Репозиторий LightHouse находится [по ссылке](https://github.com/VKCOM/lighthouse).

## Основная часть

1. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает LightHouse.
```
Добавил
- name: Install lighthouse
  hosts: clickhouse-03
  handlers:
    - name: Nginx reload
      become: true
      ansible.builtin.service:
        name: nginx
        state: restarted
  pre_tasks:
    - name: Install git
      become: true
      ansible.builtin.yum:
        name: git
        state: present
    - name: Install epel-release
      become: true
      ansible.builtin.yum:
        name: epel-release
        state: present
    - name: Install nginx
      become: true
      ansible.builtin.yum:
        name: nginx
        state: present
    - name: Apply nginx config
      become: true
      ansible.builtin.template:
        src: nginx.j2
        dest: /etc/nginx/nginx.conf
        mode: 0644
  tasks:
    - name: Clone repository
      ansible.builtin.git:
        repo: "{{ lighthouse_url }}"
        dest: "{{ lighthouse_dir }}"
        version: master
    - name: Apply config
      become: true
      ansible.builtin.template:
        src: lighthouse.conf.j2
        dest: /etc/nginx/conf.d/lighthouse.conf
        mode: 0644
      notify: Nginx reload```
2. При создании tasks рекомендую использовать модули: `get_url`, `template`, `yum`, `apt`.
3. Tasks должны: скачать статику LightHouse, установить Nginx или любой другой веб-сервер, настроить его конфиг для открытия LightHouse, запустить веб-сервер.
4. Подготовьте свой inventory-файл `prod.yml`.
```
---
clickhouse:
  hosts:
    clickhouse-01:
      ansible_host: 158.160.115.194
      ansible_connection: ssh
      ansible_user: yaha
      ansible_ssh_private_key_file: ~/.ssh/id_ed25519.pub
vector:
  hosts:
    clickhouse-02:
      ansible_host: 158.160.103.51
      ansible_connection: ssh
      ansible_user: yaha
      ansible_ssh_private_key_file: ~/.ssh/id_ed25519.pub
lighthouse:
  hosts:
    clickhouse-03:
      ansible_host: 158.160.124.60
      ansible_connection: ssh
      ansible_user: yaha
      ansible_ssh_private_key_file: ~/.ssh/id_ed25519.pub```
5. Запустите `ansible-lint site.yml` и исправьте ошибки, если они есть.

```
yaha@yahawork:~/netology/ansible-03/playbook$ ansible-lint site.yml
WARNING  Overriding detected file kind 'yaml' with 'playbook' for given positional argument: site.yml
yaha@yahawork:~/netology/ansible-03/playbook$ 


```
6. Попробуйте запустить playbook на этом окружении с флагом `--check`.
```
Выдет ошибку т.к не установлен git, в рельном прогоне ошибки нет```
7. Запустите playbook на `prod.yml` окружении с флагом `--diff`. Убедитесь, что изменения на системе произведены.
```
aha@yahawork:~/netology/ansible-03/playbook$ ansible-playbook site.yml -i inventory/prod.yml --diff

PLAY [Install Clickhouse] ******************************************************

TASK [Gathering Facts] *********************************************************
[DEPRECATION WARNING]: Distribution centos 9 on host clickhouse-01 should use 
/usr/libexec/platform-python, but is using /usr/bin/python for backward 
compatibility with prior Ansible releases. A future Ansible release will 
default to using the discovered platform python for this host. See https://docs
.ansible.com/ansible/2.10/reference_appendices/interpreter_discovery.html for 
more information. This feature will be removed in version 2.12. Deprecation 
warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
ok: [clickhouse-01]

TASK [Get clickhouse distrib] **************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 1000, "group": "yaha", "item": "clickhouse-common-static", "mode": "0644", "msg": "Request failed", "owner": "yaha", "response": "HTTP Error 404: Not Found", "secontext": "unconfined_u:object_r:user_home_t:s0", "size": 246310036, "state": "file", "status_code": 404, "uid": 1000, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib] **************************************************
ok: [clickhouse-01]

TASK [Install clickhouse packages] *********************************************
ok: [clickhouse-01]

TASK [Create database] *********************************************************
ok: [clickhouse-01]

PLAY [Install Vector] **********************************************************

TASK [Gathering Facts] *********************************************************
ok: [clickhouse-02]

TASK [Download vector packages] ************************************************
ok: [clickhouse-02]

TASK [Install vector packages] *************************************************
ok: [clickhouse-02]

TASK [Vector create systed unit] ***********************************************
[WARNING]: The value "1000" (type int) was converted to "'1000'" (type string).
If this does not look like what you expect, quote the entire value to ensure it
does not change.
ok: [clickhouse-02]

TASK [Change vector systemd unit] **********************************************
ok: [clickhouse-02]

PLAY [Install lighthouse] ******************************************************

TASK [Gathering Facts] *********************************************************
[DEPRECATION WARNING]: Distribution centos 9 on host clickhouse-03 should use 
/usr/libexec/platform-python, but is using /usr/bin/python for backward 
compatibility with prior Ansible releases. A future Ansible release will 
default to using the discovered platform python for this host. See https://docs
.ansible.com/ansible/2.10/reference_appendices/interpreter_discovery.html for 
more information. This feature will be removed in version 2.12. Deprecation 
warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
ok: [clickhouse-03]

TASK [Install git] *************************************************************
ok: [clickhouse-03]

TASK [Install epel-release] ****************************************************
ok: [clickhouse-03]

TASK [Install nginx] ***********************************************************
ok: [clickhouse-03]

TASK [Apply nginx config] ******************************************************
ok: [clickhouse-03]

TASK [Clone repository] ********************************************************
ok: [clickhouse-03]

TASK [Apply config] ************************************************************
ok: [clickhouse-03]

PLAY RECAP *********************************************************************
clickhouse-01              : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0   
clickhouse-02              : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
clickhouse-03              : ok=7    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

yaha@yahawork:~/netology/ansible-03/playbook$ 

```
8. Повторно запустите playbook с флагом `--diff` и убедитесь, что playbook идемпотентен.
```
аналогично
```

9. Подготовьте README.md-файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.
10. Готовый playbook выложите в свой репозиторий, поставьте тег `08-ansible-03-yandex` на фиксирующий коммит, в ответ предоставьте ссылку на него.

---

### Как оформить решение задания

Выполненное домашнее задание пришлите в виде ссылки на .md-файл в вашем репозитории.

---
