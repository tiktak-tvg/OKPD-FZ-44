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

Создание почтового ящика через CURL запрос:
Исправьте запрос в соответствии с параметрами вашего сервера:

curl -X POST -H "Content-Type: application/json" -H "jwt-auth: Bearer <твой_JWT_токен>" -d '{"email":"test@domain.ru","password":"mail_password"}' https://admin.domain.ru/apimail/create_user --user admin:PassworD 

Описание:
email — почтовый ящик для создания пользователя.
password — пароль для пользователя почтового ящика.
https://admin.domain.ru/apimail/create_user — адрес вызова API.
admin:PassworD  - пользователь и пароль созданные в базовой авторизации на веб сервере.

