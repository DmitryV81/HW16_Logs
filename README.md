Домашнее задание №16

Сбор и анализ логов

Описание домашнего задания

1. В Vagrant разворачиваем 2 виртуальные машины web и log

2. на web настраиваем nginx

3. на log настраиваем центральный лог сервер на любой системе на выбор

journald;

rsyslog;

elk.

4. настраиваем аудит, следящий за изменением конфигов nginx 

Все критичные логи с web должны собираться и локально и удаленно.

Все логи с nginx должны уходить на удаленный сервер (локально только критичные).

Логи аудита должны также уходить на удаленную систему.

Формат сдачи ДЗ - vagrant + ansible

Дополнительное задание

развернуть еще машину с elk

таким образом настроить 2 центральных лог системы elk и какую либо еще;

в elk должны уходить только логи нжинкса;

во вторую систему все остальное.

Ход работы.

В вагрант разворачиваем три виртуальные машины: webserver, log и elk

На webserver устанавливаем nginx и filebeat

На log сервер rsyslog и auditd

На elk - ELK-stack

Все три ВМ настраиваются автоматически с использованием ansible. Для этого созданы роли:

nginx, log, elk, filebeat, auditconf ,ntpd

В работе использовал elasticsearch_7.17.3_x86_64-224190-9bcb26.rpm, kibana_7.17.3_x86_64-224190-b13e53.rpm, logstash_7.17.3_x86_64-224190-3a605f.rpm

Перед запуском стенда их требуется скачать и поместить в директорию roles/elk/files/

Ссылка для скачивания:

https://disk.yandex.ru/d/NbPeohlbWUC0aQ

Проверяем работу rsyslog на ВМ log:
```
[root@log ~]# cat /var/log/rsyslog/webserver/nginx_access.log 
Mar 11 18:02:34 webserver nginx_access: 10.0.2.2 - - [11/Mar/2023:18:02:34 +0300] "\x16\x03\x01\x02\x00\x01\x00\x01\xFC\x03\x03\xB55E%\xF4E\xBD\xC9I v\xCE/\xBB\xD8X,d\xEE\xA8=\xBE\xD1x\xF1<C\xB7\xAE\xCB\x80\x14 \x0FD\x89\xAE\xA2\xE7\xA4m\xB6\xD6L\x1AI\x04\xDC4`\xC4\xB2Z\x97U&\x9E\x8F\xCB\x0E\xC5s>\xBE\xD2\x00\x22\x13\x01\x13\x03\x13\x02\xC0+\xC0/\xCC\xA9\xCC\xA8\xC0,\xC00\xC0" 400 157 "-" "-"
Mar 11 18:02:47 webserver nginx_access: 10.0.2.2 - - [11/Mar/2023:18:02:47 +0300] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/110.0"
Mar 11 18:02:47 webserver nginx_access: 10.0.2.2 - - [11/Mar/2023:18:02:47 +0300] "GET /img/centos-logo.png HTTP/1.1" 304 0 "http://127.0.0.1:8080/" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/110.0"
Mar 11 18:02:47 webserver nginx_access: 10.0.2.2 - - [11/Mar/2023:18:02:47 +0300] "GET /img/html-background.png HTTP/1.1" 304 0 "http://127.0.0.1:8080/" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/110.0"
Mar 11 18:02:47 webserver nginx_access: 10.0.2.2 - - [11/Mar/2023:18:02:47 +0300] "GET /img/header-background.png HTTP/1.1" 304 0 "http://127.0.0.1:8080/" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/110.0"
Mar 11 18:07:06 webserver nginx_access: 10.0.2.2 - - [11/Mar/2023:18:07:06 +0300] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/110.0"
Mar 11 18:07:07 webserver nginx_access: 10.0.2.2 - - [11/Mar/2023:18:07:07 +0300] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/110.0"
Mar 11 18:07:07 webserver nginx_access: 10.0.2.2 - - [11/Mar/2023:18:07:07 +0300] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/110.0"
Mar 11 18:07:08 webserver nginx_access: 10.0.2.2 - - [11/Mar/2023:18:07:08 +0300] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/110.0"
Mar 11 18:07:08 webserver nginx_access: 10.0.2.2 - - [11/Mar/2023:18:07:08 +0300] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/110.0"
Mar 11 18:07:09 webserver nginx_access: 10.0.2.2 - - [11/Mar/2023:18:07:09 +0300] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/110.0"
Mar 11 18:07:15 webserver nginx_access: 10.0.2.2 - - [11/Mar/2023:18:07:15 +0300] "GET /web HTTP/1.1" 404 3650 "-" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/110.0"
Mar 11 18:07:15 webserver nginx_access: 10.0.2.2 - - [11/Mar/2023:18:07:15 +0300] "GET /nginx-logo.png HTTP/1.1" 304 0 "http://127.0.0.1:8080/web" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/110.0"
Mar 11 18:07:15 webserver nginx_access: 10.0.2.2 - - [11/Mar/2023:18:07:15 +0300] "GET /poweredby.png HTTP/1.1" 304 0 "http://127.0.0.1:8080/web" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/110.0"
Mar 11 18:07:24 webserver nginx_access: 10.0.2.2 - - [11/Mar/2023:18:07:24 +0300] "GET /new_page HTTP/1.1" 404 3650 "-" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/110.0"
[root@log ~]# cat /var/log/rsyslog/webserver/nginx_error.log 
Mar 11 18:07:15 webserver nginx_error: 2023/03/11 18:07:15 [error] 5343#5343: *4 open() "/usr/share/nginx/html/web" failed (2: No such file or directory), client: 10.0.2.2, server: web, request: "GET /web HTTP/1.1", host: "127.0.0.1:8080"
[root@log ~]# 
```
Проверяем работу auditd на webserver:

```
[root@webserver ~]# grep nginx_conf /var/log/audit/audit.log 
node=webserver type=CONFIG_CHANGE msg=audit(1678550506.749:2001): auid=4294967295 ses=4294967295 subj=system_u:system_r:unconfined_service_t:s0 op=add_rule key="nginx_conf" list=4 res=1
node=webserver type=CONFIG_CHANGE msg=audit(1678550506.756:2002): auid=4294967295 ses=4294967295 subj=system_u:system_r:unconfined_service_t:s0 op=add_rule key="nginx_conf" list=4 res=1

```

Проверяем работу auditd на ВМ log:
```
[root@log ~]# grep web /var/log/audit/audit.log 
node=webserver type=DAEMON_START msg=audit(1678550506.561:3888): op=start ver=2.8.5 format=raw kernel=3.10.0-1127.el7.x86_64 auid=4294967295 pid=24243 uid=0 ses=4294967295 subj=system_u:system_r:auditd_t:s0 res=success
node=webserver type=CONFIG_CHANGE msg=audit(1678550506.748:1999): audit_backlog_limit=8192 old=8192 auid=4294967295 ses=4294967295 subj=system_u:system_r:unconfined_service_t:s0 res=1
node=webserver type=CONFIG_CHANGE msg=audit(1678550506.749:2000): audit_failure=1 old=1 auid=4294967295 ses=4294967295 subj=system_u:system_r:unconfined_service_t:s0 res=1
node=webserver type=CONFIG_CHANGE msg=audit(1678550506.749:2001): auid=4294967295 ses=4294967295 subj=system_u:system_r:unconfined_service_t:s0 op=add_rule key="nginx_conf" list=4 res=1
node=webserver type=CONFIG_CHANGE msg=audit(1678550506.756:2002): auid=4294967295 ses=4294967295 subj=system_u:system_r:unconfined_service_t:s0 op=add_rule key="nginx_conf" list=4 res=1
node=webserver type=SERVICE_START msg=audit(1678550506.760:2003): pid=1 uid=0 auid=4294967295 ses=4294967295 subj=system_u:system_r:init_t:s0 msg='unit=auditd comm="systemd" exe="/usr/lib/systemd/systemd" hostname=? addr=? terminal=? res=success'
node=webserver type=SYSCALL msg=audit(1678550571.243:2004): arch=c000003e syscall=268 success=yes exit=0 a0=ffffffffffffff9c a1=2060420 a2=1a4 a3=7ffe636db520 items=1 ppid=5405 pid=24271 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=6 comm="chmod" exe="/usr/bin/chmod" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="nginx_conf"
node=webserver type=CWD msg=audit(1678550571.243:2004):  cwd="/root"
node=webserver type=PATH msg=audit(1678550571.243:2004): item=0 name="/etc/nginx/nginx.conf" inode=724040 dev=08:01 mode=0100755 ouid=0 ogid=0 rdev=00:00 obj=system_u:object_r:httpd_config_t:s0 objtype=NORMAL cap_fp=0000000000000000 cap_fi=0000000000000000 cap_fe=0 cap_fver=0
node=webserver type=PROCTITLE msg=audit(1678550571.243:2004): proctitle=63686D6F64002D78002F6574632F6E67696E782F6E67696E782E636F6E66
node=webserver type=SYSCALL msg=audit(1678550575.739:2005): arch=c000003e syscall=268 success=yes exit=0 a0=ffffffffffffff9c a1=6a9420 a2=1ed a3=7ffeea809c60 items=1 ppid=5405 pid=24272 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=6 comm="chmod" exe="/usr/bin/chmod" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="nginx_conf"
node=webserver type=DAEMON_START msg=audit(1678550899.314:2648): op=start ver=2.8.5 format=raw kernel=3.10.0-1127.el7.x86_64 auid=4294967295 pid=24389 uid=0 ses=4294967295 subj=system_u:system_r:auditd_t:s0 res=success
node=webserver type=CONFIG_CHANGE msg=audit(1678550899.455:2010): auid=4294967295 ses=4294967295 subj=system_u:system_r:unconfined_service_t:s0 op=remove_rule key="nginx_conf" list=4 res=1
node=webserver type=CONFIG_CHANGE msg=audit(1678550899.455:2011): auid=4294967295 ses=4294967295 subj=system_u:system_r:unconfined_service_t:s0 op=remove_rule key="nginx_conf" list=4 res=1
node=webserver type=CONFIG_CHANGE msg=audit(1678550899.455:2012): audit_backlog_limit=8192 old=8192 auid=4294967295 ses=4294967295 subj=system_u:system_r:unconfined_service_t:s0 res=1
node=webserver type=CONFIG_CHANGE msg=audit(1678550899.456:2013): audit_failure=1 old=1 auid=4294967295 ses=4294967295 subj=system_u:system_r:unconfined_service_t:s0 res=1
node=webserver type=CONFIG_CHANGE msg=audit(1678550899.456:2014): auid=4294967295 ses=4294967295 subj=system_u:system_r:unconfined_service_t:s0 op=add_rule key="nginx_conf" list=4 res=1
node=webserver type=CONFIG_CHANGE msg=audit(1678550899.466:2015): auid=4294967295 ses=4294967295 subj=system_u:system_r:unconfined_service_t:s0 op=add_rule key="nginx_conf" list=4 res=1
node=webserver type=SERVICE_START msg=audit(1678550899.470:2016): pid=1 uid=0 auid=4294967295 ses=4294967295 subj=system_u:system_r:init_t:s0 msg='unit=auditd comm="systemd" exe="/usr/lib/systemd/systemd" hostname=? addr=? terminal=? res=success'
[root@log ~]# 

```

Проверяем работу ELK и настраиваем dashboard:

![ELK dashborard webserver](https://github.com/DmitryV81/HW16_Logs/blob/main/ELK.png)
