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




