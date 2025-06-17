```bash
Скачать архив с API методами:
wget https://download.r7-office.ru/mailserver/mailserver_api_pgsql_astra5.tar.gz
Распаковать архив с api и добавить сервис как службу
tar -xzvf mailserver_api_pgsql_astra5.tar.gz --selinux -C /
Создайте службу systemd для почтового api:
nano /etc/systemd/system/mailserver_api.service
вставив описание для файла:
[Unit]
Description=Mailserver API Service
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/opt/r7-office/mailserver_api
ExecStart=/opt/r7-office/mailserver_api/mailserver_api
Restart=on-failure
RestartSec=5s

[Install]
WantedBy=multi-user.target

Перезагрузите systemd:
systemctl daemon-reload
Запустите службу api добавив в автозагрузку:
systemctl enable mailserver_api --now
Проверьте статус службы api:
systemctl status mailserver_api

Установка htpasswd на сервер
Для deb:
apt-get install apache2-utils		
Создание пользователя и пароля.
sudo htpasswd -c /etc/nginx/auth.basic admin


Встраивание API в Корпоративный сервер 2024 для работы с запросами:
Необходимо дополнить файл /etc/nginx/sites-available/admin добавив секцию в файл. Добавочная секция:
        location /apimail {
            proxy_pass http://localhost:8084;
            auth_basic "Restricted area";
            auth_basic_user_file /etc/nginx/auth.basic;  
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto https;
        }
если ПС находиться на другом сервере, то localhost измените на ip адрес сервера почтового сервера.
Перезапустите nginx:
nginx -s reload

Обновить БД для работы со всеми доступными методами api и функциональными возможностями почтового сервера
wget http://download.r7-office.ru/mailserver/scripts/upgrate_mail_db.sh
sed -i 's/--username "\${DB_USER}"/--username postgres/g' ./upgrate_mail_db.sh
sudo bash upgrate_mail_db.sh
11. Установка и настройка модуля postfwd:
Установить зависимости для postfwd:
apt install libcgi-pm-perl libio-multiplex-perl libnet-cidr-perl libnet-server-perl
Скачать и установить пакет postfwd:
wget http://download.r7-office.ru/mailserver/pkg/postfwd_1.35-8_all.deb

 apt install ./postfwd_1.35-8_all.deb

Настроить модуль postfwd:
touch /etc/postfix/postfwd.cf 

nano /etc/default/postfwd

STARTUP=1

systemctl start postfwd
systemctl enable postfwd

Проверить слушается порт 10040:
ss -tulpn
Настроить postfix для работы c postfwd:

nano /etc/postfix/main.cf

Закоментировать:
smtpd_recipient_restrictions = permit_sasl_authenticated,
 reject_non_fqdn_recipient,
 reject_unknown_recipient_domain,
 reject_multi_recipient_bounce,
 reject_unauth_destination,

Ниже добавить:
# Подключаем postfwd и разрешаем отправку только авторизированным пользователям. Подключаем белый/черный список.
smtpd_recipient_restrictions = permit_mynetworks,
 check_policy_service inet:127.0.0.1:10040,
 permit_sasl_authenticated,
 reject_unauth_destination,
 check_sender_access hash:/etc/postfix/access,
 reject_non_fqdn_helo_hostname,
 reject_non_fqdn_sender,
 reject_non_fqdn_recipient,
 reject_unknown_recipient_domain,
 reject_unknown_sender_domain

# Подключаем postfwd
smtpd_end_of_data_restrictions = check_policy_service inet:127.0.0.1:10040

Перезапустите службы Dovecot и Postfix:
systemctl restart dovecot postfix
Настройка модуля quota для dovecot:
Установите пакеты для возможности работы по протоку ldap :
apt install dovecot-ldap slapd ldap-utils libldap2-dev
Создайте файл подключения к LDAP.
nano /etc/dovecot/dovecot-ldap.conf
Содержимое:
hosts = 192.168.26.199:389
ldap_version = 3
auth_bind = yes
dn = uid=vmail,cn=users,cn=accounts,dc=aldbuilder,dc=r7,dc=loc
dnpass = password
base= cn=users,cn=accounts,dc=aldbuilder,dc=r7,dc=loc
scope = subtree
deref = never
user_filter = (&(mail=%u)(objectClass=person))
pass_filter = (&(mail=%u)(objectClass=person))
user_attrs = mailUidNumber=1100,mailGidNumber=1100
pass_attrs = userPassword=password
default_pass_scheme = CRYPT

Где,
hosts — адрес или имя  сервера ldap 
auth_bind — указываем, нужно ли выполнять аутентификацию при связывании с LDAP.
ldap_version — версия ldap.
dn — учетная запись для прохождения авторизации при связывании со службой каталогов.
dnpass — пароль от записи для прохождения авторизации.
base — базовый контейнер, в котором мы ищем наших пользователей. Нельзя использовать поиск на уровне домена. 
scope — указывает на каком уровне нужно искать пользователей:
                subtree — во всех вложенных контейнерах.
                onelevel — во всех вложенных контейнерах, но на один уровень.
                base — только в контейнере, который указан в base.
deref — параметр означает использование поиска по разыменованным псевдонимам.
user_filter — фильтр для поиска учетных записей пользователей.
pass_filter — фильтр для поиска паролей пользователей. 
pass_attrs  — соответствие между атрибутами найденного объекта ldap и внутренними переменными пользователя Dovecot.
default_pass_scheme — схема хранения паролей.

Откройте конфигурационный файл dovecot.conf:
nano /etc/dovecot/dovecot.conf

Убедитесь, что в секции mail_plugins добавлен плагин quota:
mail_plugins = mailbox_alias acl quota

Добавьте плагин imap_quota в секцию protocol imap:
protocol imap {
    mail_plugins = $mail_plugins imap_acl imap_quota
    imap_client_workarounds = tb-extra-mailbox-sep
    mail_max_userip_connections = 1500
}

 Добавьте плагин quota в секцию protocol lmtp:
protocol lmtp {
 info_log_path = /var/log/dovecot/lmtp.log
 mail_plugins = quota sieve
 postmaster_address = postmaster
 lmtp_save_to_detail_mailbox = yes
 recipient_delimiter = +
}
 
 В самом низу добавьте строки:
service dict {
    unix_listener dict {
    mode = 0660
    user = vmail
    group = vmail
  }
}

plugin {
    quota = maildir:User quota
}

dict {
    sql = pgsql:/etc/dovecot/dovecot-dict-sql.conf.ext
}

passdb {
 args = /etc/dovecot/dovecot-ldap.conf
 driver = ldap
 }

        Отредактировать файл dovecot-dict-sql.conf.ext:
nano /etc/dovecot/dovecot-dict-sql.conf.ext

Удалить содержимое и добавить следующие строки:
map {
  pattern = priv/quota/storage
  table = virtual_users
  username_field = email
  value_field = quota
}


Откройте файл dovecot-pgsql.conf:
nano /etc/dovecot/dovecot-pgsql.conf

Измените строку user_query, чтобы добавить квоты:
user_query = SELECT '/mail/%d/%u' as home, 'maildir:/mail/%d/%u' as mail, NULL AS uid, NULL AS gid, '*:storage=' || quota AS quota_rule FROM virtual_users WHERE email = '%u'

Проверить квоты на почтовом ящике:
doveadm quota get -u user@example.com

Перезапустите службы Dovecot и Postfix:
systemctl restart dovecot postfix

JWT-токен:
cat /opt/r7-office/mailserver_api/jwt_token

Сохраните вывод команды для дальнейшего использования
Api 
Описание API методов работы с почтовым сервером Р7
```
