# Установовка и настройка
    Переходим в папку с проектом (cd ....)
    sudo apt install python3-pip
    pip3 install --user --upgrade pip
    sudo nano ~/.bashrc # to open the .bashrc file -> export PATH=$PATH:/home/$USER/.local/bin
    sudo apt install python3-venv
    python3 -m venv venv            win: python -m venv venv 
    source venv/bin/activate        win: .\venv\Scripts\activate
    pip install django
    django-admin startproject my_site .
    cd my_site
    python3 manage.py runserver     win: python manage.py runserver
    ctrl+c                          win: ctrl+break
    python3 manage.py startapp <приложение>
    deactivate

	docs.djangoproject.com

# Установить Django и создать проект(startproject). После чего создаем приложение.
Меняем файл settings.py, добавляем в настройки наше приложение
Создаем файл urls.py в нашем приложении и настраиваем urls.py в директории самого проекта
Пишем представления
Создаем файлы шаблонов в папке templates
Связываем url c представлениями
Запускаем наш проект
Тестируем и проверяем на баги

MTV - Models(БД), Templates(Жинжа), Views(сборка)
Управление логикой при ответе -> view; функции/классы
Как будет выглядеть страница -> template;
Состояние приложения -> model.

# Регистрация приложения -> settings.py, INSSTALLED_APPS, append(<приложение>.apps.<приложение>config)
Представление -> views.py, 
from django.http import HttpResponse 
def some(request): return HttpResponse('some text on <приложение>')
Связка с URL -> urls.py, 
from <прииложение>.views import some
urlpatterns.append path('<приложение>/', some) если функций у приложений много:
    from django.urls import path, include
    urlpatterns append -> path(<приложение>/, include('<приложение>.urls'))
<приложение> -> create urls.py
from django.urls import path
from .views import *
urlpatterns = [path('', some, name='home'), ...]

типы: str, int, slug, uuid, path, re-path 

urls.py -> handler404 = pageNotFound                Перехват несуществующих страниц
views.py -> def pageNotFound(request, exception):   500, 400, 403, 404 - будут отрабатывать если DEBUG = False
    return HttpResponseNotFound('not found')

return redirect('home', permanent=True) #301 пост, False=302 врем. перенаправление страницы

views.py -> from django.views import View если будем создавать класс для представлений
в urls.py нужно добавить наш класс в урлы:
urlpatterns = [path('<url>', <наш класс>.as_view(), name='<имя>'),]

# Модели - класс с атрибутами / таблица
<приложение> -> models.py
from django.db import models
class Some(models.Model)
    title = models.CharField(...)   # id == PK автоматически проставлется, не создаем
    ...  см. типы полей
    other = models.ForeignKey(<Class>, on_delete=models.CASCADE) # если удалить связанное поле - эта запись так же удалится
    other = models.ForeignKey(<Class>, on_delete=models.CASCADE, verbose_name='<создаем имя для связанной таблицы>') 
    verbose_name - ... # отображаение поля в админке
# Meta class
    verbose_name = 'имя таблицы в админке'
    verbose_name_plural = 'множественное число'
    ordering = ['поля для сортировки']

# apps.py
    verbose_name = 'альтернативное имя приложения'

# Слагификация
models.py ->
from django.utils.text import slugify # только латиницы
from pytils.translit import slugify # 

class SomeClass(models.Model):
    name = models.CharField(max_length=100)
    slug = models.SlugField(max_length=100, primary_key=True)
    def save(self *args, **kwargs):
        if not self.slug:
            self.slug = slugify(self.name)
        return super().save(*args, **kwargs)
admin.py ->
@admin.register(SomeClass)
class SomeClassAdmin(admin.ModelAdmin):
    readonly_fields = ['slug',]  # атрибут доступный для чтения
    
# Миграция - создание или изменение отношений (контроллер версий)
python manage.py makemigrations     - создание файла миграции,
python manage.py sqlmigrate <xxx>   - просмотр sql запроса
python manage.py migrate            - комит<###>
python3 manage.py migrate <apps> <xxx>  - xxx номер коммита либо zero

# Дамп и Восстановление БД
./manage.py dumpdata > db.json          - вся база в json
./manage.py dumpdata app > app.json     - база app в json
./manage.py dumpdata app.a > app_a.json - таблица (класс) в json
--indent 2                              - флаг, отступы для читабельности
--exclude                               - флаг исключение таблицы из бд
--format                                - флаг расширение yamls, xml, json
./manage.py loaddata db.json            - вся база из json
./manage.py dumpdata --exclude auth.permission --exclude contenttypes > db.json - иначе будут ошибки при импорте файла на другой комп

# CRUD (Create, Read, Update, Delete)
python manage.py shell - выход в терминал
создаем экземпляр класса для добавления строки в таблицу
obj = Some(title='...', ...) obj.save() -> так как эот вариант ленивый
obj = Some.objects.create(title='...', ...) -> активные, можно без создания экземплра
Some.objects.all() - список всех записей
filter, exclude, get - запросы на выборку
__lte, __gte     -<=, >=  Some.objects.filter(len(title)__gte=7)
Some.objects.filter(<поле>__icontains='some')  # ищет some в <поле>
obj = Some.objects.filter(...) obj.title = new_value, ob.save()    - update
obj.delete()    - del
см. queries
<class>.Objects.filter(<отношение>__<class>__<атрибут>__gte=100)  
Phone.objects.select_related('company')     # у телефона один производитель:
Group.objects.prefetch_related('members')   # в группе много студентов
Person.objects.defer("age", "email")        # исключаем поля
Person.objects.only("name")                 # выбираем только
Group.objects.prefetch_related('members').values_list('name', 'members__name')  # получаем QuerySet 

# Шаблоны
views.py -> from django.shortcuts import render
def some_func(request):
    ...
    return render(request, <имя шаблона>, context:dict) - имя шаблона после -> templates/
<приложение>/templates/<приложение> - создаём шаблон html
Базовый.html -> прописываем скелет страницы и создаём блоки:
{% block main %} ... {% endblock %} 
Дочерние.html -> прописываем что добавляем в блок базового шаблона:
{% extends <приложение/Базовый.html> %}
{% block main %> ... <% endblock %}
{{ сюда вставлем ссылку из словаря }}
{% for elem in elements %} {{ elem }} {% endfor %} - цикл по элекменту из словаря
{% if user.name == '' %} ... {% elif user.name == ' ' %} ... {% else %} ... {% endif %} - логическое ветвление
<form> <input name="value"> {% csrf_token %} </form> - используется для защиты

# Подключение стилей:
<приложение>/static -> tyle.css
Базовый.html -> {% load static %} + <link rel="stylesheet" type="text/css" href="{% static 'style.css' %}">
settings.py -> STATIC_URL = '/static/', STATIC_ROOT = os.path.join(BASE_DIR, 'static')

# Фильтры - https://docs.djangoproject.com/en/4.2/ref/templates/builtins/
django.template.defaultfilters
свои теги и фильтры на основе функций и декораторов https://stepik.org/lesson/902550/step/9?unit=907716

# Пагинация -> view.py
from django.core.paginator import Paginator
принимает массив и диапозон выводимого
page_num = int(request.GET.get('page', 1))
get_page(page_num) - вывод диапазона на page_num странице
.html
{% if page.has_previous %} (has_next)
    <a href='&page={{ page.previous_page_number }}'>Назад</a>
{% endif %}

# Postgresql
https://byzoni.org/posts/how-to-install-postgresql-on-ubuntu-22-04/#%d1%88%d0%b0%d0%b3-2-%d1%83%d1%81%d1%82%d0%b0%d0%bd%d0%be%d0%b2%d0%b8%d1%82%d0%b5-postgresql
settings.py ->
DATABASES = {'default': {'ENGINE': django.db.backends.postgresql, 'NAME': <namedb>, 'USER': <user>, 'PASSWORD': 'pass'}}
pip install psycopg2-binary
sudo -u postgres psql
CREATE USER userneto WITH PASSWORD 'passUserNeto';
CREATE USER women_admin WITH PASSWORD 'passWom1adm';
GRANT ALL PRIVILEGES ON DATABASE "netology_classified_ads" to userneto;
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO "userneto";

# Формирование url в шаблонах
<a href='{% url "<имя вью-маршрута>" %}'>

# Динамические ссылки
urls.py ->
urlpatterns = [..., path('post')/<int:post_id/>, <функция>, name='<имя маршрута>']
post_id передается в функцию! 
....html -> 
{% for elem in elements %}
    <a href='{% "<имя маршрута>" elem.pk %}'>1 вариант вручную пишем имя</a>{% endfor %}
models.py ->
    from django.utils import reverse
class ...:
    def get_absolute_url(self):      # вернем словарь со значением первичного ключа к kwargs
        return reverse('<имя маршрута in url>', kwargs={'<имя парамерметра>': self.pk})
    <a href='{{ elem.get_absolute_url }}'>2 вариант через метод класса</a>{% endfor %}

# Django Debug Toolbar
pip install django-debug-toolbar
settings.py -> 
INSTALLED_APPS = [..., 'django.contrib.staticfiles',   ...,'debug_toolbar',]
STATIC_URL = '/static/'
APP_DIRS = True
MIDDLEWARE = ['debug_toolbar.middleware.DebugToolbarMiddleware',  ...]
INTERNAL_IPS = ['127.0.0.1',]
urls.py -> 
from django.conf import settings        from django.urls import include, path       import debug_toolbar
urlpatterns = [..., path('__debug__/', include(debug_toolbar.urls)),] 

# Many-to-many
class Order(models.Model):
    products = models.ManyToManyField(<класс связи>, related_name='<имя промежуточной таб-ы>', through='OrderPosition')

# admin.py ->
python3 manage.py createsuperuser               # создаем админа  
from .models import *
class <m2mclassInline>(admin.TabularInline):
    model = <m2mclass>  extra = 3 filelds = ['prod', 'quan']
@admin.register(<class>) # <==> admin.site.register(<class>, <classAdmin>)
class <classAdmin>(admin.ModelAdmin):   # чтобы видетю модель в админке - её нужно регистрировать
    list_display = ['<какие поля отображать>']
    list_display_links = ['<поля доступные для перехода>']
    search_fields = ['<поиск по полям>']
    list_filter = ['<фильтр по полям>']
    inlines = [<m2mclassInline>]
    list_editable = ['поля редактируемые из админки']

# Q class запроос ИЛИ
from django.db.models import Q
<class>.objects.filter(id__lt=5, amount__gte=10000) # запрс И
<class>.objects.filter(Q(id__lt=5) | Q(amount__gte=10000)) # запрс ИЛИ
             ~ NOT, & - И

# F class
позволяет работать с текущим значением поля при работе с запросами наприер для генерации нового столбца
<class>.objects.all().annotate(<имя нового стобца>=F(<'поле класса'> <логика вычисления>))

#.aggregate & values
агрегирущая функция -> Count, Avg, Min, Max, some_func
values - те столбцы что нам интересны

# .gitignore -> gitignore.io
venv/ env/ .env __pycache__/ *.pyc *.sqlite3 .idea/ .vscode/

# forms.py ->
from django import forms
forms.Form прописываем в ручную поля модели
forms.ModelForm прописываем мета класс в которой указываем model = ... & fields = ['<поля отображений>']

# User
models.py -> 
from django.contrib.auth.models import AbstractUser
from django.db import models
class User(AbstractUser):
    Full_name = models.CharField(max_length=80) ...
    def create_user(self, username, name, email, password):
        self.username = username self.Full_name = name self.email = email self.set_password(password) self.save()
settings.py ->
AUTH_USER_MODEL = "<apps>.User"
view.py -> # auth
from django.contrib.auth import authenticate, login, logout
from django.shortcuts import render, HttpResponse
    def login_page(request):
    if data := request.POST:
        if user := authenticate(request, username=data['username'], password=data['password']):
            login(request, user)
    def logout_page(request): logout(request) return HttpResponse("<h3>Вы успешно вышли из системы</h3>")

# users
python3 manage.py startapp users





### DRF
rest clinet -> request.http
GET http://localhost:8000 - > send request ### 
POST http://localhost:8000 - > send request ### 
pip install djangorestframework  # DRF -> INSSTALLED_APPS [rest_framework]
from rest_framework import generics
.CreateAPIView          # создание POST
.ListAPIView            # возврат списка GET
.RetrieveAPIView        # возврат записи GET
.DestroyAPIView         # удаление записи DELETE
.UpdateAPIView          # изменение записи PUT + PATCH
.ListCreateAPIView      # чтение и возврат списков GET POST
.RetrieveUpdateAPIView  # чтение и изменение записи GET PUT + PATCH
.RetrieveDestroyAPIView # чтение и удаление записи GET DELETE
.RetrieveUpdateDestroyAPIView   # чтение удаление и изменение записи  GET, PUT, PATCH, DELETE

#ViewSet https://www.django-rest-framework.org/api-guide/viewsets/
работаем с одной моделью, сериализатором и хотим разные представления под запросы? исползуем сэты
urls.py -> в вью передаем словарь с методами, которые хотим реализовать, все кроме list/create 
ожидают pk
{'get': 'list'| 'retrieve', 'post': 'create', 'put': 'update',
 , 'delete': 'destroy',}
если по одному адресу запрашиваем разные методы можем воспользоватьься роутером для объединения
создать роутер, зарегистрируем имя и вью, в урл передаем include(router.urls)
с помощью @action(methods=['...'], detail=True/False)
можем прописать новый маршрут из роутера
добавляем во вью новый метод, декорируем, делаем запрос и возвращаем респонс

# Permissions
https://www.django-rest-framework.org/api-guide/permissions/
AllowAny # Полный доступ
IsAuthenticated # авторизованный пользователь
IsAdminUser # администатор
IsAuthenticatedOrReadOnly # авторизованный пользователь/только чтение

# Авторизация и Аутентефикация
https://www.django-rest-framework.org/api-guide/authentication/
Session-based authentication # на основе кукис и сессии
Token-based authentication # на основе токенов // Djoser
JSON WEB Token  // Simple JWT 
Django REST framework OAth # через соцсети

# Пагинация
https://www.django-rest-framework.org/api-guide/pagination/
setting.py ->
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.LimitOffsetPagination',  # offset - начиная с 
    # 'rest_framework.pagination.PageNumberPagination' # обычная постраничная
    'PAGE_SIZE': 100
}
для каждого отображения можем подключать собственную пагинацию
наследуясь от PageNumberPagination

# Валидация
serializers.py ->
from rest_framework.exceptions  import ValidationError
def validate(self, data): логика валидации raise ValidationError / retrun data 
create/update - так же перегружаются при необходимости

# Фильтрация
pip install django-filter
settings.py -> INSSTALLED_APPS -> 'django_filters' подключаем
добавляем в словарь REST_FRAMEWORK ->
'DEFAULT_FILTER_BACKENDS': ['django_filters.rest_framework.DjangoFilterBackend',
'django_filters.rest_framework.filters.SearchFilter', 'django_filters.rest_framework.filters.OrderingFilter']
views.py ->     либо в настройках либо в модулях прописываем дефолтные фильтры
filter_backends = [django_filters.rest_framework.DjangoFilterBackend, filters.SearchFilter, filters.OrderingFilter]
filterset_fields = ['category', 'in_stock']
search_fields = ['username', 'email']   # settings.py -> REST_FRAMEWORK -> 'SEARCH_PARAM': 'q'
ordering_fields = ['username', 'email'] # settings.py -> REST_FRAMEWORK -> 'ORDERING_PARAM': 'o'