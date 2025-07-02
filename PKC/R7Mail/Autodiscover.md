### Настройка Autodiscover для корпоративного почтового сервера Р7

Для настройки Autodiscover в **Корпоративном почтовом сервере Р7** выполните следующие шаги:

#### 1.Подготовка DNS-записей
Добавьте в DNS вашего домена:
```bash
# Основная запись
autodiscover.IN.CNAME r7mx1.it.company.lan

# Альтернативно (если не поддерживается CNAME)
autodiscover.IN.A 192.168.25.91

# SRV-запись для улучшения совместимости
_autodiscover._tcp.IN.SRV 10 0 443 r7mx1.it.company.lan
```
Пример с ALD PRO:

| Тип      | Имя                         | Значение                     | TTL  |
|----------|-----------------------------|------------------------------|------|
| `CNAME или A`  | `autodiscover`        | `r7mx1(имя почтового сервера)`| 3600 |
| `A`      | `r7mx1`                      | `192.168.25.91`        | 3600 |
| `SRV`    | `_autodiscover._tcp`        | `10 0 443 r7mx1` | 3600 |

---

#### 2.Создание XML-файла Autodiscover

Autodiscover в Р7 реализуется через **XML/JSON-файл** на веб-сервере.

Создайте папку `mkdir /var/www/autodiscover`

![image](https://github.com/user-attachments/assets/19bd8513-16cb-4475-9a54-3bc4a709fc3f)

Создайте файл `touch /var/www/autodiscover/autodiscover.xml` 

![image](https://github.com/user-attachments/assets/8f0332f6-9a89-4287-a723-131e17474bb4)

Создайте файл `nano /var/www/autodiscover/autodiscover.xml` заполните содержимым:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Autodiscover xmlns="http://schemas.microsoft.com/exchange/autodiscover/responseschema/2006">
  <Response>
    <Account>
      <AccountType>email</AccountType>
      <Protocol>
        <Type>IMAP</Type>
        <Server>r7mx1.it.company.lan</Server>
        <Port>993</Port>
        <LoginName>%EMAILADDRESS%</LoginName>
        <SSL>on</SSL>
        <AuthRequired>on</AuthRequired>
      </Protocol>
      <Protocol>
        <Type>SMTP</Type>
        <Server>r7mx1.it.company.lan</Server>
        <Port>587</Port>
        <SSL>starttls</SSL>
        <AuthRequired>on</AuthRequired>
      </Protocol>
    </Account>
  </Response>
</Autodiscover>
```
![image](https://github.com/user-attachments/assets/e6c16ee5-eff0-4f36-a9a1-9837e7d9e2c2)

---

#### 3.Настройка веб-сервера (Nginx)
Добавьте конфигурацию в файл `nano /etc/nginx/conf.d/autodiscover.conf`:

```nginx
server {
    listen 443 ssl;
    server_name autodiscover.it.company.lan;

    # SSL-сертификат (должен включать autodiscover.ваша-компания.ru)
    ssl_certificate /etc/nginx/ssl/it.company.lan.crt;
    ssl_certificate_key /etc/nginx/ssl/it.company.lan.key;

    # Основной endpoint
    location = /autodiscover/autodiscover.xml {
        alias /var/www/autodiscover/autodiscover.xml;
        default_type application/xml;
    }

    # Для совместимости с Outlook
    location = /Autodiscover/Autodiscover.xml {
        alias /var/www/autodiscover/autodiscover.xml;
        default_type application/xml;
    }

    # Thunderbird autoconfig
    location = /.well-known/autoconfig/mail/config-v1.1.xml {
        alias /var/www/autodiscover/thunderbird.xml;
        default_type application/xml;
    }
}
```
![image](https://github.com/user-attachments/assets/3fe13c1b-b054-4c0d-a907-62e33b39e9f3)

Перезагрузите Nginx:  
```bash
проверить запись на ошибки
nginx -t

systemctl reload nginx
```
![image](https://github.com/user-attachments/assets/ab1252d7-ca2b-4de7-87d6-5e1e295dd8cd)

---

#### 4.Дополнительно: Файл для Thunderbird
Создайте файл `nano /var/www/autodiscover/thunderbird.xml`:
```xml
<?xml version="1.0"?>
<clientConfig version="1.1">
  <emailProvider id="it.company.lan">
    <domain>it.company.lan</domain>
    <displayName>Почта Вашей Компании</displayName>
    <incomingServer type="imap">
      <hostname>r7mx1.it.company.lan</hostname>
      <port>993</port>
      <socketType>SSL</socketType>
      <authentication>password-encrypted</authentication>
    </incomingServer>
    <outgoingServer type="smtp">
      <hostname>r7mx1.it.company.lan</hostname>
      <port>587</port>
      <socketType>STARTTLS</socketType>
      <authentication>password-encrypted</authentication>
    </outgoingServer>
  </emailProvider>
</clientConfig>
```
---

#### 5.Проверка работы
1. **Тест в браузере**:  
   Откройте `https://autodiscover.it.company.lan/autodiscover/autodiscover.xml` → Должен отобразиться XML.

2. **Тест через Outlook**:
   ```powershell
   Test-EmailAutoConfiguration -Identity info@it.company.lan -Protocol Autodiscover

3. **Тест через telnet/openssl**:
   ```bash
   openssl s_client -connect autodiscover.it.company.lan:443
   GET /autodiscover/autodiscover.xml HTTP/1.1
   Host: autodiscover.it.company.lan
   ```
   → Убедитесь, что возвращается XML.```
---

#### 6.Решение проблем
| Ошибка | Решение |
|-------|---------|
| *404 Not Found* | Проверьте пути в Nginx и права доступа к файлам (`chmod 644`) |
| *SSL-ошибки* | Убедитесь, что сертификат включает `autodiscover.it.company.lan` |
| *Outlook не подхватывает настройки* | Добавьте SRV-запись в DNS |
| Сертификат не доверенный          | Используйте сертификат от Let's Encrypt или коммерческого ЦС. |
| Клиент игнорирует Autodiscover    | Убедитесь, что в DNS есть CNAME и SRV-запись.                 |
| Блокировка фаерволом              | Откройте порт 443 для `autodiscover.it.company.lan`.        |
| Неверный Content-Type             | Укажите в веб-сервере: `application/xml`.                     |

---

#### 7.Оптимизация для Р7
1. **Интеграция с LDAP/AD**:  
   В файле `autodiscover.xml` укажите:
   ```xml
   <LoginName>%USERNAME%@it.company.lan</LoginName>
   ```

2. **Поддержка ActiveSync**:  
   Добавьте в XML:
   ```xml
   <Protocol>
     <Type>ActiveSync</Type>
     <Server>r7mx1.it.company.lan</Server>
     <Port>443</Port>
     <SSL>on</SSL>
   </Protocol>
   ```

3. **Безопасность**:  
   Ограничьте доступ по IP в Nginx:
   ```nginx
   allow 192.168.1.0/24;
   allow 203.0.113.5;
   deny all;
   ```
---

### Важные особенности Р7:
1. **Стандартные порты**:
   - IMAPS: 993
   - SMTPS: 465
   - Submission: 587

2. **Аутентификация**:  
   Для Outlook требуется:
   ```xml
   <AuthRequired>on</AuthRequired>
   ```

3. **Поддержка переменных**:  
   Р7 понимает:
   - `%EMAILADDRESS%` → user@domain.ru
   - `%USERNAME%` → логин без домена


### 8.Альтернатива: Autoconfig
Для совместимости с Thunderbird создайте дополнительно файл:  
`https://mail.ваша-компания.ru/.well-known/autoconfig/mail/config-v1.1.xml`

Пример:
```xml
<?xml version="1.0"?>
<clientConfig version="1.1">
  <emailProvider id="ваша-компания.ru">
    <domain>ваша-компания.ru</domain>
    <incomingServer type="imap">
      <hostname>mail.ваша-компания.ru</hostname>
      <port>993</port>
      <socketType>SSL</socketType>
      <authentication>password-cleartext</authentication>
    </incomingServer>
    <outgoingServer type="smtp">
      <hostname>mail.ваша-компания.ru</hostname>
      <port>587</port>
      <socketType>STARTTLS</socketType>
      <authentication>password-cleartext</authentication>
    </outgoingServer>
  </emailProvider>
</clientConfig>
```
---

