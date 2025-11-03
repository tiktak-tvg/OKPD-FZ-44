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
curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2VtYWlsIjoic3VwZXJhZG1pbkBOb25lIiwic2NvcGVzIjpbImdlbmVyYXRlX3Rva2VuIiwiYXNzaWduX3Njb3BlIiwicmV2b2tlX3Njb3BlIiwibGlzdF9hZG1pbnMiLCJjcmVhdGVfdXNlciIsImNoYW5nZV9wYXNzd29yZCIsImNoYW5nZV9lbWFpbCIsImRlbGV0ZV91c2VyIiwiY3JlYXRlX2FsaWFzIiwiY3JlYXRlX2FsaWFzX211bHR5IiwiY2hhbmdlX2FsaWFzIiwiZGVsZXRlX2FsaWFzIiwiY3JlYXRlX2RvbWFpbiIsImRlbGV0ZV9kb21haW4iLCJjaGVja19lbWFpbCIsIm1haWxib3hfc2l6ZSIsInNldF9xdW90YSIsImNyZWF0ZV9zaGFyZWRfbWFpbGJveCIsImFkZF9hY2wiLCJjaGFuZ2VfYWNsIiwicmVtb3ZlX2FjbCIsImdldF9hY2wiLCJnZXRfbGltaXRzIiwic2V0X3NlbmRfbGltaXQiLCJkZWxldGVfbGltaXQiLCJtb2RpZnlfbGlzdCIsIm1haWxfcXVldWUiXX0.rNEQ3HglhwQfAirn5dNZHWayNVRpWlHDfgrNeKt5vw0" -d '{"email":"komov@rosreestr.ru","password":"Qwerty5+"}' https://admin.rosreestr.ru/apimail/create_user --user admin:ПАРОЛЬ
```

<img width="2243" height="98" alt="image" src="https://github.com/user-attachments/assets/739c8f69-bc62-4eec-a8dc-dde05925c8ec" />

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

В исправленном запросе удаляем пользователя `КОМОВ А.Н.` 

P.s. незабудьте поменять пароль на admin:
```

```
