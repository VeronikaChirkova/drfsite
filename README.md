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

## Ограничения доступа (permissions)
1. В women/models.py в class Women добавить поле user.<br>
2. Сделать миграции:<br>
```bash
python manage.py makemigrations # 1
python manage.py migrate
```
3. В women/views.py вместо class WomenViewSet задать три класса обычных представлений WomenAPIList - возвращает список статей, WomenAPIUpdate - изменяет записи, WomenAPIDestroy - удаляет записи.<br>
4. Добавить URL-шаблон в drfsite/urls.py:<br>
```text
    path('api/v1/women/', WomenAPIList.as_view()),
    path('api/v1/women/<int:pk>/', WomenAPIUpdate.as_view()),
    path('api/v1/womendelete/<int:pk>/', WomenAPIDestroy.as_view()),
```
5. Добавление записи только для авторизованных пользователей в women/views.py/WomenAPIList добавить: `permission_classes = (IsAuthenticatedOrReadOnly,)`<br>
6. Автоматическая связь пользователя с добаленной записью в women/serializers.py длбавить `user = serializers.HiddenField(default=serializers.CurrentUserDefault())`<br>
7. Просматривать записи для каждого пользователя, а удалять только админ - создать класс в women/permissions.py: `class IsAdminReadOnly`.<br> В women/views.py/WomenAPIDestroy `permission_classes = (IsAdminReadOnly, )`<br>
8. Для изменения записи только автору, а просматривать всем создать class IsOwnerOrReadOnly вwomen/permissions.py -> women/views.py/WomenAPIUpdate `permission_classes = (IsOwnerOrReadOnly,)`<br>

## Аутентификация по токенам. Пакет Djoser
1. Установить библиотеку Djoser:<br>
```bash
pip install Djoser
```
2. В INSTALLED_APPS добавить: `"rest_framework.authtoken",`, `"djoser",`<br>
3. Сделать миграции<br>
```bash
python manage.py migrate
```
4. Добавить маршруты в drfsite/urls.py:<br>
```text
    path('api/v1/auth/', include('djoser.urls')),
    re_path(r'^auth/', include("djoser.urls.authtoken")),
```
5. Разрешить аутентификацию по токену settings.py/REST_FRAMEWORK:<br>
```text
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework.authentication.TokenAuthentication',
        'rest_framework.authentication.BasicAuthentication',
        'rest_framework.authentication.SessionAuthentication',
        )
```
