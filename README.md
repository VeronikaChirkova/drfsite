1. Создание и активация виртуального окружения:<br>
```bash
python -m venv env
. ./env/bin/activate
```
2. Установить Django и Django DRF:<br>
```bash
pip install Django
pip install djangorestframework
```
3. Создать проект и приложение:<br>
```bash
django-admin startproject drfsite

cd drfsite/
django-admin startapp women
```
4. Сделать миграции<br>
```bash
python manage.py makemigrations
python manage.py migrate
```
5. В INSTALLED_APPS активировать приложение 'women.apps.WomenConfig', 'rest_framework'.<br>
6. В women/models.py прописать модели.<br>
7. Сделать миграции<br>
```bash
python manage.py makemigrations
python manage.py migrate
```
8. Создать суперпользователя:<br>
```bash
python manage.py createsuperuser
```
9. В admin.py прописать приложение Women.<br>
10. В women/views.py прописать представление WomenAPIView.<br>
11. Создать сериализатор women/serializers.py<br>
12. Добавить URL-шаблон в drfsite/urls.py `path('api/v1/womenlist/', WomenAPIView.as_view())`<br>

## Класс Serializer
1. В women/serializer определели сериализатор для модели Women. С помощью сериализатора можем преобразовывать объекты модели в словарь, а затем словарь в JSON строку (JSONRenderer()).<br>

2.В women/views.py при GET-запросе: вызывается JSONRenderer() и преобразовывает данные в байтовую JSON строку, клиент принимает  байтовую JSON строку.<br>

3. В women/views.py при POSt-запросе (принимаем данные): с помощью WomenSerializer() распаковываем данные -> успешная проверка -> добавляем запись в БД и возвращаем то, что было добавлено.<br>

## Методы save(), create() и update() класса Serializer
1. В women/views.py - обработка запросов, прописать методы POST, GET, CREATE, UPDATE, DELETE.<br>
2. Весь функционал по обработке данных в сериализаторе - обработка данных.