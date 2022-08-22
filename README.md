# API для YaMDb

## Getting Started:
Клонировать репозиторий и перейти в него в командной строке:
Установить и активировать виртуальное окружение:
```
python3 -m venv env
source env/bin/activate
python3 -m pip install --upgrade pip
```
Установить зависимости из файла requirements.txt
```
python -m pip install --upgrade pip
pip install -r requirements.txt
```
Выполнить миграции:
```
python manage.py migrate
```
В папке с файлом manage.py выполнить команду:
```
python manage.py runserver
```
Для загрузки тестовых данных из csv-файлов выполнить команду:
```
python manage.py load_data
```

Документация доступна после запуска по адресу:
http://127.0.0.1/redoc/

Проект собирает отзывы пользователей на произведения. 
Произведения делятся на категории. 
Список категорий может быть расширен администратором.

Сами произведения в YaMDb не хранятся, здесь нельзя посмотреть фильм или 
послушать музыку.

В каждой категории есть произведения: книги, фильмы или музыка. 

Произведению может быть присвоен жанр из списка предустановленных. Новые жанры может создавать только 
администратор.

Пользователи оставляют к произведениям текстовые 
отзывы и ставят произведению оценку в диапазоне от одного до десяти 
(целое число); из пользовательских оценок формируется усреднённая оценка 
произведения — рейтинг. На одно произведение пользователь может 
оставить только один отзыв.

### Алгоритм регистрации пользователей
1. Пользователь отправляет POST-запрос на добавление нового пользователя с 
параметрами email и username на эндпоинт /api/v1/auth/signup/.
2. YaMDB отправляет письмо с кодом подтверждения (confirmation_code) на адрес email.
3. Пользователь отправляет POST-запрос с параметрами username и 
confirmation_code на эндпоинт /api/v1/auth/token/, в ответе на запрос ему 
приходит token (JWT-токен). В результате пользователь получает токен и может 
работать с API проекта, отправляя этот токен с каждым запросом.
4. Для повторного получения письма с кодом подтверждения пользователь отправляет
POST-запрос с параметрами username и email на эндпоинт /api/v1/auth/remind/.
5. При желании пользователь отправляет PATCH-запрос на эндпоинт 
/api/v1/users/me/ и заполняет поля в своём профайле.
6. Если пользователя создаёт администратор, например, через POST-запрос на 
эндпоинт api/v1/users/ — письмо с кодом также приходит к нему на почту.

### Пользовательские роли
- **Аноним** — может просматривать описания произведений, читать отзывы и комментарии.
- **Аутентифицированный пользователь (user)** — может, как и **Аноним**, читать всё, 
дополнительно он может публиковать отзывы и ставить оценку произведениям 
(фильмам/книгам/песенкам), может комментировать чужие отзывы; может редактировать 
и удалять свои отзывы и комментарии. Эта роль присваивается по умолчанию 
каждому новому пользователю.
- **Модератор (moderator)** — те же права, что и у **Аутентифицированного** 
пользователя плюс право удалять любые отзывы и комментарии.
- **Администратор (admin)** — полные права на управление всем контентом проекта. 
Может создавать и удалять произведения, категории и жанры. Может назначать 
роли пользователям.
- **Суперпользователь Django** — обладает правами администратора (admin). Даже если 
изменить пользовательскую роль суперпользователя — это не лишит его прав администратора. 
Суперпользователь — всегда администратор, но администратор — не обязательно суперпользователь.

### Ресурсы проекта API YaMDb
- Ресурс **auth**: аутентификация.
- Ресурс **users**: отзывы на произведения. Отзыв привязан к определённому произведению.
- Ресурс **comments**: комментарии к отзывам. Комментарий привязан к определённому отзыву.
