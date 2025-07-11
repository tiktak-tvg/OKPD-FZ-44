### Инструкция по установке почтового сервера отказоустойчивая архитектура (ОС Astra Linux Orel 1.7.4)

Содержание.

1. Подготовка к установке.<br>	
2. Установим базу данных PostgreSQL, Postfix, Dovecot и GlusterFS.<br>
3. Создание общей директории для синхронизации почты.<br>	
4. Настройка брандмауэра(firewalld).<br>	
5. Настройка и конфигурация Postfix.<br>	
6. Настройка и интеграция служб Dovecot.<br>	
7. Создание почтовых ящиков.<br>
8. Установка спам-фильтра Spamassassin(опционально).<br>
9. Настройка дополнительных записей.<br>
10. Полезные команды.<br>	
11. Схема работы(отказоустойчивая архитектура).<br>

#### Подготовка к установке.
##### Записи в DNS.
Необходимо сделать A и MX записи в виде:
# MX записи
```bash
mx - 10.0.0.1
mx1 - 10.0.0.2
mxN - 10.0.0.N
```
# А записи
```bash
smtp - 192.168.77.9, 192.168.77.13
imap - 192.168.77.9, 192.168.77.13
```
Пример А записей:   

![image](https://github.com/user-attachments/assets/cc0ce4ab-953a-4b84-a700-5e0edcf6cf18)

Пример MX записей:

![image](https://github.com/user-attachments/assets/c6aa6b1a-1be2-4897-a5e8-5cd83f8ed2b6)

А также TXT зпись v=spf1 +mx ~all

![image](https://github.com/user-attachments/assets/b17d330d-6040-43bb-943e-d18cdd6c57cc)

Пропишем соответствующее доменное имя почтового сервера на ВМ (поправить на свой домен):

    • На всех серверах
    
```bash
сервер mx
hostnamectl set-hostname mx.r7mail.ru
systemctl restart systemd-hostnamed
сервер mx1
hostnamectl set-hostname mx1.r7mail.ru
systemctl restart systemd-hostnamed
```
>Справка.

Hostname сервера не совпадает с его доменом.<br>
Например, mx.r7mail.ru — полное имя домена, а server — его mx.
Проверим.
```bash
hostname -d && hostname -s
```

![image](https://github.com/user-attachments/assets/47b7b901-c5ab-450c-8f37-0f9992827b55)

Скопируем репозиторий для возможности быстрого доступа к копированию и просмотру конфигурационных файлов:

    • На всех серверах

Скачайте архив `wget https://download.r7-office.ru/mailserver/mailserver-deb-config.tar.gz` 

![image](https://github.com/user-attachments/assets/30bdde02-ff13-480f-8293-085553e16ba8)

и распакуйте его, чтобы каталог config и остальные файлы была после данной директории `/mnt/mailserver/`, чтобы было быстрее выполнять команды. Например так:
```bash
tar -xzf mailserver-deb-config.tar.gz -C /mnt
mv /mnt/mailserver-ubuntu22-config /mnt/mailserver
```
Т.к. далее примеры команд указаны с данным путём.

Установим базу данных PostgreSQL, Postfix, Dovecot и GlusterFS:
    
    • На всех серверах

Выполните только на Astra Linux:
```bash
echo "deb https://dl.astralinux.ru/astra/stable/1.7_x86-64/repository-extended/ 1.7_x86-64 main contrib non-free" >> /etc/apt/sources.list
```
<img width="1106" height="79" alt="image" src="https://github.com/user-attachments/assets/dcfa0ec3-3906-40e7-92fd-4da64d4bef34" />

На всех ОС
```bash
sudo apt update && sudo apt install postgresql acl ca-certificates postfix postfix-pgsql dovecot-imapd dovecot-pop3d dovecot-sieve dovecot-managesieved dovecot-lmtpd dovecot-pgsql glusterfs-server rsyslog -y
```
<img width="1571" height="235" alt="image" src="https://github.com/user-attachments/assets/c608635e-3475-40bd-b5eb-886cb4ad109d" />

На первом экране настройки выбираем Internet Site.<br>
На следующем экране нужно указать FQDN сервера, на котором работает почтовый сервис (уже имеется, нажимаем ok).<br>
Настройка репликации базы данных PostgreSQL:

    • На сервере №1 mx.r7mail.ru

Создайте первичный кластер базы данных:
```bash
postgresql-setup --initdb
```
/usr/lib/postgresql/11/bin

    • На сервере №1 mx.r7mail.ru
    
Запускаем PostgreSQL и добавляем её в автозагрузку:
```bash
systemctl enable --now postgresql
```
    • На всех серверах
    
В файле pg_hba.conf меняем тип авторизации в строке `nano /etc/postgresql/11/main/postgresql.conf`:

в зависимости от версии, нашем случае версия postgresql 11
```bash
nano /etc/postgresql/11/main/postgresql.conf
```
```bash
listen_addresses = 'localhost,ip_srv1,ip_srv2'          # Слушать на адресах
port = 5432                                             # Задать порт БД
wal_level = replica                                     # Включить репликацию
archive_mode = on                                       # Включить архивирование WAL
archive_command = 'cp %p /var/lib/postgresql/11/main/archive/%f'     # Команда для архивирования WAL
max_wal_senders = 5                                     # Максимальное количество одновременных подключений репликации
wal_keep_segments = 50                                  # Количество WAL-сегментов для хранения
hot_standby = on                                        # Разрешить подключения в режиме hot standby
```
где,  `ip_srv1,2` адреса внутренней сети первого и второго почтового сервера.

    • На всех серверах

В файле `postgresql.conf` разрешаем логическую репликацию, добавляем строку для прослушивания локального интерфейса:

в зависимости от версии, нашем случае версия postgresql 11 - `sudo nano /etc/postgresql/11/main/pg_hba.conf`:
```bash
host    all             all             127.0.0.1/32            ident
Заменяем строку на:
# Разрешить локальные подключения для всех пользователей
host    all             all             127.0.0.1/32            md5
```
Добавляем строки:
```bash
# Разрешить репликацию с обоих серверов для пользователя replication_user
host    replication     replication_user        ip_another_srv/32    md5
host    replication     replication_user        ip_srv/32    md5
```
где, `ip_another_srv/32` адрес первого или второго почтового сервера, в зависимости от сервера где производиться настройка.

    • На сервере №1 mx.r7mail.ru

После базовой настройки базы данных, создаем необходимую структуру в БД, создаем пользователей и настраиваем репликацию:
```bash
sudo -u postgres psql
```
В консоли `psql>` выполняем следующие команды (вместо `password` и `replication_password` задайте свой пароль и в поле `insert into` вместо `YOUR_DOMAIN` укажите реальный адрес Вашего домена), подтверждая каждую нажатием Enter:
```bash
CREATE DATABASE postfix ENCODING 'UTF-8';
CREATE USER postfix WITH PASSWORD 'ваш пароль';
GRANT ALL PRIVILEGES ON DATABASE postfix TO postfix;
\c postfix;
create table virtual_domains (id serial primary key, name varchar(50) not null);
create table virtual_users (id serial primary key, domain_id int not null, password varchar(106) not null, email varchar(120) not null, quota bigint default null, unique (email), foreign key (domain_id) references virtual_domains(id) on delete cascade);
create table virtual_aliases (id serial primary key, domain_id int not null, source varchar(100) not null, destination varchar(100) not null, foreign key (domain_id) references virtual_domains(id) on delete cascade);
GRANT ALL PRIVILEGES ON TABLE virtual_users TO postfix;
GRANT ALL PRIVILEGES ON TABLE virtual_domains TO postfix;
GRANT ALL PRIVILEGES ON TABLE virtual_users_id_seq TO postfix;
GRANT ALL PRIVILEGES ON TABLE virtual_aliases TO postfix;
GRANT ALL PRIVILEGES ON TABLE virtual_aliases_id_seq TO postfix;
insert into virtual_domains (name) values ('r7mail.ru');
```
    • На сервере №1 mx.r7mail.ru

Создайте директорию для архива:
```bash
sudo mkdir /var/lib/pgsql/archive
```
    • На сервере №1 mx.r7mail.ru

Перезагружаем службу PostgreSQL для применения изменений:
```bash
systemctl restart postgresql
```
    • На сервере №2 mx1.r7mail.ru

Удалите существующую директорию данных:
```bash
rm -rf /var/lib/pgsql/data
или (в зависимости от версии)
rm -rf  /var/lib/postgresql/11/main
```

    • На сервере №2

Создайте базовую резервную копию с мастера:
```bash
su - postgres -c "pg_basebackup --host=ip_srv1 --username=replication_user --pgdata=/var/lib/pgsql/data --wal-method=stream --write-recovery-conf"
или (в зависимости от версии)
su - postgres -c "pg_basebackup --host=ip_srv1 --username=replication_user --pgdata=/var/lib/postgresql/13/main --wal-method=stream --write-recovery-conf"
```
где,  ip_srv1 адрес внутренней сети первого почтового сервера, с master БД.

Создайте файл recovery.conf в директории данных:
```bash
sudo vi /var/lib/pgsql/data/recovery.conf
```
Добавьте следующие строки:
```bash
standby_mode = 'on'
primary_conninfo = 'host= ip_srv1 port=5432 user=replication_user password=replication_password'
restore_command = 'cp /var/lib/pgsql/archivedir/%f %p'
```
где,  ip_srv1 адрес внутренней сети первого почтового сервера, с master БД и replication_password заданный на этапе ранее

Запускаем PostgreSQL на slave и добавляем в автозагрузку:
```bash
sudo systemctl enable --now postgresql
```
Проверьте состояние репликации:
```bash
sudo -u postgres psql -x -c 'select * from pg_stat_wal_receiver;'
```
Создание общей директории для синхронизации почты

    • На всех серверах

`nano /etc/hosts`
```bash
127.0.0.1       localhost
first_ip_address  gluster1
second_ip_address gluster2
```
Где
    • first_ip_address - внутренний ip первого сервера, например 10.0.0.1
    • second_ip_address - внутренний ip второго сервера, например 10.0.0.2 Например:
```bash
127.0.0.1       localhost
10.0.0.1 gluster1
10.0.0.2 gluster2
```
Запускаем glusterfs.

    • На всех серверах
    
```bash    
systemctl start glusterd.service
systemctl enable glusterd.service
```
Связываем ноды.
Не имеет значения, какой из узлов вы будете использовать, но в следующем примере команда запускается на gluster1:
```bash
gluster peer probe gluster2
```
Фактически эта команда сообщает `gluster1` доверять `gluster2` и регистрирует его как часть пула хранения данных.

Если зондирование пройдет успешно, вы получите следующий вывод
```bash
peer probe: success
```
Вы можете проверить связь узлов в любое время путем запуска команды
```bash
gluster peer status
```
Если вы запустите эту команду из gluster2, вы увидите следующий вывод
```bash
Number of Peers: 1

Hostname: gluster1
Uuid: 7ecfa2d1-3394-4f15-a1f5-bba484f2bbef
State: Peer in Cluster (Connected)
```
На этом этапе два ваших сервера взаимодействуют и готовы к созданию томов хранения друг с другом.

Создание тома хранения

    • Выполняется на первом сервере

Для создания тома вы будете использовать команду gluster volume create с таким общим синтаксисом:
```bash
sudo gluster volume create mail_volume replica 2 gluster{1,2}:/mail_volume force
```
Если том был создан успешно, вы увидите следующий вывод:
```bash
volume create: mail_volume: success: please start the volume to access data
```
На этом этапе ваш том создан, но еще не активирован. Вы можете запустить том и сделать его доступным для использования путем выполнения следующей команды с любого сервера 
```bash
Gluster:
sudo gluster volume start mail_volume
```
Вы получите следующий вывод, если том запущен корректно:
```bash
volume start: mail_volume: success
```  
Затем проверьте, находится ли том в сети. Запустите следующую команду с любого из ваших узлов:
```bash
sudo gluster volume status
```
В результате вы увидите вывод, аналогичный данному:
```bash
Status of volume: mail
Gluster process                             TCP Port  RDMA Port  Online  Pid
------------------------------------------------------------------------------
Brick gluster1:/mail                        49152     0          Y       4495
Brick gluster2:/mail                        49152     0          Y       20192
Self-heal Daemon on localhost               N/A       N/A        Y       4518
Self-heal Daemon on gluster2                N/A       N/A        Y       20215

Task Status of Volume mail_volume
------------------------------------------------------------------------------
There are no active volume tasks
```bash


