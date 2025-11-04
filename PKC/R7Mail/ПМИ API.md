#### ПРОГРАММА И МЕТОДИКА ИСПЫТАНИЙ ПРОГРАМНОГО ОБЕСПЕЧЕНИЯ ИМПОРОЗАМЕЩЕННОЙ ВЭП 
##### 1.1 Создание почтового ящика
Создание почтового ящика и подключение ранее созданному  пользователю из интерфейса корпоративного сервера:
1. Откройте модуль «Р7-управление»
2. В верхней панели выберете «Пользователи»
3. Выберете пользователя для которого нужно создать почтовый ящик и откройте его для редактирования.
4. В списке настроек профиля выберете «Почтовые аккаунты»
5. Выберете  «Создать новый email» 
6. Введите email, укажите домен, и задайте пароль и нажмите создать.

<img width="1332" height="405" alt="image" src="https://github.com/user-attachments/assets/cf5b9a63-a294-474c-8022-be25d209d1d2" />

<img width="901" height="858" alt="image" src="https://github.com/user-attachments/assets/1aba3d1c-a2cb-48e5-849b-8255568afe96" />

<img width="898" height="516" alt="image" src="https://github.com/user-attachments/assets/c69b75cc-20f7-441c-8681-5c3766759c37" />

<img width="1578" height="387" alt="image" src="https://github.com/user-attachments/assets/253e1dcb-fe56-47c7-b9b6-cd283fa7a0d8" />

<img width="1563" height="580" alt="image" src="https://github.com/user-attachments/assets/8e5b0b97-6bab-479f-82ae-444d62521e7d" />

<img width="1575" height="430" alt="image" src="https://github.com/user-attachments/assets/8e5c8b1b-653a-4c60-9ff9-30eba04f2a93" />

<img width="1573" height="768" alt="image" src="https://github.com/user-attachments/assets/15d2e7ab-7dd2-472c-8343-f211e29b1e77" />

<img width="1573" height="326" alt="image" src="https://github.com/user-attachments/assets/5c6bf746-ef13-4d6e-9f56-e61661ee9514" />

<img width="1579" height="546" alt="image" src="https://github.com/user-attachments/assets/c7768a79-9a6b-42ac-a9e5-8aaa7923cc9a" />

<img width="677" height="466" alt="image" src="https://github.com/user-attachments/assets/ad47dfdb-3a8b-4b51-8331-ea44ebf442df" />

##### Создание почтового ящика через CURL запрос:

Исправьте запрос в соответствии с параметрами вашего сервера:
```
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <твой_JWT_токен>" -d '{"email":"test@domain.ru","password":"mail_password"}' https://admin.domain.ru/apimail/create_user --user admin:PassworD 
```
Описание:
- email — почтовый ящик для создания пользователя.
- password — пароль для пользователя почтового ящика.
- https://admin.domain.ru/apimail/create_user — адрес вызова API.
- admin:PassworD  - пользователь и пароль созданные в базовой авторизации на веб сервере.

В исправленном запросе добавляем пользователя `КОМОВ А.Н.` 

##### P.s. незабудьте поменять пароль на admin:
```
#!/bin/bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2VtYWlsIjoic3VwZXJhZG1pbkBOb25lIiwic2NvcGVzIjpbImdlbmVyYXRlX3Rva2VuIiwiYXNzaWduX3Njb3BlIiwicmV2b2tlX3Njb3BlIiwibGlzdF9hZG1pbnMiLCJjcmVhdGVfdXNlciIsImNoYW5nZV9wYXNzd29yZCIsImNoYW5nZV9lbWFpbCIsImRlbGV0ZV91c2VyIiwiY3JlYXRlX2FsaWFzIiwiY3JlYXRlX2FsaWFzX211bHR5IiwiY2hhbmdlX2FsaWFzIiwiZGVsZXRlX2FsaWFzIiwiY3JlYXRlX2RvbWFpbiIsImRlbGV0ZV9kb21haW4iLCJjaGVja19lbWFpbCIsIm1haWxib3hfc2l6ZSIsInNldF9xdW90YSIsImNyZWF0ZV9zaGFyZWRfbWFpbGJveCIsImFkZF9hY2wiLCJjaGFuZ2VfYWNsIiwicmVtb3ZlX2FjbCIsImdldF9hY2wiLCJnZXRfbGltaXRzIiwic2V0X3NlbmRfbGltaXQiLCJkZWxldGVfbGltaXQiLCJtb2RpZnlfbGlzdCIsIm1haWxfcXVldWUiXX0.rNEQ3HglhwQfAirn5dNZHWayNVRpWlHDfgrNeKt5vw0" -d '{"email":"komov@rosreestr.ru","password":"Qwerty5+"}' https://admin.rosreestr.ru/apimail/create_user --user admin:'ПАРОЛЬ'
```
<img width="837" height="97" alt="image" src="https://github.com/user-attachments/assets/e49b8129-ab8a-49cf-aee6-283f88b52539" />

**Теперь созданого пользователя КОМОВ А.Н. нужно добавить в админку**

<img width="899" height="856" alt="image" src="https://github.com/user-attachments/assets/bb445f9c-a5a1-4976-ad44-2433dca9d633" />
<img width="903" height="857" alt="image" src="https://github.com/user-attachments/assets/44753467-709d-4deb-a564-40ed2d783dd8" />
<img width="1574" height="568" alt="image" src="https://github.com/user-attachments/assets/e501a369-45a0-46a2-adbd-87bc340dd0ea" />
<br>

***Проверим, пользователей на почтовом клиенте***

<br>

<img width="800" height="604" alt="image" src="https://github.com/user-attachments/assets/d60ab6f0-ce94-476b-acb7-ef391ea117f4" />
<img width="806" height="616" alt="image" src="https://github.com/user-attachments/assets/e4b240b1-7177-4e99-9542-a15f64ddf265" />
<img width="804" height="603" alt="image" src="https://github.com/user-attachments/assets/f29a377b-9d72-4c0c-ab11-242009ff7a45" />
<img width="804" height="604" alt="image" src="https://github.com/user-attachments/assets/ecf1a202-5485-4c5c-9e58-b4876fad6384" />
<img width="1271" height="472" alt="image" src="https://github.com/user-attachments/assets/56ecd3d2-0107-437a-b149-794437b8c1b1" />

Как видим почта работает!!!

##### 1.2 Удаление почтовых ящиков
Удаление почтового ящика (с удалением данных почты):

Исправьте запрос в соответствии с параметрами вашего сервера:
```
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer  <твой_JWT_токен>" -d '{"email":"test@domain.ru"}' https://admin.domain.ru/apimail/delete_user --user admin:PassworD
```
Описание:
- email — почтовый ящик для удаления.
- https://admin.domain.ru/apimail/delete_user — адрес вызова API.
- admin:PassworD  - пользователь и пароль созданные в базовой авторизации на веб сервере.

В исправленном запросе удаляем пользователя `komov@rosreestr.ru` 

P.s. незабудьте поменять пароль на admin:
```
#!/bin/bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2VtYWlsIjoic3VwZXJhZG1pbkBOb25lIiwic2NvcGVzIjpbImdlbmVyYXRlX3Rva2VuIiwiYXNzaWduX3Njb3BlIiwicmV2b2tlX3Njb3BlIiwibGlzdF9hZG1pbnMiLCJjcmVhdGVfdXNlciIsImNoYW5nZV9wYXNzd29yZCIsImNoYW5nZV9lbWFpbCIsImRlbGV0ZV91c2VyIiwiY3JlYXRlX2FsaWFzIiwiY3JlYXRlX2FsaWFzX211bHR5IiwiY2hhbmdlX2FsaWFzIiwiZGVsZXRlX2FsaWFzIiwiY3JlYXRlX2RvbWFpbiIsImRlbGV0ZV9kb21haW4iLCJjaGVja19lbWFpbCIsIm1haWxib3hfc2l6ZSIsInNldF9xdW90YSIsImNyZWF0ZV9zaGFyZWRfbWFpbGJveCIsImFkZF9hY2wiLCJjaGFuZ2VfYWNsIiwicmVtb3ZlX2FjbCIsImdldF9hY2wiLCJnZXRfbGltaXRzIiwic2V0X3NlbmRfbGltaXQiLCJkZWxldGVfbGltaXQiLCJtb2RpZnlfbGlzdCIsIm1haWxfcXVldWUiXX0.rNEQ3HglhwQfAirn5dNZHWayNVRpWlHDfgrNeKt5vw0" -d '{"email":"komov@rosreestr.ru","password":"123QWEasd"}' https://admin.rosreestr.ru/apimail/delete_user --user admin:'ПАРОЛЬ'
```
<img width="839" height="93" alt="image" src="https://github.com/user-attachments/assets/33b05644-391c-48aa-ace9-f642c62418bd" />

Проверяем, что ящик удалён. Попробуем послать на него письмо.

<img width="1278" height="586" alt="image" src="https://github.com/user-attachments/assets/5468b810-d140-435f-95f4-f4ddf9fa062d" />
<img width="1281" height="704" alt="image" src="https://github.com/user-attachments/assets/a24b8325-562a-4bba-8619-c77875ddfa42" />

Отправить не удалось, почтового ящика не существует!!!

##### 1.3 Изменение пароля почтового ящика

Изменение пароля почтового ящика:

Исправьте запрос в соответствии с параметрами вашего сервера:
```
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer  <твой_JWT_токен>" -d  '{"email":"test@domain.ru","password":"new_password"}' https://admin.domain.ru/apimail/change_password --user admin:PassworD 
```
Описание:
- email — почтовый ящик, для которого изменяется пароль.
- password — новый пароль для пользователя почтового ящика.
- https://admin.domain.ru/apimail/change_password — адрес вызова API.
- admin:PassworD  - пользователь и пароль созданные в базовой авторизации на веб сервере.

В исправленном запросе меняем пароль на пользователя `komarov@rosreestr.ru`
```
#!/bin/bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2VtYWlsIjoic3VwZXJhZG1pbkBOb25lIiwic2NvcGVzIjpbImdlbmVyYXRlX3Rva2VuIiwiYXNzaWduX3Njb3BlIiwicmV2b2tlX3Njb3BlIiwibGlzdF9hZG1pbnMiLCJjcmVhdGVfdXNlciIsImNoYW5nZV9wYXNzd29yZCIsImNoYW5nZV9lbWFpbCIsImRlbGV0ZV91c2VyIiwiY3JlYXRlX2FsaWFzIiwiY3JlYXRlX2FsaWFzX211bHR5IiwiY2hhbmdlX2FsaWFzIiwiZGVsZXRlX2FsaWFzIiwiY3JlYXRlX2RvbWFpbiIsImRlbGV0ZV9kb21haW4iLCJjaGVja19lbWFpbCIsIm1haWxib3hfc2l6ZSIsInNldF9xdW90YSIsImNyZWF0ZV9zaGFyZWRfbWFpbGJveCIsImFkZF9hY2wiLCJjaGFuZ2VfYWNsIiwicmVtb3ZlX2FjbCIsImdldF9hY2wiLCJnZXRfbGltaXRzIiwic2V0X3NlbmRfbGltaXQiLCJkZWxldGVfbGltaXQiLCJtb2RpZnlfbGlzdCIsIm1haWxfcXVldWUiXX0.rNEQ3HglhwQfAirn5dNZHWayNVRpWlHDfgrNeKt5vw0" -d '{"email":"komarov@rosreestr.ru","password":"123qweasd"}' https://admin.rosreestr.ru/apimail/change_password --user admin:'ПАРОЛЬ'
```
<img width="836" height="108" alt="image" src="https://github.com/user-attachments/assets/0a6ed9c0-0ae2-4a1d-88d8-e9d24807aea1" />

Проверяем. Не даёт отправить, просит сменить пароль. Меняем.

<img width="1131" height="675" alt="image" src="https://github.com/user-attachments/assets/c08257ad-9977-44c0-a2f4-b7e2ba318111" />

Отправляем

<img width="1219" height="256" alt="image" src="https://github.com/user-attachments/assets/1e976e26-f098-44b8-803f-36465ad6f3cf" />

Пришло адресату!!!

#### 1.4 Изменение имени почтового ящика

Изменение имени почтового ящика (с копированием директории с почтовыми данными):

Исправьте запрос в соответствии с параметрами вашего сервера:
```
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer  <твой_JWT_токен>" -d '{"old_email":"test@domain.ru","new_email":"test2@domain.ru"}' https://admin.domain.ru/apimail/change_email --user admin:PassworD
```
Описание:
- old_email — текущий почтовый ящик.
- new_email — новое имя почтового ящика.
- https://admin.domain.ru/apimail/change_email — адрес вызова API.
- admin:PassworD  - пользователь и пароль созданные в базовой авторизации на веб сервере.

В исправленном запросе меняем имя пользователя `komarov@rosreestr.ru` на `komarovAA@rosreestr.ru`
```
#!/bin/bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2VtYWlsIjoic3VwZXJhZG1pbkBOb25lIiwic2NvcGVzIjpbImdlbmVyYXRlX3Rva2VuIiwiYXNzaWduX3Njb3BlIiwicmV2b2tlX3Njb3BlIiwibGlzdF9hZG1pbnMiLCJjcmVhdGVfdXNlciIsImNoYW5nZV9wYXNzd29yZCIsImNoYW5nZV9lbWFpbCIsImRlbGV0ZV91c2VyIiwiY3JlYXRlX2FsaWFzIiwiY3JlYXRlX2FsaWFzX211bHR5IiwiY2hhbmdlX2FsaWFzIiwiZGVsZXRlX2FsaWFzIiwiY3JlYXRlX2RvbWFpbiIsImRlbGV0ZV9kb21haW4iLCJjaGVja19lbWFpbCIsIm1haWxib3hfc2l6ZSIsInNldF9xdW90YSIsImNyZWF0ZV9zaGFyZWRfbWFpbGJveCIsImFkZF9hY2wiLCJjaGFuZ2VfYWNsIiwicmVtb3ZlX2FjbCIsImdldF9hY2wiLCJnZXRfbGltaXRzIiwic2V0X3NlbmRfbGltaXQiLCJkZWxldGVfbGltaXQiLCJtb2RpZnlfbGlzdCIsIm1haWxfcXVldWUiXX0.rNEQ3HglhwQfAirn5dNZHWayNVRpWlHDfgrNeKt5vw0" -d '{"old_email":"komarov@rosreestr.ru","new_email":"komarovAA@rosreestr.ru"}' https://admin.rosreestr.ru/apimail/change_email --user admin:'ПАРОЛЬ'
```

<img width="954" height="112" alt="image" src="https://github.com/user-attachments/assets/265670d1-52dd-45c3-9531-2a5c84e8207d" />

Проверяем. Не работает

<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/7089e158-8ee1-4e3c-b012-9a8ff8299aa9" />

Добавляем пользователя с новым именем

<img width="901" height="857" alt="image" src="https://github.com/user-attachments/assets/db9c4434-df60-4156-bdc8-63b9f74daa6a" />

<img width="899" height="510" alt="image" src="https://github.com/user-attachments/assets/e0bb69c7-81fa-4fee-b2a3-b23617e472fa" />

Проверяем. Работает

<img width="1151" height="411" alt="image" src="https://github.com/user-attachments/assets/3c725f78-fc35-40bb-bf57-58860635429f" />

<img width="1283" height="475" alt="image" src="https://github.com/user-attachments/assets/53f3d19e-94c1-4599-8f62-43649401758a" />

#### 1.5 Отправка почтового сообщения

1. Перейти в модуль Р7-почта
2. Нажать на кнопку «Создать сообщение»
3. Ввести адресата в поле «Кому», ввести тему письма и сообщение
4. Нажать отправить.

#### 1.6 Получение почтового сообщения

1. Перейти в модуль Р7-почта
2. Нажать на кнопку «Создать сообщение»
3. Ввести собственный email в поле «Кому», ввести тему письма и сообщение
4. Нажать отправить.

#### 1.7 Настройки квоты почтового ящика

Установка квоты для почтового ящика:

Метод позволяет установить квоту (ограничение на объем хранилища) для указанного почтового ящика.

Исправьте запрос в соответствии с параметрами вашего сервера:
```
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer  <твой_JWT_токен>" -d '{"email":"user@domain.ru","quota":1024}' https://admin.domain.ru/apimail/set_quota --user admin:PassworD
```
Описание:
- email — почтовый ящик, для которого устанавливается квота.
- quota — квота в мегабайтах (MB). Должна быть положительным числом.
- admin:PassworD  - пользователь и пароль созданные в базовой авторизации на веб сервере.
- Адрес вызова API:https://admin.domain.ru/apimail/set_quota

В исправленном запросе меняем квоту на пользователя r7info@rosreestr.ru

Сначала проверим какая квота есть
```
doveadm quota get -u r7info@rosreestr.ru
sh: /usr/sbin/astrase-fix-maildir: Нет такого файла или каталога
Quota name Type    Value Limit                                                                                                             %
User quota STORAGE     0     -                                                                                                             0
User quota MESSAGE     0     - 
```
```
#!/bin/bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2VtYWlsIjoic3VwZXJhZG1pbkBOb25lIiwic2NvcGVzIjpbImdlbmVyYXRlX3Rva2VuIiwiYXNzaWduX3Njb3BlIiwicmV2b2tlX3Njb3BlIiwibGlzdF9hZG1pbnMiLCJjcmVhdGVfdXNlciIsImNoYW5nZV9wYXNzd29yZCIsImNoYW5nZV9lbWFpbCIsImRlbGV0ZV91c2VyIiwiY3JlYXRlX2FsaWFzIiwiY3JlYXRlX2FsaWFzX211bHR5IiwiY2hhbmdlX2FsaWFzIiwiZGVsZXRlX2FsaWFzIiwiY3JlYXRlX2RvbWFpbiIsImRlbGV0ZV9kb21haW4iLCJjaGVja19lbWFpbCIsIm1haWxib3hfc2l6ZSIsInNldF9xdW90YSIsImNyZWF0ZV9zaGFyZWRfbWFpbGJveCIsImFkZF9hY2wiLCJjaGFuZ2VfYWNsIiwicmVtb3ZlX2FjbCIsImdldF9hY2wiLCJnZXRfbGltaXRzIiwic2V0X3NlbmRfbGltaXQiLCJkZWxldGVfbGltaXQiLCJtb2RpZnlfbGlzdCIsIm1haWxfcXVldWUiXX0.rNEQ3HglhwQfAirn5dNZHWayNVRpWlHDfgrNeKt5vw0" -d '{"email":"r7info@rosreestr.ru","quota":1024}' https://admin.rosreestr.ru/apimail/set_quota --user admin:'ПАРОЛЬ'
```
<img width="822" height="90" alt="image" src="https://github.com/user-attachments/assets/944e7163-2f63-40a7-b10f-edc92057dd80" />

<img width="825" height="198" alt="image" src="https://github.com/user-attachments/assets/48a8681b-e025-4bd8-ad70-e83373750fef" />

<img width="818" height="290" alt="image" src="https://github.com/user-attachments/assets/58d0d52c-4466-4420-8997-7acfe65119f5" />

Квота добавилась!!!

#### 1.8 Настройки псевдонима (alias) почтового ящика 

Создание нового алиаса

Исправьте запрос в соответствии с параметрами вашего сервера:
```
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer  <твой_JWT_токен>" -d '{"alias":"news@domain.ru","email":"user@domain.ru"}' https://admin.domain.ru/apimail/create_alias --user admin:PassworD
```
Описание:
- alias — адрес алиаса (например, news@domain.ru).
- email — основной почтовый ящик, на который будет перенаправляться почта.
- https://admin.domain.ru/apimail/create_alias — адрес вызова API.
- admin:PassworD  - пользователь и пароль созданные в базовой авторизации на веб сервере.

```
#!/bin/bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2VtYWlsIjoic3VwZXJhZG1pbkBOb25lIiwic2NvcGVzIjpbImdlbmVyYXRlX3Rva2VuIiwiYXNzaWduX3Njb3BlIiwicmV2b2tlX3Njb3BlIiwibGlzdF9hZG1pbnMiLCJjcmVhdGVfdXNlciIsImNoYW5nZV9wYXNzd29yZCIsImNoYW5nZV9lbWFpbCIsImRlbGV0ZV91c2VyIiwiY3JlYXRlX2FsaWFzIiwiY3JlYXRlX2FsaWFzX211bHR5IiwiY2hhbmdlX2FsaWFzIiwiZGVsZXRlX2FsaWFzIiwiY3JlYXRlX2RvbWFpbiIsImRlbGV0ZV9kb21haW4iLCJjaGVja19lbWFpbCIsIm1haWxib3hfc2l6ZSIsInNldF9xdW90YSIsImNyZWF0ZV9zaGFyZWRfbWFpbGJveCIsImFkZF9hY2wiLCJjaGFuZ2VfYWNsIiwicmVtb3ZlX2FjbCIsImdldF9hY2wiLCJnZXRfbGltaXRzIiwic2V0X3NlbmRfbGltaXQiLCJkZWxldGVfbGltaXQiLCJtb2RpZnlfbGlzdCIsIm1haWxfcXVldWUiXX0.rNEQ3HglhwQfAirn5dNZHWayNVRpWlHDfgrNeKt5vw0" -d '{"alias":"test55mx1@rosreestr.ru","email":"test55mx2@rosreestr.ru"}' https://admin.rosreestr.ru/apimail/create_alias --user admin:'ПАРОЛЬ'
```

#### 1.9	Настройка параметров переадресации почтового ящика

Создание алиаса с несколькими получателями

Исправьте запрос в соответствии с параметрами вашего сервера:
```
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer  <твой_JWT_токен>" -d '{"source":"news_users@domain.ru","destinations":"user3@domain.ru,user4@domain.ru"}' https://admin.domain.ru/apimail/create_alias_multy --user admin:PassworD
```
Описание:
- source — адрес алиаса (например, news_users@domain.ru).
- destinations — список получателей, разделенных запятой (например, user3@domain.ru,user4@domain.ru).
- https://admin.domain.ru/apimail/create_alias_multy — адрес вызова API.
- admin:PassworD  - пользователь и пароль созданные в базовой авторизации на веб сервере.

```
#!/bin/bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2VtYWlsIjoic3VwZXJhZG1pbkBOb25lIiwic2NvcGVzIjpbImdlbmVyYXRlX3Rva2VuIiwiYXNzaWduX3Njb3BlIiwicmV2b2tlX3Njb3BlIiwibGlzdF9hZG1pbnMiLCJjcmVhdGVfdXNlciIsImNoYW5nZV9wYXNzd29yZCIsImNoYW5nZV9lbWFpbCIsImRlbGV0ZV91c2VyIiwiY3JlYXRlX2FsaWFzIiwiY3JlYXRlX2FsaWFzX211bHR5IiwiY2hhbmdlX2FsaWFzIiwiZGVsZXRlX2FsaWFzIiwiY3JlYXRlX2RvbWFpbiIsImRlbGV0ZV9kb21haW4iLCJjaGVja19lbWFpbCIsIm1haWxib3hfc2l6ZSIsInNldF9xdW90YSIsImNyZWF0ZV9zaGFyZWRfbWFpbGJveCIsImFkZF9hY2wiLCJjaGFuZ2VfYWNsIiwicmVtb3ZlX2FjbCIsImdldF9hY2wiLCJnZXRfbGltaXRzIiwic2V0X3NlbmRfbGltaXQiLCJkZWxldGVfbGltaXQiLCJtb2RpZnlfbGlzdCIsIm1haWxfcXVldWUiXX0.rNEQ3HglhwQfAirn5dNZHWayNVRpWlHDfgrNeKt5vw0" -d '{"source":"test55mx1@rosreestr.ru","destinations":["test55mx2@rosreestr.ru", "test55mx3@rosreestr.ru"]}' https://admin.rosreestr.ru/apimail/create_alias_multy --user admin:'ПАРОЛЬ'
```

#### 1.10	Создание и удаление почтовых доменов

Создание нового домена (c генерацией ключа opendkim)

Исправьте запрос в соответствии с параметрами вашего сервера:
```
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer  <твой_JWT_токен>" -d '{"name":"new-domain.ru"}' https://admin.domain.ru/apimail/create_domain --user admin:PassworD
```
Описание:
- name — имя нового домена (например, new-domain.ru).
- https://admin.domain.ru/apimail/create_domain — адрес вызова API.
- admin:PassworD  - пользователь и пароль созданные в базовой авторизации на веб сервере.

```
#!/bin/bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2VtYWlsIjoic3VwZXJhZG1pbkBOb25lIiwic2NvcGVzIjpbImdlbmVyYXRlX3Rva2VuIiwiYXNzaWduX3Njb3BlIiwicmV2b2tlX3Njb3BlIiwibGlzdF9hZG1pbnMiLCJjcmVhdGVfdXNlciIsImNoYW5nZV9wYXNzd29yZCIsImNoYW5nZV9lbWFpbCIsImRlbGV0ZV91c2VyIiwiY3JlYXRlX2FsaWFzIiwiY3JlYXRlX2FsaWFzX211bHR5IiwiY2hhbmdlX2FsaWFzIiwiZGVsZXRlX2FsaWFzIiwiY3JlYXRlX2RvbWFpbiIsImRlbGV0ZV9kb21haW4iLCJjaGVja19lbWFpbCIsIm1haWxib3hfc2l6ZSIsInNldF9xdW90YSIsImNyZWF0ZV9zaGFyZWRfbWFpbGJveCIsImFkZF9hY2wiLCJjaGFuZ2VfYWNsIiwicmVtb3ZlX2FjbCIsImdldF9hY2wiLCJnZXRfbGltaXRzIiwic2V0X3NlbmRfbGltaXQiLCJkZWxldGVfbGltaXQiLCJtb2RpZnlfbGlzdCIsIm1haWxfcXVldWUiXX0.rNEQ3HglhwQfAirn5dNZHWayNVRpWlHDfgrNeKt5vw0" -d '{"name":"new-domain.ru"}' https://admin.rosreestr.ru/apimail/create_domain  --user admin:'ПАРОЛЬ'
```
Удаление домена (c удалением данных opendkim)
Исправьте запрос в соответствии с параметрами вашего сервера:
```
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer  <твой_JWT_токен>" -d '{"name":"domain.ru"}' https://admin.domain.ru/apimail/delete_domain --user admin:PassworD
```
Описание:
- name — имя домена для удаления.
- https://admin.domain.ru/apimail/delete_domain — адрес вызова API.
- admin:PassworD  - пользователь и пароль созданные в базовой авторизации на веб сервере.

```
#!/bin/bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2VtYWlsIjoic3VwZXJhZG1pbkBOb25lIiwic2NvcGVzIjpbImdlbmVyYXRlX3Rva2VuIiwiYXNzaWduX3Njb3BlIiwicmV2b2tlX3Njb3BlIiwibGlzdF9hZG1pbnMiLCJjcmVhdGVfdXNlciIsImNoYW5nZV9wYXNzd29yZCIsImNoYW5nZV9lbWFpbCIsImRlbGV0ZV91c2VyIiwiY3JlYXRlX2FsaWFzIiwiY3JlYXRlX2FsaWFzX211bHR5IiwiY2hhbmdlX2FsaWFzIiwiZGVsZXRlX2FsaWFzIiwiY3JlYXRlX2RvbWFpbiIsImRlbGV0ZV9kb21haW4iLCJjaGVja19lbWFpbCIsIm1haWxib3hfc2l6ZSIsInNldF9xdW90YSIsImNyZWF0ZV9zaGFyZWRfbWFpbGJveCIsImFkZF9hY2wiLCJjaGFuZ2VfYWNsIiwicmVtb3ZlX2FjbCIsImdldF9hY2wiLCJnZXRfbGltaXRzIiwic2V0X3NlbmRfbGltaXQiLCJkZWxldGVfbGltaXQiLCJtb2RpZnlfbGlzdCIsIm1haWxfcXVldWUiXX0.rNEQ3HglhwQfAirn5dNZHWayNVRpWlHDfgrNeKt5vw0" -d '{"name":"new-domain.ru"}' https://admin.rosreestr.ru/apimail/delete_domain --user admin:'ПАРОЛЬ'
```
#### 1.11	Интеграция со службами каталога с множественным созданием почтовых аккаунтов
1. Выполнить настройку по инструкции
- https://support.r7-office.ru/corporate-server2024/settings_cs-r7disk/rekomendacii-po-sinhronizacii-ldap-s-sluzhboj-katalogov-ald-pro/
2. В поле «Email для создания» указать атрибут из LDAP который указывает на email пользователя или группы.
3. Нажать сохранить и синхронизировать

#### 1.12	Журналирование почтового трафика и журналов событий
1.Ввести в консоли cat /var/log/mail.log
2. Перейти в папку /var/log/dovecot, и открыть нужный лог.

Убедиться что события логируются 

#### 1.13	Просмотр очередей на отправку и прием писем

Просмотр очереди на отправку и прием писем

Исправьте запрос в соответствии с параметрами вашего сервера:
```
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <твой_JWT_токен>" https://admin.domain.ru/apimail/mail_queue --user admin:PassworD
```
Консольная команда:
```
echo "тело письма" | mail -s "Тема" test@example.ru
```
```
 #!/bin/bash
 curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2VtYWlsIjoic3VwZXJhZG1pbkBOb25lIiwic2NvcGVzIjpbImdlbmVyYXRlX3Rva2VuIiwiYXNzaWduX3Njb3BlIiwicmV2b2tlX3Njb3BlIiwibGlzdF9hZG1pbnMiLCJjcmVhdGVfdXNlciIsImNoYW5nZV9wYXNzd29yZCIsImNoYW5nZV9lbWFpbCIsImRlbGV0ZV91c2VyIiwiY3JlYXRlX2FsaWFzIiwiY3JlYXRlX2FsaWFzX211bHR5IiwiY2hhbmdlX2FsaWFzIiwiZGVsZXRlX2FsaWFzIiwiY3JlYXRlX2RvbWFpbiIsImRlbGV0ZV9kb21haW4iLCJjaGVja19lbWFpbCIsIm1haWxib3hfc2l6ZSIsInNldF9xdW90YSIsImNyZWF0ZV9zaGFyZWRfbWFpbGJveCIsImFkZF9hY2wiLCJjaGFuZ2VfYWNsIiwicmVtb3ZlX2FjbCIsImdldF9hY2wiLCJnZXRfbGltaXRzIiwic2V0X3NlbmRfbGltaXQiLCJkZWxldGVfbGltaXQiLCJtb2RpZnlfbGlzdCIsIm1haWxfcXVldWUiXX0.rNEQ3HglhwQfAirn5dNZHWayNVRpWlHDfgrNeKt5vw0" https://admin.rosreestr.ru/apimail/mail_queue --user admin:'ПАРОЛЬ'
```

#### 1.14	Организация списков рассылки это одно и тоже что и в п. 1.9

Создание алиаса с несколькими получателями

Исправьте запрос в соответствии с параметрами вашего сервера:
```
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer  <твой_JWT_токен>" -d '{"source":"news_users@domain.ru","destinations":"user3@domain.ru,user4@domain.ru"}' https://admin.domain.ru/apimail/create_alias_multy --user admin:PassworD
```
Описание:
- source — адрес алиаса (например, news_users@domain.ru).
- destinations — список получателей, разделенных запятой (например, user3@domain.ru,user4@domain.ru).
- https://admin.domain.ru/apimail/create_alias_multy — адрес вызова API.
- admin:PassworD  - пользователь и пароль созданные в базовой авторизации на веб сервере.

```
#!/bin/bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2VtYWlsIjoic3VwZXJhZG1pbkBOb25lIiwic2NvcGVzIjpbImdlbmVyYXRlX3Rva2VuIiwiYXNzaWduX3Njb3BlIiwicmV2b2tlX3Njb3BlIiwibGlzdF9hZG1pbnMiLCJjcmVhdGVfdXNlciIsImNoYW5nZV9wYXNzd29yZCIsImNoYW5nZV9lbWFpbCIsImRlbGV0ZV91c2VyIiwiY3JlYXRlX2FsaWFzIiwiY3JlYXRlX2FsaWFzX211bHR5IiwiY2hhbmdlX2FsaWFzIiwiZGVsZXRlX2FsaWFzIiwiY3JlYXRlX2RvbWFpbiIsImRlbGV0ZV9kb21haW4iLCJjaGVja19lbWFpbCIsIm1haWxib3hfc2l6ZSIsInNldF9xdW90YSIsImNyZWF0ZV9zaGFyZWRfbWFpbGJveCIsImFkZF9hY2wiLCJjaGFuZ2VfYWNsIiwicmVtb3ZlX2FjbCIsImdldF9hY2wiLCJnZXRfbGltaXRzIiwic2V0X3NlbmRfbGltaXQiLCJkZWxldGVfbGltaXQiLCJtb2RpZnlfbGlzdCIsIm1haWxfcXVldWUiXX0.rNEQ3HglhwQfAirn5dNZHWayNVRpWlHDfgrNeKt5vw0" -d '{"source":"test55mx1@rosreestr.ru","destinations":["test55mx2@rosreestr.ru", "test55mx3@rosreestr.ru"]}' https://admin.rosreestr.ru/apimail/create_alias_multy --user admin:'ПАРОЛЬ'
```

#### 1.15	Добавление внешних  адресов в перечень списков рассылки это одно и тоже что и в п. 1.9

Создание алиаса с несколькими получателями

Исправьте запрос в соответствии с параметрами вашего сервера:
```
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer  <твой_JWT_токен>" -d '{"source":"news_users@domain.ru","destinations":"user3@domain.ru,user4@domain.ru"}' https://admin.domain.ru/apimail/create_alias_multy --user admin:PassworD
```
Описание:
- source — адрес алиаса (например, news_users@domain.ru).
- destinations — список получателей, разделенных запятой (например, user3@domain.ru,user4@domain.ru).
- https://admin.domain.ru/apimail/create_alias_multy — адрес вызова API.
- admin:PassworD  - пользователь и пароль созданные в базовой авторизации на веб сервере.
```
#!/bin/bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2VtYWlsIjoic3VwZXJhZG1pbkBOb25lIiwic2NvcGVzIjpbImdlbmVyYXRlX3Rva2VuIiwiYXNzaWduX3Njb3BlIiwicmV2b2tlX3Njb3BlIiwibGlzdF9hZG1pbnMiLCJjcmVhdGVfdXNlciIsImNoYW5nZV9wYXNzd29yZCIsImNoYW5nZV9lbWFpbCIsImRlbGV0ZV91c2VyIiwiY3JlYXRlX2FsaWFzIiwiY3JlYXRlX2FsaWFzX211bHR5IiwiY2hhbmdlX2FsaWFzIiwiZGVsZXRlX2FsaWFzIiwiY3JlYXRlX2RvbWFpbiIsImRlbGV0ZV9kb21haW4iLCJjaGVja19lbWFpbCIsIm1haWxib3hfc2l6ZSIsInNldF9xdW90YSIsImNyZWF0ZV9zaGFyZWRfbWFpbGJveCIsImFkZF9hY2wiLCJjaGFuZ2VfYWNsIiwicmVtb3ZlX2FjbCIsImdldF9hY2wiLCJnZXRfbGltaXRzIiwic2V0X3NlbmRfbGltaXQiLCJkZWxldGVfbGltaXQiLCJtb2RpZnlfbGlzdCIsIm1haWxfcXVldWUiXX0.rNEQ3HglhwQfAirn5dNZHWayNVRpWlHDfgrNeKt5vw0" -d '{"source":"test55mx1@rosreestr.ru","destinations":["test55mx2@rosreestr.ru", "test55mx3@rosreestr.ru"]}' https://admin.rosreestr.ru/apimail/create_alias_multy --user admin:'ПАРОЛЬ'
```
#### 1.16 Управление черным или белым списком

Исправьте запрос в соответствии с параметрами вашего сервера:

Метод добавляет почтовый адрес, домен или ip-адрес в черный или белый список список.

Добавление в черный список
```
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer  <твой_JWT_токен>" -d '{
    "action": "add",
    "entry": "spam@example.ru",
    "list_type": "blacklist"
}' https://admin.domain.ru/apimail/modify_list --user admin:PassworD
```

```
#!/bin/bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2VtYWlsIjoic3VwZXJhZG1pbkBOb25lIiwic2NvcGVzIjpbImdlbmVyYXRlX3Rva2VuIiwiYXNzaWduX3Njb3BlIiwicmV2b2tlX3Njb3BlIiwibGlzdF9hZG1pbnMiLCJjcmVhdGVfdXNlciIsImNoYW5nZV9wYXNzd29yZCIsImNoYW5nZV9lbWFpbCIsImRlbGV0ZV91c2VyIiwiY3JlYXRlX2FsaWFzIiwiY3JlYXRlX2FsaWFzX211bHR5IiwiY2hhbmdlX2FsaWFzIiwiZGVsZXRlX2FsaWFzIiwiY3JlYXRlX2RvbWFpbiIsImRlbGV0ZV9kb21haW4iLCJjaGVja19lbWFpbCIsIm1haWxib3hfc2l6ZSIsInNldF9xdW90YSIsImNyZWF0ZV9zaGFyZWRfbWFpbGJveCIsImFkZF9hY2wiLCJjaGFuZ2VfYWNsIiwicmVtb3ZlX2FjbCIsImdldF9hY2wiLCJnZXRfbGltaXRzIiwic2V0X3NlbmRfbGltaXQiLCJkZWxldGVfbGltaXQiLCJtb2RpZnlfbGlzdCIsIm1haWxfcXVldWUiXX0.rNEQ3HglhwQfAirn5dNZHWayNVRpWlHDfgrNeKt5vw0" -d '{
    "action": "add",
    "entry": "test55mx1@rosreestr.ru",
    "list_type": "blacklist"
}' https://admin.rosreestr.ru/apimail/modify_list --user admin:'ПАРОЛЬ'
```


Удаление из белого списка
```
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer  <твой_JWT_токен>" -d '{
    "action": "remove",
    "entry": "trusted@example.ru",
    "list_type": "whitelist"
}' https://admin.domain.ru/apimail/modify_list --user admin:PassworD
```
Описание:
- action — add (добавить) или remove (удалить).
- entry — email, домен или IP-адрес.
- list_type — blacklist (черный список) или whitelist (белый список).
- Адрес вызова API:https://admin.domain.ru/apimail/modify_list

```
#!/bin/bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2VtYWlsIjoic3VwZXJhZG1pbkBOb25lIiwic2NvcGVzIjpbImdlbmVyYXRlX3Rva2VuIiwiYXNzaWduX3Njb3BlIiwicmV2b2tlX3Njb3BlIiwibGlzdF9hZG1pbnMiLCJjcmVhdGVfdXNlciIsImNoYW5nZV9wYXNzd29yZCIsImNoYW5nZV9lbWFpbCIsImRlbGV0ZV91c2VyIiwiY3JlYXRlX2FsaWFzIiwiY3JlYXRlX2FsaWFzX211bHR5IiwiY2hhbmdlX2FsaWFzIiwiZGVsZXRlX2FsaWFzIiwiY3JlYXRlX2RvbWFpbiIsImRlbGV0ZV9kb21haW4iLCJjaGVja19lbWFpbCIsIm1haWxib3hfc2l6ZSIsInNldF9xdW90YSIsImNyZWF0ZV9zaGFyZWRfbWFpbGJveCIsImFkZF9hY2wiLCJjaGFuZ2VfYWNsIiwicmVtb3ZlX2FjbCIsImdldF9hY2wiLCJnZXRfbGltaXRzIiwic2V0X3NlbmRfbGltaXQiLCJkZWxldGVfbGltaXQiLCJtb2RpZnlfbGlzdCIsIm1haWxfcXVldWUiXX0.rNEQ3HglhwQfAirn5dNZHWayNVRpWlHDfgrNeKt5vw0" -d '{
    "action": "remove",
    "entry": "test55mx1@rosreestr.ru",
    "list_type": "whitelist"
}' https://admin.rosreestr.ru/apimail/modify_list --user admin:'ПАРОЛЬ'
```

#### 1.17	Ограничение пользователям отправлять сообщения за пределы организации

Ограничение пользователям отправлять сообщения за пределы организации:

Исправьте запрос в соответствии с параметрами вашего сервера:
```
Сurl -X POST -H "Content-Type: application/json" -H "Authorization: Bearer <твой_JWT_токен>" -d '{"identifier":"mailbox@domain.ru","id_type":"email","limit": 1,"period":"hour","limit_type":"send"}' https://admin.domain.ru/apimail/set_send_limit --user admin:PassworD
```
Описание:
- identifier — имя почтового ящика, домен.
- id_type – указываем для чего правила ("email", "domain", "group", "server")
- limit – количество писем,
- period – указывается в течение какого периода ("hour", "day", "week")
- limit_type – указываем тип лимита ("send", "rcpt")
- user — пользователь, для которого запрашиваются права.
- admin:PassworD  - пользователь и пароль созданные в пункте 11.2
- Адрес вызова API:https://admin.domain.ru/apimail/set_send_limit

```
#!/bin/bash
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2VtYWlsIjoic3VwZXJhZG1pbkBOb25lIiwic2NvcGVzIjpbImdlbmVyYXRlX3Rva2VuIiwiYXNzaWduX3Njb3BlIiwicmV2b2tlX3Njb3BlIiwibGlzdF9hZG1pbnMiLCJjcmVhdGVfdXNlciIsImNoYW5nZV9wYXNzd29yZCIsImNoYW5nZV9lbWFpbCIsImRlbGV0ZV91c2VyIiwiY3JlYXRlX2FsaWFzIiwiY3JlYXRlX2FsaWFzX211bHR5IiwiY2hhbmdlX2FsaWFzIiwiZGVsZXRlX2FsaWFzIiwiY3JlYXRlX2RvbWFpbiIsImRlbGV0ZV9kb21haW4iLCJjaGVja19lbWFpbCIsIm1haWxib3hfc2l6ZSIsInNldF9xdW90YSIsImNyZWF0ZV9zaGFyZWRfbWFpbGJveCIsImFkZF9hY2wiLCJjaGFuZ2VfYWNsIiwicmVtb3ZlX2FjbCIsImdldF9hY2wiLCJnZXRfbGltaXRzIiwic2V0X3NlbmRfbGltaXQiLCJkZWxldGVfbGltaXQiLCJtb2RpZnlfbGlzdCIsIm1haWxfcXVldWUiXX0.rNEQ3HglhwQfAirn5dNZHWayNVRpWlHDfgrNeKt5vw0" -d '{"identifier":"test55mx3@rosreestr.ru","id_type":"email","limit": 1,"period":"hour","limit_type":"send"}' https://admin.rosreestr.ru/apimail/set_send_limit --user admin:'ПАРОЛЬ'
```



















