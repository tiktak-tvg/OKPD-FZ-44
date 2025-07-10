#### Настройка API для работы с почтовым сервером.
Скачать архив с API методами:
```bash
wget https://download.r7-office.ru/mailserver/mailserver_api_pgsql_astra5.tar.gz
```
![image](https://github.com/user-attachments/assets/5bf17a9a-bdb4-43db-a2fd-823feb9e55d2)

Распаковать архив с api и добавить сервис как службу
```bash
tar -xzvf mailserver_api_pgsql_astra5.tar.gz --selinux -C /
```
![image](https://github.com/user-attachments/assets/922249c4-6d93-428c-b7f5-96ea7c2b589d)

Создайте службу systemd для почтового api:
```bash
nano /etc/systemd/system/mailserver_api.service
```
вставив описание для файла:
```bash
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
```
![image](https://github.com/user-attachments/assets/ea2470f1-a9cf-4e62-b579-5b378330c578)

Перезагрузите systemd:
```bash
systemctl daemon-reload
```
Запустите службу api добавив в автозагрузку:
```bash
systemctl enable mailserver_api --now
```
Проверьте статус службы api:
```bash
systemctl status mailserver_api
```
![image](https://github.com/user-attachments/assets/6146a54d-8c9e-4dde-8418-fa0a8a996a71)

#### Установка htpasswd на сервер.
Для deb:
```bash
apt-get install apache2-utils
```
![image](https://github.com/user-attachments/assets/4a0a4b26-5647-42cf-b0b0-b6267b8860a0)

Создание пользователя и пароля.
```bash
sudo htpasswd -c /etc/nginx/auth.basic admin
```
#### Встраивание API в Корпоративный сервер 2024 для работы с запросами.

Необходимо дополнить файл `/etc/nginx/sites-available/admin` добавив секцию в файл. Добавочная секция:
```bash
        location /apimail {
            proxy_pass http://localhost:8084;
            auth_basic "Restricted area";
            auth_basic_user_file /etc/nginx/auth.basic;  
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto https;
        }
```
>если ПС находиться на другом сервере, то localhost измените на ip адрес сервера почтового сервера.

![image](https://github.com/user-attachments/assets/ee43841b-cdf7-4104-9352-6e2b369c7632)

Перезапустите nginx:
```bash
nginx -s reload
```
![image](https://github.com/user-attachments/assets/11c816b0-6e3b-4ab5-ae5e-3245276e07b1)

Обновить БД для работы со всеми доступными методами `api` и функциональными возможностями почтового сервера
```bash
wget http://download.r7-office.ru/mailserver/scripts/upgrate_mail_db.sh
далее
sed -i 's/--username "\${DB_USER}"/--username postgres/g' ./upgrate_mail_db.sh
далее
sudo bash upgrate_mail_db.sh
```
![image](https://github.com/user-attachments/assets/cad5ea93-3681-4ed1-9183-b8dea1070b6a)

#### Установка и настройка модуля postfwd.

>Установить зависимости для postfwd:

```bash
apt install libcgi-pm-perl libio-multiplex-perl libnet-cidr-perl libnet-server-perl
```
![image](https://github.com/user-attachments/assets/ee3f9568-87b3-4cd6-8424-17b50eb7593b)

Скачать и установить пакет postfwd:
```bash
wget http://download.r7-office.ru/mailserver/pkg/postfwd_1.35-8_all.deb
Далее распаковываем и устанавливаем
apt install ./postfwd_1.35-8_all.deb
```
![image](https://github.com/user-attachments/assets/521e73ad-0912-427b-b3e2-b3bb726f17f0)

Настроить модуль postfwd.
```bash
touch /etc/postfix/postfwd.cf 
```
Открываем в редакторе `nano /etc/default/postfwd`.

Меняем `STARTUP=0` на `STARTUP=1` 
```bash
systemctl start postfwd
systemctl enable postfwd
```
![image](https://github.com/user-attachments/assets/89a16920-5958-442a-b73d-27b472dbe672)

Проверить слушается порт 10040:
```bash
ss -tulpn | grep 10040
```
![image](https://github.com/user-attachments/assets/a8cd76cf-9dd9-4e26-b2eb-bb6c70b00af6)

Настроить `postfix` для работы c `postfwd`.

Открываем в редакторе `nano /etc/postfix/main.cf`

Закоментировать:
```bash
smtpd_recipient_restrictions = permit_sasl_authenticated,
 reject_non_fqdn_recipient,
 reject_unknown_recipient_domain,
 reject_multi_recipient_bounce,
 reject_unauth_destination,
```
![image](https://github.com/user-attachments/assets/0b66f642-0528-442c-b679-e9d474926f4e)

Ниже добавить:
```bash
# Подключаем postfwd и разрешаем отправку только авторизированным пользователям.
# Подключаем белый/черный список.
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
```
![image](https://github.com/user-attachments/assets/91785ea1-e4d9-47aa-9217-c536e5aea2f9)

Перезапустите службы Dovecot и Postfix:
```bash
systemctl restart dovecot postfix
```
Проверяем статус
```bash
systemctl status dovecot postfix
```
![image](https://github.com/user-attachments/assets/c47e858a-d03a-4a8f-a7ca-02bf661c6368)

#### Настройка модуля quota для dovecot.
Установите пакеты для возможности работы по протоку `ldap`.
```bash
apt install dovecot-ldap slapd ldap-utils libldap2-dev
```
![image](https://github.com/user-attachments/assets/1ee07c67-fb94-4b1b-b6b5-644380b05824)

Создайте файл подключения к `LDAP`<br>
Открываем в редакторе `nano /etc/dovecot/dovecot-ldap.conf`
 
Содержимое:
```bash
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
```
Где
```bash
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
```
![image](https://github.com/user-attachments/assets/760c840b-ebf5-4edd-953a-ccfbe6afc51c)

Откройте в редакторе конфигурационный файл dovecot.conf `nano /etc/dovecot/dovecot.con`.

```bash
nano /etc/dovecot/dovecot.conf
```
Убедитесь, что в секции `mail_plugins` добавлен плагин `quota`.

```bash
mail_plugins = mailbox_alias acl quota
```
Добавьте плагин imap_quota в секцию protocol imap.
```bash
protocol imap {
 mail_plugins = $mail_plugins imap_acl imap_quota
 imap_client_workarounds = tb-extra-mailbox-sep
 mail_max_userip_connections = 1500
}
```
Добавьте плагин quota в секцию protocol lmtp.
```bash
protocol lmtp {
 info_log_path = /var/log/dovecot/lmtp.log
 mail_plugins = quota sieve
 postmaster_address = postmaster
 lmtp_save_to_detail_mailbox = yes
 recipient_delimiter = +
}
```
![image](https://github.com/user-attachments/assets/f60685c1-3000-4215-b244-33fc99316a7b)

В самом низу добавьте строки.
```bash
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
```
![image](https://github.com/user-attachments/assets/fa1b89a9-80a2-496e-8a0f-e049f3d357bc)

Отредактировать файл dovecot-dict-sql.conf.ext.
```bash
nano /etc/dovecot/dovecot-dict-sql.conf.ext
```
Закомментировать или удалить содержимое и добавить следующие строки:
```bash
map {
  pattern = priv/quota/storage
  table = virtual_users
  username_field = email
  value_field = quota
}
```
![image](https://github.com/user-attachments/assets/7fe96da0-38cd-4084-a2ef-5ffcf55278c5)

Откройте файл dovecot-pgsql.conf.
```bash
nano /etc/dovecot/dovecot-pgsql.conf
```
Измените строку user_query, чтобы добавить квоты:
```bash
user_query = SELECT '/mail/%d/%u' as home, 'maildir:/mail/%d/%u' as mail, NULL AS uid, NULL AS gid, '*:storage=' || quota AS quota_rule FROM virtual_users WHERE email = '%u'
```
![image](https://github.com/user-attachments/assets/817df5f3-ac57-4876-9e15-6f346133204a)

Проверить квоты на почтовом ящике.
```bash
doveadm quota get -u user@example.com
```
Перезапустите службы Dovecot и Postfix.
```bash
systemctl restart dovecot postfix
```
JWT-токен.
```bash
cat /opt/r7-office/mailserver_api/jwt_token
```
>Сохраните вывод команды для дальнейшего использования Api. 

![image](https://github.com/user-attachments/assets/f9468167-0106-484b-a096-cdaef601d169)

#### Примеры запросов к API для работы почтовым сервером.
Описание API методов работы с почтовым сервером Р7.<br>
Можно отправлять POST-запросы с данными.<br> 
Описание методов REST API.<br>

**Создание почтового ящика.**

```bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <jwt-token>” -d &apos;{"email":"test@domain.ru","password":"mail_password"}&apos; https://admin.domain.ru/apimail/create_user --user admin:PassworD
```
*Описание:*
```bash
email — почтовый ящик для создания пользователя.
password — пароль для пользователя почтового ящика.
https://admin.domain.ru/apimail/create_user — адрес вызова API.
admin:PassworD  - пользователь и пароль созданные в пункте 11.2
```

**Изменение пароля почтового ящика.**

```bash
curl -X POST -H "Content-Type: application/json" -d -H "jwt-auth: Bearer <jwt-token>” &apos;{"email":"test@domain.ru","password":"new_password"}&apos; https://admin.domain.ru/apimail/change_password --user admin:PassworD
```
*Описание:*
```bash
email — почтовый ящик, для которого изменяется пароль.
password — новый пароль для пользователя почтового ящика.
https://admin.domain.ru/apimail/change_password — адрес вызова API.
admin:PassworD  - пользователь и пароль созданные в пункте 11.2
```

**Изменение имени почтового ящика (с копированием директории с почтовыми данными).**

```bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <jwt-token>” -d &apos;{"old_email":"test@domain.ru","new_email":"test2@domain.ru"}&apos; https://admin.domain.ru/apimail/change_email --user admin:PassworD
```
*Описание:*
```bash
old_email — текущий почтовый ящик.
new_email — новое имя почтового ящика.
https://admin.domain.ru/apimail/change_email — адрес вызова API.
admin:PassworD  - пользователь и пароль созданные в пункте 11.2
```

**Удаление почтового ящика (с удалением данных почты).**

```bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <jwt-token>” -d &apos;{"email":"test@domain.ru"}&apos; https://admin.domain.ru/apimail/delete_user --user admin:PassworD
```
*Описание:*
```bash
email — почтовый ящик для удаления.
https://admin.domain.ru/apimail/delete_user — адрес вызова API.
admin:PassworD  - пользователь и пароль созданные в пункте 11.2
```

**Создание нового алиаса.**

```bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <jwt-token>” -d &apos;{"alias":"news@domain.ru","email":"user@domain.ru"}&apos; https://admin.domain.ru/apimail/create_alias --user admin:PassworD
```
*Описание:*
```bash
alias — адрес алиаса (например, news@domain.ru).
email — основной почтовый ящик, на который будет перенаправляться почта.
https://admin.domain.ru/apimail/create_alias — адрес вызова API.
admin:PassworD  - пользователь и пароль созданные в пункте 11.2
```

**Создание нового домена (c генерацией ключа opendkim).**

```bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <jwt-token>” -d &apos;{"name":"new-domain.ru"}&apos; https://admin.domain.ru/apimail/create_domain --user admin:PassworD
```
*Описание:*
```bash
name — имя нового домена (например, new-domain.ru).
https://admin.domain.ru/apimail/create_domain — адрес вызова API.
admin:PassworD  - пользователь и пароль созданные в пункте 11.2
```

**Получение DKIM записи домена (если необходимо добавить в глобальный DNS, для повышения доверия к домену).**

```bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <jwt-token>” -d &apos;{"domain":"new-domain.ru"}&apos; https://admin.domain.ru/apimail/get_dkim --user admin:PassworD
```
*Описание:*
```bash
domain — имя домена (например, new-domain.ru).
https://admin.domain.ru/apimail/get_dkim — адрес вызова API.
admin:PassworD  - пользователь и пароль созданные в пункте 11.2
```
**Удаление домена (c удалением данных opendkim).**

```bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <jwt-token>” -d &apos;{"name":"domain.ru"}&apos; https://admin.domain.ru/apimail/delete_domain --user admin:PassworD
```
*Описание:*
```bash
name — имя домена для удаления.
https://admin.domain.ru/apimail/delete_domain — адрес вызова API.
admin:PassworD  - пользователь и пароль созданные в пункте 11.2
```

**Создание алиаса с несколькими получателями.**

```bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <jwt-token>” -d &apos;{"source":"news_users@domain.ru","destinations":"user3@domain.ru,user4@domain.ru"}&apos; https://admin.domain.ru/apimail/create_alias_multy --user admin:PassworD
```
*Описание:*
```bash
source — адрес алиаса (например, news_users@domain.ru).
destinations — список получателей, разделенных запятой (например, user3@domain.ru,user4@domain.ru).
https://admin.domain.ru/apimail/create_alias_multy — адрес вызова API.
admin:PassworD  - пользователь и пароль созданные в пункте 11.2
```

**Изменение алиаса (для одного получателя).**

```bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <jwt-token>” -d &apos;{"source":"news@domain.ru","destinations":"news5@domain.ru"}&apos; 
https://admin.domain.ru/apimail/change_alias --user admin:PassworD
```
*Описание:*
```bash
source — текущий адрес алиаса.
destinations — новый получатель для алиаса.
https://admin.domain.ru/apimail/change_alias — адрес вызова API.
admin:PassworD  - пользователь и пароль созданные в пункте 11.2
```

**Удаление алиаса.**

```bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <jwt-token>” -d &apos;{"alias":"news@domain.ru"}&apos; https://admin.domain.ru/apimail/delete_alias --user admin:PassworD
```
*Описание:*
```bash
alias — адрес алиаса для удаления.
https://admin.domain.ru/apimail/delete_alias — адрес вызова API.
admin:PassworD  - пользователь и пароль созданные в пункте 11.2
```

**Проверка наличия почтового ящика.**
```bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <jwt-token>” -d &apos;{"email":"user@domain.ru"}&apos; https://admin.domain.ru/apimail/check_email --user admin:PassworD
```
*Описание:*
```bash
email — почтовый ящик для проверки.
https://admin.domain.ru/apimail/check_email — адрес вызова API.
admin:PassworD  - пользователь и пароль созданные в пункте 11.2
```

**Получение информации по объёму почтового ящика.**

```bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <jwt-token>” -d &apos;{"email":"user@domain.ru"}&apos; https://admin.domain.ru/apimail/mailbox_size --user admin:PassworD
```
*Описание:*
```bash
email — почтовый ящик для получения информации.
https://admin.domain.ru/apimail/mailbox_size — адрес вызова API.
admin:PassworD  - пользователь и пароль созданные в пункте 11.2
```

**Установка квоты для почтового ящика.**

Метод позволяет установить квоту (ограничение на объем хранилища) для указанного почтового ящика.
```bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <jwt-token>” -d &apos;{"email":"user@domain.ru","quota":1024}&apos; https://admin.domain.ru/apimail/set_quota --user admin:PassworD
```
*Описание:*
```bash
email — почтовый ящик, для которого устанавливается квота.
quota — квота в мегабайтах (MB). Должна быть положительным числом.
admin:PassworD  - пользователь и пароль созданные в пункте 11.2
Адрес вызова API: https://admin.domain.ru/apimail/set_quota
```

**Создание общего почтового ящика.**

Метод создает общий почтовый ящик и предоставляет доступ к нему указанному пользователю.
```bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <jwt-token>” -d &apos;{"from_user":"shared@domain.ru","to_user":"user@domain.ru","password":"shared_password"}&apos; https://admin.domain.ru/apimail/create_shared_mailbox --user admin:PassworD
```
*Описание:*
```bash
from_user — имя общего почтового ящика (например, shared@domain.ru).
to_user — пользователь, которому предоставляется доступ к общему ящику (опционально).
password — пароль для общего почтового ящика.
admin:PassworD  - пользователь и пароль созданные в пункте 11.2
Адрес вызова API: https://admin.domain.ru/apimail/create_shared_mailbox
```

**Добавление ACL для общего почтового ящика.**

Метод добавляет права доступа (ACL) для указанного пользователя к общему почтовому ящику.
```bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <jwt-token>” -d &apos;{"shared_mailbox":"shared@domain.ru","user":"user@domain.ru","rights":"read write"}&apos; https://admin.domain.ru/apimail/add_acl --user admin:PassworD
```
*Описание:*
```bash
shared_mailbox — имя общего почтового ящика.
user — пользователь, которому предоставляются права.
rights — права доступа (например, read, write).
admin:PassworD  - пользователь и пароль созданные в пункте 11.2
Адрес вызова API: https://admin.domain.ru/apimail/add_acl
```

**Удаление ACL для общего почтового ящика.**

Метод удаляет права доступа (ACL) для указанного пользователя к общему почтовому ящику.
```bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <jwt-token>” -d &apos;{"shared_mailbox":"shared@domain.ru","user":"user@domain.ru","rights":"read write"}&apos; https://admin.domain.ru/apimail/remove_acl --user admin:PassworD
```
*Описание:*
```bash
shared_mailbox — имя общего почтового ящика.
user — пользователь, у которого удаляются права.
rights — права доступа, которые необходимо удалить (например, read, write).
admin:PassworD  - пользователь и пароль созданные в пункте 11.2
Адрес вызова API: https://admin.domain.ru/apimail/remove_acl
```

**Получение ACL для общего почтового ящика.**

Метод возвращает права доступа (ACL) для указанного пользователя к общему почтовому ящику.
```bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <jwt-token>” -d &apos;{"shared_mailbox":"shared@domain.ru","user":"user@domain.ru"}&apos; https://admin.domain.ru/apimail/get_acl --user admin:PassworD
```
*Описание:*
```bash
shared_mailbox — имя общего почтового ящика.
user — пользователь, для которого запрашиваются права.
admin:PassworD  - пользователь и пароль созданные в пункте 11.2
Адрес вызова API: https://admin.domain.ru/apimail/get_acl
```

**Получение очереди почтового сервера.**

Метод возвращает состояние очереди почтового сервера.
```bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <jwt-token>” -H "Authorization: Bearer <твой_JWT_токен>" https://admin.domain.ru/apimail/mail_queue --user admin:PassworD
```
*Описание:*
```bash
user — пользователь, для которого запрашиваются права.
admin:PassworD  - пользователь и пароль созданные в пункте 11.2
Адрес вызова API: https://admin.domain.ru/apimail/mail_queue
```

**Получение списка лимитов на отправку почты.**

Метод возвращает лимиты установленные на ящик, домен, общие.
```bash
curl -X POST -H "Content-Type: application/json"  -H "jwt-auth: Bearer <jwt-token>” -d &apos;{"identifier":"mailbox@domain.ru","id_type":"email","limit_type":"send"}&apos; https://admin.domain.ru/apimail/get_limits --user admin:PassworD
```
*Описание:*
```bash
identifier — имя почтового ящика, домен.
id_type – указываем для чего правила ("email", "domain", "group", "server")
limit_type – указываем тип лимита ("send", "rcpt")
user — пользователь, для которого запрашиваются права.
admin:PassworD  - пользователь и пароль созданные в пункте 11.2
Адрес вызова API: https://admin.domain.ru/apimail/get_limits
```

**Установка лимитов на отправку почту.**

Метод возвращает лимиты установленные на ящик, домен, общие.
```bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <jwt-token>” -d &apos;{"identifier":"mailbox@domain.ru","id_type":"email","limit": 1,"period":"hour","limit_type":"send"}&apos; https://admin.domain.ru/apimail/set_send_limit --user admin:PassworD
```
*Описание:*
```bash
identifier — имя почтового ящика, домен.
id_type – указываем для чего правила ("email", "domain", "group", "server")
limit – количество писем,
period – указывается в течение какого периода ("hour", "day", "week")
limit_type – указываем тип лимита ("send", "rcpt")
user — пользователь, для которого запрашиваются права.
admin:PassworD  - пользователь и пароль созданные в пункте 11.2
Адрес вызова API: https://admin.domain.ru/apimail/set_send_limit
```

**Удаление лимитов на отправку почты.**

Метод возвращает лимиты установленные на ящик, домен, общие.
```bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <jwt-token>” -d &apos;{"identifier":"mailbox@domain.ru","id_type":"email","limit_type":"send"}&apos; https://admin.domain.ru/apimail/delete_limit --user admin:PassworD
```
*Описание:*
```bash
identifier — имя почтового ящика, домен.
id_type – указываем для чего правила ("email", "domain", "group", "server")
limit_type – указываем тип лимита ("send", "rcpt")
user — пользователь, для которого запрашиваются права.
admin:PassworD  - пользователь и пароль созданные в пункте 11.2
Адрес вызова API:
 https://admin.domain.ru/apimail/delete_limit
```

**Управление черным или белым списком.**

Метод добавляет почтовый адрес, домен или ip-адрес в черный или белый список список.<br>
Добавление в черный список.
```bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <jwt-token>” -d &apos;{
    "action": "add",
    "entry": "spam@example.ru",
    "list_type": "blacklist"
}&apos; https://admin.domain.ru/apimail/modify_list --user admin:PassworD
```

**Удаление из белого списка.**

```bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <jwt-token>” -d &apos;{
    "action": "remove",
    "entry": "trusted@example.ru",
    "list_type": "whitelist"
}&apos; https://admin.domain.ru/apimail/modify_list --user admin:PassworD
```
*Описание:*
```bash
action — add (добавить) или remove (удалить).
entry — email, домен или IP-адрес.
ist_type — blacklist (черный список) или whitelist (белый список).
Адрес вызова API: https://admin.domain.ru/apimail/modify_list
```

**Управление правами пользователей (JWT-аутентификация).**

Выдача токена.
```bash
curl -X POST -H "Content-Type: application/json" -d &apos;{
    "email": "admin@domain.ru"
}&apos; https://admin.domain.ru/apimail/generate_token --user admin:PassworD
```
*Описание:*
```bash
email — email пользователя, для которого создается JWT-токен.
```

**Назначение прав пользователю.**

```bash
curl -X POST -H "Content-Type: application/json" -d &apos;{
    "email": "user@domain.ru",
    "scope": "create_user"
}&apos; https://admin.domain.ru/apimail/assign_scope --user admin:PassworD
```
*Описание:*
```bash
email — email пользователя.
scope — назначаемое право (например, create_user, delete_user).
```

**Удаление прав у пользователя.**

```bash
curl -X POST -H "Content-Type: application/json" -d &apos;{
    "email": "user@domain.ru",
    "scope": "create_user"
}&apos; https://admin.domain.ru/apimail/revoke_scope --user admin:PassworD
```
*Описание:*
```bash
email — email пользователя.
scope — назначаемое право (например, create_user, delete_user).	
```

**Получение списка администраторов для управления правами:**

    • Запрашивает список пользователей, у которых есть права администратора.
    • Возвращает массив email-адресов.
```bash
curl -X POST -H "Content-Type: application/json" -d &apos;{
    "email": "user@domain.ru",
    "scope": "create_user"
}&apos; https://admin.domain.ru/apimail/revoke_scope --user admin:PassworD
``` 
