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