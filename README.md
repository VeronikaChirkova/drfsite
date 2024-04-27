### Проект по курсу Django REST Framework Сергея Балакирева

1. Создание и активация виртуального окружения:<br>
```bash
python3-m venv env
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
python manage.py startapp women
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

## Авторизацию по JWT-токенам
1. Установить библиотеку Simple JWT:<br>
```bash
pip install djangorestframework-simplejwt
```
2. В settings.py/REST_FRAMEWORK добавить:<br>
```text
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_simplejwt.authentication.JWTAuthentication',
        )
```
3. Добавить маршруты в drfsite/urls.py:<br>
```text
    path('api/v1/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('api/v1/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
    path('api/v1/token/refresh/', TokenVerifyView.as_view(), name='token_verify'),
```
4. Добавить настройки в settings.py:<br>
```text
   SIMPLE_JWT = {
    "ACCESS_TOKEN_LIFETIME": timedelta(minutes=5),
    "REFRESH_TOKEN_LIFETIME": timedelta(days=1),
    "ROTATE_REFRESH_TOKENS": False,
    "BLACKLIST_AFTER_ROTATION": False,
    "UPDATE_LAST_LOGIN": False,

    "ALGORITHM": "HS256",
    "SIGNING_KEY": SECRET_KEY,
    "VERIFYING_KEY": "",
    "AUDIENCE": None,
    "ISSUER": None,
    "JSON_ENCODER": None,
    "JWK_URL": None,
    "LEEWAY": 0,

    "AUTH_HEADER_TYPES": ("Bearer",),
    "AUTH_HEADER_NAME": "HTTP_AUTHORIZATION",
    "USER_ID_FIELD": "id",
    "USER_ID_CLAIM": "user_id",
    "USER_AUTHENTICATION_RULE": "rest_framework_simplejwt.authentication.default_user_authentication_rule",

    "AUTH_TOKEN_CLASSES": ("rest_framework_simplejwt.tokens.AccessToken",),
    "TOKEN_TYPE_CLAIM": "token_type",
    "TOKEN_USER_CLASS": "rest_framework_simplejwt.models.TokenUser",

    "JTI_CLAIM": "jti",

    "SLIDING_TOKEN_REFRESH_EXP_CLAIM": "refresh_exp",
    "SLIDING_TOKEN_LIFETIME": timedelta(minutes=5),
    "SLIDING_TOKEN_REFRESH_LIFETIME": timedelta(days=1),

    "TOKEN_OBTAIN_SERIALIZER": "rest_framework_simplejwt.serializers.TokenObtainPairSerializer",
    "TOKEN_REFRESH_SERIALIZER": "rest_framework_simplejwt.serializers.TokenRefreshSerializer",
    "TOKEN_VERIFY_SERIALIZER": "rest_framework_simplejwt.serializers.TokenVerifySerializer",
    "TOKEN_BLACKLIST_SERIALIZER": "rest_framework_simplejwt.serializers.TokenBlacklistSerializer",
    "SLIDING_TOKEN_OBTAIN_SERIALIZER": "rest_framework_simplejwt.serializers.TokenObtainSlidingSerializer",
    "SLIDING_TOKEN_REFRESH_SERIALIZER": "rest_framework_simplejwt.serializers.TokenRefreshSlidingSerializer",
}
```
## Пагинация (pagination)
1. Автоматическая пагинация к списку данных для всего проекта в settings.py добавить:<br>
```text
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.LimitOffsetPagination',
    'PAGE_SIZE': 2,
}
```
2. Определенная пагинация для некоторых запросов.<br> Создать свой класс пагинации в women/views.py `class WomenListPagination` и подключить его к виду  `pagination_class = WomenListPagination`.<br>

## drf-yasg Swagger
1. Установить:<br>
```bash
pip install -U drf-yasg
```
2.В settings.py:<br>
```text
INSTALLED_APPS = [
   ...
   'drf_yasg',
   ...
]
```
3.В urls.py:<br>
```text
...
from django.urls import re_path
from rest_framework import permissions
from drf_yasg.views import get_schema_view
from drf_yasg import openapi

...

schema_view = get_schema_view(
   openapi.Info(
      title="Snippets API",
      default_version='v1',
      description="Test description",
      terms_of_service="https://www.google.com/policies/terms/",
      contact=openapi.Contact(email="contact@snippets.local"),
      license=openapi.License(name="BSD License"),
   ),
   public=True,
   permission_classes=(permissions.AllowAny,),
)

urlpatterns = [
   path('swagger<format>/', schema_view.without_ui(cache_timeout=0), name='schema-json'),
   path('swagger/', schema_view.with_ui('swagger', cache_timeout=0), name='schema-swagger-ui'),
   path('redoc/', schema_view.with_ui('redoc', cache_timeout=0), name='schema-redoc'),
   ...
]

```