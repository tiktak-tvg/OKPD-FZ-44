## Установка Корпоративного сервера 2024 без графического интерфейса offline-версии(без интернета) для ОС Astra.

Установка производится на VM Ware Workstation, версия ОС Астра 1.7.4

Установка состоит из нескольких этапов:

1. Предварительная подготовка.
2. Установка необходимых служб и баз данных для почтового сервера 2024 offline-версии.
3. Установка почтового сервера R7.
4. Настройка почтового сервера.
5. Настройка почтовых протоколов в DNS на доменном сервере ALD PRO.
6. Настройка API/служб Dovecot и Postfix.
7. Настройка почтового клиента Органайзер на клиентском компьютере.
8. Работа в Органайзере.

### 1. Предварительная подготовка.   

Скачаваем образ `1.7.4-24.04.2023_14.23.iso`

#### Системные требования. 
- Поддерживается только 64-битная архитектура. 
- Процессор: рекомендуется от 2 ядер 
- Оперативная память: рекомендуется от 8 Гб 
- Свободное место на жестком диске: рекомендуется желательно не меньше 80 Гб 
- Операционная система из перечня поддерживаемых 
- Подключение к сети Интернет 
- Требуется сертификат типа wildcard

>[!WARNING]
>Минимальные системные требования рассчитаны при одновременной работе до 300 пользователей.

Устанавливаем виртуальную машину ОС Астра 1.7.4 из образа
```bash
1.7.4-24.04.2023_14.23.iso`
```
При установке операционной системы выберите пункты <br>
**«Средства работы с интернет»** <br>
**«Консольные утилиты»** <br>
**«Средства удаленного подключения SSH»** <br>
во избежание сообщений об отсутствующих зависимостях <br>

Для упрощения в работе временно даём разрешение на подключению по ssh для суперадмина root

Открываем терминал меняем пароль на root и даём разрешение на подключение в файле `/etc/ssh/sshd_config`
```bash
$ sudo -i
# sed -i 's/^#*PermitRootLogin.*/PermitRootLogin yes/' /etc/ssh/sshd_config
```
![image](https://github.com/user-attachments/assets/f97a31ec-cc05-444a-9bbf-9670397f860e)

Если есть доступ к общим папкам по сети, тогда подключаем сетевую общую шару. Выполняем команду:
```bash
mkdir /mnt/myshare
mount -t cifs //путь до вашей шары  /mnt/myshare –o username=user,password=pass,wr,nounix,iocharset=utf8
```

Открываем репозитории `` nano /etc/apt/sources.list`` и комментируем все репозитории

![image](https://github.com/user-attachments/assets/e96855d8-29ec-4034-8d3e-da9cadd312b1)

Для корректной установки, архив поместить в директории `/mnt`
```bash
cd /mnt
```
Скачайте архив Корпоративный сервер 2024 для установки 

- Оффлайн установкаТекущая версия: 2.0.2024.14752
- Модуль Р7 Диск, Модуль Р7-Админ,
- Модуль Р7-Календарь, Модуль Р7-Почта, Модуль Р7-Проекты

![image](https://github.com/user-attachments/assets/5a70f184-9664-4d04-b2b3-9983295d8222)

Ссылка: [https://r7-office.ru/downloadserver#retail]

![image](https://github.com/user-attachments/assets/46376d55-c05c-464a-8b1a-53e62ee45c6e)

![image](https://github.com/user-attachments/assets/495b298b-52d6-437e-b584-4ac6d80fbcec)

![image](https://github.com/user-attachments/assets/3a9287b7-68d2-4bbe-a8a3-9136fa1fddd5)

Скачайте архив `cddisk.zip` который содержит пакеты для корпоративного сервера 2024. 

Ссылка: [https://nct.r7-office.ru/link/549705e3-5e21-49f1-afc4-57c88ecae674]

![image](https://github.com/user-attachments/assets/df7ca167-4541-432a-b97f-8f7a8e98d9ff)

Перетащите его в директорию /mnt  

Зайдите на терминале в директорию со скаченными архивами и распакуте их в корень папки `/mnt`

Распакуйте архив `CDinstall_2.0.2024.14752_Astra_1.7.4_offline.zip`
```bash
cd /mnt
unzip CDinstall_2.0.2024.14752_Astra_1.7.4_offline.zip
unzip cddisk.zip
```
![image](https://github.com/user-attachments/assets/d8f008ef-6e3e-4171-9c89-b628d56b03ad)

![image](https://github.com/user-attachments/assets/4db0bdde-9d56-47c1-a66d-b6a46219d129)

![image](https://github.com/user-attachments/assets/d0403d17-7481-4793-afb7-282de1c4643e)

После как распаковали получились две папки ``CDDiskPack`` и ``cddisk``

Перейдите в каталог ``cddisk`` и установите пакеты командой ``install.sh``
```
cd cddisk
chmod +x install.sh
./install.sh
```
![image](https://github.com/user-attachments/assets/6f188292-8344-4c91-8724-a3a2ea93e269)

![image](https://github.com/user-attachments/assets/01c8ed7c-29e1-46a8-80a4-2cb419f00b44)

Проверяем что распаковалось всё без ошибок и продолжаем установку.

>[!WARNING]
>Для offline установки требуется установить пакеты в систему и подключить iso образ установочного диска операционный системы в папку distr.

![image](https://github.com/user-attachments/assets/b75c7e92-c332-4eae-a2f6-b865d5333dff)


>[!WARNING]
>Для корректной работы Корпоративного сервера обязательно требуется настройка HTTPS. Перед установкой, скопируйте crt и key файлы в папку ``/mnt/CDDiskPack/CDinstall_Astra_1.7.4/sslcert``. 

Потребуется использовать ssl сертификат типа wildcard с соответствующей А записью (пример, ``*.yourdomain.ru``) на используемом DNS сервере в сети сервера. 

Имя файла должно содержать название домена и расширение. Рекомендуем в .crt указывать всю цепочку сертификатов, домен, промежуточные и корневой. Например, для домена r7.ru имена файлов должны быть ``it.company.lan.crt и it.company.lan.key``.

![image](https://github.com/user-attachments/assets/c2c46672-701f-477e-b184-8817dbf51fd9)

Отключаем полностью доступ в интернет в файле nano ``/etc/network/interfaces`` закомментируем шлюз

![image](https://github.com/user-attachments/assets/bb502064-23a8-46ff-882a-6e3974a611a1)

Проверяем

![image](https://github.com/user-attachments/assets/725be3cb-fe20-4b09-9aac-a2fcc16c3763)

### 2. Установка необходимых служб и баз данных для почтового сервера 2024 offline-версии

Теперь запускаем установку

```bash
cd /mnt/CDDiskPack/CDinstall_Astra_1.7.4/
chmod +x offline-installer.sh
./offline-installer.sh
```

![image](https://github.com/user-attachments/assets/a3f7c38e-fc38-46b6-94fe-16cac330c3fb)

Чистая установка.

Если требуется выполнить чистую установку (удалит имеющуюся инсталляцию Корпоративный сервер 2024 и зависимости):

Выберите. Да

<hr>

![image](https://github.com/user-attachments/assets/4fd55bad-acb9-49ce-9985-ac0f43881b55)
 
Установка СУБД на локальную ВМ.

Для установки PostgreSQL (при инсталляции всё в одном) на локальный компьютер:

Выберите. Да

<hr>

![image](https://github.com/user-attachments/assets/df64e74e-0c34-44ac-b978-a05e56fc5651)

Установка Сервера Документов.

Для его установки: Выберите. Да

<hr>

![image](https://github.com/user-attachments/assets/1f24f651-b632-41cd-a803-e88a3d79db7c)


Необходимо ввести пароль-секрет для защищённого доступа между Корпоративный сервер 2024 и Сервером Документов.

<hr>

![image](https://github.com/user-attachments/assets/551af633-bb6e-45c1-88cb-516a0f4067bd)


Пароль для Базы Данных DS.

<hr>

![image](https://github.com/user-attachments/assets/ca77e6dc-0bb6-4bc0-99b5-33879d15d8f5)

Установка api и web диска.

Для его установки: Выберите. Да

<hr>

![image](https://github.com/user-attachments/assets/961ab686-f66b-449e-bee2-fa8d581b6e58)

Тип СУБД Корпоративный сервер 2024.

Выберите PostgreSQL

<hr>

![image](https://github.com/user-attachments/assets/2851b0be-5601-41a8-9546-660536d10d7a)

Создание БД. Выберите. Да

<hr>

![image](https://github.com/user-attachments/assets/e53b957e-ffd4-4f72-837c-15c8e15ffca8)

Хост СУБД

При локальной установки. Выберите. Ок

>[!Warning]
>Если СУБД установлена отдельно, укажите ip или имя хоста

<hr>

![image](https://github.com/user-attachments/assets/efb59610-5587-4b89-881c-68e17bb8f46c)

По умолчанию 5432 используется. Если настроен другой, указать его. Оставляем как есть.

<hr>

![image](https://github.com/user-attachments/assets/5d8652cd-4b91-42a7-85d0-6598c6d04f99)

Пользователь с правами создания БД и пользователей.

По умолчанию ccdisk

<hr>

![image](https://github.com/user-attachments/assets/50516ca5-1416-4627-b177-17797cef2dff)

Пароль пользователя с правами создания БД и пользователя ccdisk.

<hr>

![image](https://github.com/user-attachments/assets/2338b1fd-8a30-4e90-93b5-5976a0f822f2)

Пароль для пользователя БД Корпоративный сервер 2024.

Укажите пароль для пользователя cddisk.

<hr>

![image](https://github.com/user-attachments/assets/75e1d240-bfc7-4ca1-9121-0ab0c0434631)

coremachinkey от CS.

Изменить на актуальный, если есть Р7-Офис Корпоративный сервер и нажмите ОК, если нет, нажмите Ок без редактирования.

нажмите Ок

<hr>

![image](https://github.com/user-attachments/assets/eeb7efb0-bef6-4b9c-8681-93b390253895)

>[!Warning]
>Если Вы выбрали установки без HTTPS, то, после инсталляции, почтовый сервер работать не будет. Он не запустится.

<hr>

![image](https://github.com/user-attachments/assets/f84b01c3-e939-4a99-be2d-d167faaec63c)

Укажите домен. Необходимо указать домен, в котором у Вас созданы записи.

Например, it.company.lan

<hr>

![image](https://github.com/user-attachments/assets/66be4d92-4f9d-4209-87bf-a61562ce9f6c)

Префикс Р7-Диск.

Указать имя, которое будет открываться в браузере для веб р7-Диска.

Например, если Вы хотите, чтобы открылся Р7-Диск по адресу cdisk.it.company.lan, то указать нужно именно cdisk, без указания домена.

Также, необходимо сделать соответствующую А запись в DNS.

<hr>

![image](https://github.com/user-attachments/assets/b59d6e51-4f55-464a-bb56-5085017c1fc2)

Префикс Р7-Админ.

Указать имя, которое будет открываться в браузере для веб админской панели.

Например, если Вы хотите, чтобы открылся Р7-Админ по адресу admin.it.company.lan, то указать нужно именно admin, без указания домена.

Также, необходимо сделать соответствующую А запись в DNS.

<hr>

![image](https://github.com/user-attachments/assets/753c4eb6-0801-4e24-91c8-edfeaae3a5eb)

Префикс Р7-Контакты.

Указать имя, которое будет открываться в браузере для веб клиента контактов.

Например, если Вы хотите, чтобы открылся Р7-Контакты по адресу contacts.it.company.lan, то указать нужно именно contacts, без указания домена.

Также, необходимо сделать соответствующую А запись в DNS.

<hr>

![image](https://github.com/user-attachments/assets/40ba1167-8e99-40dd-b440-ebda9a94799e)

Префикс Р7-Почта.

Указать имя, которое будет открываться в браузере для веб клиента почты.

Например, если Вы хотите, чтобы открылся Р7-Почта по адресу objects.it.company.lan, то указать нужно именно objects, без указания домена.

Также, необходимо сделать соответствующую А запись в DNS.

<hr>

![image](https://github.com/user-attachments/assets/9415ae9e-2b7b-438e-8bcb-8a4b74946558)

Префикс Р7-Почта.

Указать имя, которое будет открываться в браузере для веб клиента почты.

Например, если Вы хотите, чтобы открылся Р7-Почта по адресу cmail.it.company.lan, то указать нужно именно cmail, без указания домена.

Также, необходимо сделать соответствующую А запись в DNS.

<hr>

![image](https://github.com/user-attachments/assets/4bdbbe02-4d4b-4fa2-ab72-f08ebbdcb47d)

Префикс Р7-Календарь.

Укажите имя, которое будет открываться в браузере для веб календаря.

Например, если Вы хотите, чтобы открылся Р7-Календарь по адресу calendar.it.company.lan, то указать нужно именно calendar, без указания домена.

Также, необходимо сделать соответствующую А запись в DNS.

<hr>

![image](https://github.com/user-attachments/assets/2f42133d-8ec8-4d67-86d9-8a73e9b7e166)

Префикс Р7-Сервер Документов.

Указать имя, которое будет открываться в браузере для веба сервера документом.

Например, если Вы хотите, чтобы открылся Р7-Сервер Документов по адресу ds.it.company.lan, то указать нужно именно ds, без указания домена.

Также, необходимо сделать соответствующую А запись в DNS.

<hr>

### 3. Установка почтового сервера R7.

![image](https://github.com/user-attachments/assets/7a5864f9-55a1-41a3-85d3-2e086d5960a0)

Установка почтового сервера - ДА

---

![image](https://github.com/user-attachments/assets/384e52b1-47b3-4b56-b9d7-ea2ab735353c)

Выберите базу данных для установки.

---

![image](https://github.com/user-attachments/assets/7c186766-c1a5-450a-b2bd-fed838bb031c)

В данном окне надо внести имя вашего MX почтового сервера, который прописан в записи А на контроллере домена. 

В моём случае запись А в DNS такая ``A  r7mx1.it.company.lan  TTL``.

Если MX запись выглядит, как ``r7mx1.it.company.lan``, то ввести необходимо просто hostname почтового сервера **r7mx1**.

<hr>

![image](https://github.com/user-attachments/assets/84aeb3b3-b18f-45fa-9db8-88dccae1f1aa)

Укажите ip адрес.

Укажите внешний ip адрес сервера, для корректной работы почтового сервера.

Если указать приватный внутренний ip, то почта будет работать только внутри сети организации.

<hr>

![image](https://github.com/user-attachments/assets/61ee719d-51e6-406a-a17d-e23adf49774d)

Пароль root для Postgresql.

Укажите пароль, который будет задан пользователю root Postgresql.

<hr>

![image](https://github.com/user-attachments/assets/6c8fdf01-d493-4fbb-99e2-4b56596f149d)

Установка SpamAssassin.

Если требуется установка.

Выберите 1.

<hr>

![image](https://github.com/user-attachments/assets/32a3af30-3c02-43f5-aabb-90ed074ee08a)

Установка OpenDKIM.

Если требуется установка. Выберите. Да

<hr>

**DKIM** — это технология аутентификации электронной почты, которая позволяет проверить, что письмо действительно отправлено от имени домена и не было изменено в процессе доставки. DKIM использует цифровую подпись, чтобы подтвердить подлинность отправителя и целостность содержимого письма.<br>
DKIM-запись хранится в DNS домена в формате TXT-записи и выглядит примерно так:
```bash
selector._domainkey.it.company.lan.  TXT  "v=DKIM1; k=rsa; p=MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQC..."
```
После инсталляции в консоли будет предложено сделать TXT запись.

![image](https://github.com/user-attachments/assets/29b8a56f-4da1-46e7-acaf-4a4b4c1814a2)

**Перезагружаем севрвер**

Проверяем работу службы ``rabbitmq``.

![image](https://github.com/user-attachments/assets/3672e338-7388-4579-9480-13eba974e74d)

Обратно раскомментируем шлюз и репозитории.

![image](https://github.com/user-attachments/assets/228cff78-e77b-4ff6-8ead-7fe020f835c9)

![image](https://github.com/user-attachments/assets/815645a7-c5d9-4f2e-b576-48b409844458)

### 4. Настройка почтового сервера Р7.

Открываем браузер Мозилла, вводим адрес почтового сервер, чтобы попасть в панель администрации.
```bash
https://admin.it.company.lan
```

![image](https://github.com/user-attachments/assets/4ebab8ed-fbfc-4723-a2a0-dbafa6bfbfcc)

![image](https://github.com/user-attachments/assets/00519bae-6323-4e46-bca9-7deb495ae38a)

![image](https://github.com/user-attachments/assets/fb373ca0-d024-4850-b169-b67b52f10c36)

![image](https://github.com/user-attachments/assets/e0f61e64-b714-4a3e-a7a4-8ed41c8d2523)

![image](https://github.com/user-attachments/assets/e1c6ca11-968a-4dc7-91e4-0877cbd16fb3)


В настройках КС24 указать корректные порты ПС.

- **POP сервер** - 995  Тип соединения: None 
- **IMAP сервер** - 993 Тип соединения: SslOnConnect 
- **SMTP сервер** - 465 Тип соединения: SslOnConnect

Создать почтового пользователя и проверить работу почты.

Переходим во вкладку  ![image](https://github.com/user-attachments/assets/a143ef65-fa75-4438-9fd0-d4f45d8cd2c1) ``Почта``

![image](https://github.com/user-attachments/assets/0490e089-4f78-4ea4-b219-9c27a88d009e)

![image](https://github.com/user-attachments/assets/ef89b633-2f21-40aa-8123-2898c7222516)

![image](https://github.com/user-attachments/assets/0120fac9-ad57-4cb0-b1f5-d65358016b5c)

![image](https://github.com/user-attachments/assets/c78b0506-fea7-4f9b-a230-9bf73546c794)

>[!Warning]
>После установки почтового сервера прописываем записи в DNS для работы почтового сервера.
>Необходимо добавить SRV записи IMAP, IMAPS, POP, SMTP и AUTODISCOVER для автоматического обнаружения протоколов. Создать файл ``autodiscover.xml``.

Остальные настройки находятся в файлах.

- Настройка в файле Autodiscover.md
- Настройка SRV записей.md
- Настройка почтовых протоколов.md
- Настройка API.md
- Установка Органайзера.md
- Установка модуля графики.md

















