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
На всех ОС
```bash
sudo apt update && sudo apt install postgresql acl ca-certificates postfix postfix-pgsql dovecot-imapd dovecot-pop3d dovecot-sieve dovecot-managesieved dovecot-lmtpd dovecot-pgsql glusterfs-server rsyslog -y
```
На первом экране настройки выбираем Internet Site.<br>
На следующем экране нужно указать FQDN сервера, на котором работает почтовый сервис (уже имеется, нажимаем ok).<br>
Настройка репликации базы данных PostgreSQL:
    • На сервере №1 mx
Создайте первичный кластер базы данных:
```bash
sudo postgresql-setup --initdb
```
    • На сервере №1 mx1
    
Запускаем PostgreSQL и добавляем её в автозагрузку:
```bash
sudo systemctl enable --now postgresql
```
    • На всех серверах
    
В файле pg_hba.conf меняем тип авторизации в строке:
```bash
sudo vi /var/lib/pgsql/data/postgresql.conf
```
или (в зависимости от версии)
```bash
sudo vi /etc/postgresql/13/main/postgresql.conf
```
```bash
listen_addresses = 'localhost,ip_srv1,ip_srv2'       # Слушать на адресах
port = 5432                         # Задать порт БД
wal_level = replica             # Включить репликацию
archive_mode = on               # Включить архивирование WAL
archive_command = 'cp %p /var/lib/pgsql/archive/%f'   # Команда для архивирования WAL
max_wal_senders = 5            # Максимальное количество одновременных подключений репликации
wal_keep_segments = 50          # Количество WAL-сегментов для хранения
hot_standby = on                # Разрешить подключения в режиме hot standby
где,  ip_srv1,2 адреса внутренней сети первого и второго почтового сервера.
```
    • На всех серверах


