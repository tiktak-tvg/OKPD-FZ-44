![image](https://github.com/user-attachments/assets/f50eada9-4fd0-49b9-932b-0758da912a42)

Настройка SRV-записей для почтового сервера помогает клиентам автоматически находить сервисы (SMTP, IMAP и т.д.) через DNS. Вот пошаговая инструкция:

### 1. **Основные параметры SRV-записи**
Формат записи:  
`_Сервис._Протокол.Домен. TTL Класс SRV Приоритет Вес Порт Цель`

Пример:  
`_submission._tcp.example.com. 3600 IN SRV 10 5 587 mail.example.com.`

### 2. **Ключевые сервисы для почты**
- **SMTP (отправка):**
  - `_submission._tcp` (порт 587) — для клиентской отправки
  - `_smtps._tcp` (порт 465) — SMTP over SSL
- **IMAP (получение):**
  - `_imap._tcp` (порт 143)
  - `_imaps._tcp` (порт 993) — IMAP over SSL
- **POP3 (получение):**
  - `_pop3._tcp` (порт 110)
  - `_pop3s._tcp` (порт 995) — POP3 over SSL
- **Autodiscover (автонастройка):**
  - `_autodiscover._tcp` (порт 443)

### 3. **Примеры записей**
```dns
; Отправка почты (клиенты)
_submission._tcp.example.com.  3600 IN SRV 10 5 587 mail.example.com.

; Получение почты (IMAP)
_imaps._tcp.example.com.       3600 IN SRV 10 0 993 mail.example.com.

; Autodiscover (для Outlook/Thunderbird)
_autodiscover._tcp.example.com. 3600 IN SRV 10 0 443 mail.example.com.
```

### 4. **Параметры**
- **Приоритет (Priority):** Меньше значение → выше приоритет.  
- **Вес (Weight):** Балансировка нагрузки между серверами одного приоритета.  
- **Порт (Port):** TCP-порт сервиса (587, 993 и т.д.).  
- **Цель (Target):** FQDN почтового сервера (должен иметь A/AAAA-запись).  

### 5. **Как добавить запись**
1. Зайдите в панель управления DNS-хостингом (Cloudflare, REG.RU и т.д.).  
2. Создайте новую запись типа **SRV**.  
3. Заполните поля по шаблону:  
   - **Service:** `_submission._tcp` (или другой сервис)  
   - **Target:** `mail.example.com` (ваш сервер)  
   - **Priority:** `10`  
   - **Weight:** `5`  
   - **Port:** `587`  

### 6. **Проверка**
Используйте команды:  
```bash
dig SRV _submission._tcp.example.com
# или
nslookup -type=SRV _submission._tcp.example.com
```

### Важные нюансы
1. **Поддержка клиентов:**  
   SRV-записи работают не со всеми клиентами (Outlook поддерживает для Autodiscover, Thunderbird — для IMAP/SMTP).  
2. **Обязательные A/AAAA-записи:**  
   Убедитесь, что `mail.example.com` разрешается в IP-адрес.  
3. **Autodiscover:**  
   Для Exchange/Office 365 используйте `_autodiscover._tcp` + HTTPS-ресурс `https://autodiscover.example.com/autodiscover/autodiscover.xml`.  
4. **Безопасность:**  
   Для портов 465/587/993 используйте SSL/TLS.  

### A, TXT, PTR и MX-записи
- **MX-записи:** Для входящей почты (не для SRV).  

Необходимо добавить запись А (ваш почтовый сервер hostname -f) и обратную запись, а также запись MX и TXT v=spf1 +mx ~all
Пример:

|        |it.company.lan       	|TTL          	|Приоритет
|--------|-----------------------|---------------|--------------|
|MX	 |r7mx1.it.company.lan  |300            |10		|
|TXT     |it.company.lan         |TTL            |		|
|	 |v=spf1+mx~all          |300            |		|
|A       |r7mx1.it.company.lan  |TTL            |		|
|	 |192.168.18.141          |300            |		|
   
Теперь по подробней.

**MX-запись** — это тип DNS-записи, который указывает, какой сервер отвечает за прием электронной почты для домена.<br> 
MX-запись указывает на доменное имя почтового сервера (например, в нашем случае - r7mx1.it.company.lan), которое должно иметь A-запись (IPv4), чтобы связать домен с IP-адресом. Чем меньше число, тем выше приоритет.

>MX-записи не могут указывать напрямую на IP-адрес, только на доменное имя.

Проверить MX-записи можно через команды в терминале:
```bash
nslookup -type=mx it.company.lan
или так
dig mx it.company.lan 
```
Запись ``v=spf1 +mx ~all`` в DNS — это SPF-запись, которая помогает защитить домен от подделки электронной почты (спуфинга). <br>Она указывает, какие серверы имеют право отправлять письма от имени вашего домена.<br>
SPF-запись v=spf1 +mx ~all разрешит отправку писем только с r7mx1.it.company.lan, а письма с других серверов будут отклонены или помечены.

Если всё настроили среяйте с этими настройками.

![image](https://github.com/user-attachments/assets/b5879541-2b67-4e66-9a13-ff081426bbf8)

![image](https://github.com/user-attachments/assets/860e925f-a5c0-49e4-88fb-d4044db46a81)


