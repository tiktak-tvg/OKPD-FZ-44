### Устанавливаем и запускаем.
```bash
apt install firewalld -y
systemctl start firewalld
```
### Добавляем правила.
#### Для Dovecot и Postfix.
```bash
firewall-cmd --zone=public --add-service=smtp # 25 порт
firewall-cmd --zone=public --add-service=smtps # 465 порт
firewall-cmd --zone=public --add-service=imap # 143 порт
firewall-cmd --zone=public --add-service=imaps # 993 порт
firewall-cmd --zone=public --add-service=smtp-submission # 587 порт
```
#### SSH.
```bash
firewall-cmd --zone=public --add-service=ssh # 22 порт ssh
firewall-cmd --zone=public --add-port=2345/tcp # пример добавления любых tcp портов
```
#### GlusterFS.
```bash
firewall-cmd --zone=internal --add-service=glusterfs
firewall-cmd --zone=internal --add-port=49150-49160/tcp # Порт из п.3.3 (первоначально 49152), на нём слушается volume /mail_volume. Если будете создавать новые, то они будут занимать следующие порты по порядку#PgSQL
firewall-cmd --zone=internal --add-service=pgsql
```
##### Добавляем с какого диапозона или ip возможно подключение к PgSQL и GlusterFS.
```bash
firewall-cmd --zone=internal --add-source=192.168.25.0/24
```
#### Добавляем в автозагрузку.
```bash
sudo systemctl enable --now firewalld
```
#### Проверяем правила, лишние порты и сервисы можете удалить.
```bash
firewall-cmd --zone=public --list-all
firewall-cmd --zone=internal --list-all
```
#### Применяем правила на постоянное использование.
```bash
firewall-cmd --runtime-to-permanent # применения правил на постоянной основе
firewall-cmd --reload # перезагружаем firewalld
```
