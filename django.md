## ссылки
- [всё о Джанго](https://docs.djangoproject.com)
- [всё о Постгре](https://www.postgresql.org/)
- [статься о postgreSQL](https://byzoni.org/posts/how-to-install-postgresql-on-ubuntu-22-04/)


- [Авторизация и регистрация](#auth_reg)
- [Глоссарий](#gloss)

#### Установовка и настройка
```bash
Переходим в папку с проектом (cd ...)
sudo apt install postgresql postgresql-client   # установка postgresql
sudo systemctl enable postgresql.service && sudo systemctl start postgresql.service # запуск postgre
sudo apt install python3-pip        # установка пакета pip в убунту
pip3 install --user --upgrade pip   # установка пакета pip в убунту
sudo nano ~/.bashrc                 # to open the .bashrc file -> export PATH=$PATH:/home/$USER/.local/bin
sudo apt install python3-venv       # установка виртуального окружения
python3 -m venv venv                # создание виртуального окружения win: python -m venv venv
source venv/bin/activate            # активация виртуального окружения win: .\venv\Scripts\activate
pip install django                  # установка django
django-admin startproject my_site . # создание проекта
cd my_site                          # переход в папку проекта
python3 manage.py runserver         # запуск проекта win: python manage.py runserver
ctrl+c                              # отмена запуска win: ctrl+break
python3 manage.py startapp <пр-ие>  # создание приложения
deactivate                          # деактивация виртуального окружения
```

#### Postgresql
```bash
DATABASES = {'default': {'ENGINE': django.db.backends.postgresql, 'NAME': <namedb>, 'USER': <user>, 'PASSWORD': 'pass'}}                            # настройки settings.py
pip install psycopg2-binary         # установка для работы с postgreSQL
sudo -u postgres psql               # вход в postgreSQL
# создание юзера и передача ему привелегий для работы с базой и таблицами
CREATE USER women_admin WITH PASSWORD 'passWom1adm';
GRANT ALL PRIVILEGES ON DATABASE "netology_classified_ads" to userneto;
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO "userneto";
```

#### Миграция - создание или изменение отношений (контроллер версий)
```bash
python manage.py makemigrations     # создание файла миграции,
python manage.py sqlmigrate <xxx>   # просмотр sql запроса
python manage.py migrate            # комит<###>
python3 manage.py migrate <apps> <xxx>  # xxx номер коммита либо zero
```

#### Дамп и Восстановление БД
```bash
./manage.py dumpdata > db.json          # вся база в json
./manage.py dumpdata app > app.json     # база app в json
./manage.py dumpdata app.a > app_a.json # таблица (класс) в json
--indent 2                              # флаг, отступы для читабельности
--exclude                               # флаг исключение таблицы из бд
--format                                # флаг расширение yamls, xml, json
./manage.py loaddata db.json            # вся база из json
# пример дампа базы, иначе будут ошибки при импорте файла на другой комп
./manage.py dumpdata --exclude auth.permission --exclude contenttypes > db.json
```

#### Установить Django и создать проект(startproject). После чего создаем приложение.
```
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
```

#### Регистрация приложения -> settings.py, INSSTALLED_APPS, append(<приложение>.apps.<приложение>config)
```python
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
```

#### Модели - класс с атрибутами / таблица
```python
<приложение> -> models.py
from django.db import models
class Some(models.Model)
    title = models.CharField(...)   # id == PK автоматически проставлется, не создаем
    ...  см. типы полей
    other = models.ForeignKey(<Class>, on_delete=models.CASCADE) # если удалить связанное поле - эта запись так же удалится
    other = models.ForeignKey(<Class>, on_delete=models.CASCADE, verbose_name='<создаем имя для связанной таблицы>')
    verbose_name - ... # отображаение поля в админке
```

#### Meta class
```python
    verbose_name = 'имя таблицы в админке'
    verbose_name_plural = 'множественное число'
    ordering = ['поля для сортировки']
```

#### apps.py
```python
    verbose_name = 'альтернативное имя приложения'
```

#### Слагификация
```python
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
```

#### CRUD (Create, Read, Update, Delete)
```bash
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
```

#### Шаблоны
```html
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
```

#### Подключение стилей:
```python
<приложение>/static -> tyle.css
Базовый.html -> {% load static %} + <link rel="stylesheet" type="text/css" href="{% static 'style.css' %}">
settings.py -> STATIC_URL = '/static/', STATIC_ROOT = os.path.join(BASE_DIR, 'static')
```

#### [Фильтры](https://docs.djangoproject.com/en/4.2/ref/templates/builtins/)
```
django.template.defaultfilters
[свои теги и фильтры на основе функций и декораторов](https://stepik.org/lesson/902550/step/9?unit=907716)
```

#### Пагинация -> view.py
```python
from django.core.paginator import Paginator
# принимает массив и диапозон выводимого
page_num = int(request.GET.get('page', 1))
get_page(page_num)              # вывод диапазона на page_num странице
.html
{% if page.has_previous %} (has_next)
    <a href='&page={{ page.previous_page_number }}'>Назад</a>
{% endif %}
```

#### Формирование url в шаблонах
```python
<a href='{% url "<имя вью-маршрута>" %}'>
# если в модели прописан
    def get_absolute_url(self):
        return reverse('tag', kwargs={'t_slug': self.slug})
<a class='menu-link' href='{{ tag.get_absolute_url }}'>{{ tag.title|capfirst }}</a>
```

#### Динамические ссылки
```python
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
```

#### Django Debug Toolbar
```python
# pip install django-debug-toolbar
settings.py ->
INSTALLED_APPS = [..., 'django.contrib.staticfiles',   ...,'debug_toolbar',]
STATIC_URL = '/static/'
APP_DIRS = True
MIDDLEWARE = ['debug_toolbar.middleware.DebugToolbarMiddleware',  ...]
INTERNAL_IPS = ['127.0.0.1',]
urls.py ->
from django.conf import settings        from django.urls import include, path       import debug_toolbar
urlpatterns = [..., path('__debug__/', include(debug_toolbar.urls)),]
```

#### Many-to-many
```python
class Order(models.Model):
    products = models.ManyToManyField(<класс связи>, related_name='<имя промежуточной таб-ы>', through='OrderPosition')
```

#### admin.py ->
```python
python3 manage.py createsuperuser               # создаем админа
from .models import *
class <m2mclassInline>(admin.TabularInline):
    model = <m2mclass>  extra = 3 filelds = ['prod', 'quan']
@admin.register(<class>)                        # <==> admin.site.register(<class>, <classAdmin>)
class <classAdmin>(admin.ModelAdmin):           # чтобы видетю модель в админке - её нужно регистрировать
    list_display = ['<какие поля отображать>']
    list_display_links = ['<поля доступные для перехода>']
    search_fields = ['<поиск по полям>']
    list_filter = ['<фильтр по полям>']
    inlines = [<m2mclassInline>]
    list_editable = ['поля редактируемые из админки']
```

#### Q class запроос ИЛИ
```python
from django.db.models import Q
<class>.objects.filter(id__lt=5, amount__gte=10000) # запрс И
<class>.objects.filter(Q(id__lt=5) | Q(amount__gte=10000)) # запрс ИЛИ
             ~ NOT, & - И
```

#### F class
```
позволяет работать с текущим значением поля при работе с запросами наприер для генерации нового столбца
<class>.objects.all().annotate(<имя нового стобца>=F(<'поле класса'> <логика вычисления>))
```

#### .aggregate & values
```
агрегирущая функция -> Count, Avg, Min, Max, some_func
values - те столбцы что нам интересны
```

#### .gitignore -> gitignore.io
```
venv/ env/ .env __pycache__/ *.pyc *.sqlite3 .idea/ .vscode/
```

#### forms.py ->
```
from django import forms
forms.Form прописываем в ручную поля модели
forms.ModelForm прописываем мета класс в которой указываем model = ... & fields = ['<поля отображений>']
```

#### User
```python
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
```

##### <a name='auth_reg'><h3>Авторизация и регистрация</h3></a>

- [Введение в авторизацию пользователей](#auth_reg1)

- [Авторизация пользователей. Функции authenticate() и login()](#auth_reg2)

- [Шаблонные контекстные процессоры](#auth_reg3)

- [Классы LoginView, LogoutView и AuthenticationForm](#auth_reg4)

- [Декоратор login_required и класс LoginRequiredMixin](#auth_reg5)

- [Регистрация пользователей через функции представления](#auth_reg6)

- [Класс UserCreationForm](#auth_reg7)

- [Авторизация через email. Профиль пользователя](#auth_reg8)

- [Классы PasswordChangeView и PasswordChangeDoneView](#auth_reg9)

<a name='auth_reg1'><h4>Введение в авторизацию пользователей</h4></a>

- Authentication (аутентификация) - проверка подлинности (наличия) пользователя по введенным данным (обычно, это логин/пароль).
- Authorization (авторизация) – разрешение на доступ (непосредственно вход) к закрытой части сайта.
- данные в БД в таблице auth_user, обычно модуль объявляют отдельнам приложением. users
- регистрируем приложение в settings.py ->
```python
INSTALLED_APPS.append(users.apps.UsersConfig)
```
- добавляем маршруты womens/urls.py ->
```python
path('users/', include('users.urls', namespace="users")), # добавляем пространство имен, это важно!
```
- добавляем маршруты users/urls.py ->
```python
app_name = 'users' # это важно, в шаблонах можно будет писать users: login
urlpatterns = [
    path('login/', views.login_user, name='login'),
    path('logout/', views.logout_user, name='logout'),
]
```

<a name='auth_reg2'><h4>Авторизация пользователей. Функции authenticate() и login()</h4></a>

- создаем форму для авторизации forms.py
```python
class LoginUserForm(forms.Form):
    username = forms.CharField(label='Логин', widget=forms.TextInput(attrs={'class': 'form-input'}))
    password = forms.CharField(label='Пароль', widget=forms.PasswordInput(attrs={'class': 'form-input'}))
```
- создаём шаблон авторизации login.html ->
```html
{% extends 'base.html' %}
{% block content %}
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <p><button type="submit">Войти</button></p>
</form>
{% endblock %}
```
- создаём обработчик во wviews.py ->
```python
def login_user(request):
    if request.method == 'POST': # если пост запрос
        form = LoginUserForm(request.POST) # создаем форму на основе наших данных и модели формы
        if form.is_valid(): # если данные верны и прошли проверку
            cd = form.cleaned_data # очищаем данные
            user = authenticate(request, username=cd['username'], password=cd['password']) # проходи аутентификацию
            if user and user.is_active: # если аутентефикация пройдена и пользователь существет
                login(request, user) # логинимся
                return HttpResponseRedirect(reverse('home')) # переходим на стартовую страницу
    form = LoginUserForm()
    return render(request, 'users/login.html', {'form': form})

def logout_user(request):
    logout(request)
    return HttpResponseRedirect(reverse('users:login'))
```

<a name='auth_reg3'><h4>Шаблонные контекстные процессоры</h4></a>

- создаём вспомогательный файл cont_pocessors.py ->
```python
def get_mainmenu(request):
    return {'mainmenu': ()} # возвращает словарь для главного меню
```
- добавляем контекстный процессор в settings.py TEMPLATES->
```python
context_processors.append('main.cont_pocessors.get_mainmenu') # приложение.файл.имя
```
- дополняем базовый шаблон templates/base.html ->
```html
{% for elem in mainmenu %} <!-- mainmenu берем из контекстного менеджера на прямую -->
    <li class='menu-list-item'>
        <a class='menu-link' href='{% url elem.link %}'>{{ elem.name }}</a>
    </li>
{% endfor %}
{% if user.is_authenticated %} <!-- проверяем авторизацию -->
    <li class='menu-list-item'>{{ user.username }} |
    <a class='menu-link' href='{% url 'users:logout' %}'>Выйти</a></li>
{% else %}
<li class='menu-list-item'>
    <a class='menu-link' href='{% url 'users:login' %}'>Войти</a> |
    <a class='menu-link' href='{% url 'users:register' %}'>Регистрация</a>
</li>
{% endif %}
```

<a name='auth_reg4'><h4>Классы LoginView, LogoutView и AuthenticationForm</h4></a>

```python
from django.contrib.auth.views import LoginView, LogoutView
from django.contrib.auth.forms import AuthenticationForm
# базовые классы для авторизации и выхода пользователя
```
- создаём форму для авторизации пользователя forms.py ->
```python
class LoginForm(AuthenticationForm):
    """ Наследуемся от формы обработки аутентификации пользователя.
    перегружаем базовые атрибуты, присваиваем им имена, доп настройки """
    username = forms.CharField(label='Логин', widget=forms.TextInput(attrs={'class': 'form-input'}))
    password = forms.CharField(label='Пароль', widget=forms.PasswordInput(attrs={'class': 'form-input'}))
    class Meta:
        model = get_user_model() # возврат текущей модели пользователя
        fields = ['username', 'password'] # отображаемые поля
```
- создаём шаблон авторизации login.html ->
```html
{% extends 'base.html' %} <!-- какой шаблон расширяем -->
{% block content %} <!-- именованый блок -->
<div class='post'><div class='post-content'>
    <form method='POST'> <!-- форма регистрации -->
        {% csrf_token %} <!-- скрытый атрибут для проверки -->
        <input type="hidden" name="next" value="{{ next }}" /> <!-- скрытый атрибут перенаправит на страницу откуда пришел -->
        <div class='form-error'>{{ form.non_field_errors }}</div> <!-- отображение общих ошибок -->
        {% for elem in form %}
            <label class='form-label' for='{{ elem.id_for_label }}'>{{elem.label}}: </label>{{ elem }}</p>
            <div class='form-error'>{{ elem.errors }}</div> <!-- ошибки заполнения формы -->
        {% endfor %}
        <button class='form-button' type='submit'>Войти</button>
    </form>
</div></div>
{% endblock content%}
```
- подключаем путь urls.py ->
```python
# LogoutView берем прямо из модуля
url_patterns.append(path('login/', LoginUser.as_view(), name='login'),
                    path('logout/', LogoutView.as_view(), name='logout'),)
```
- создаём обработчик формы views.py ->
```python
class LoginUser(LoginView):
    form_class = LoginForm # с какой формой работаем
    template_name = 'users/login.html' # куда рендер идет
    extra_context = {'title': 'Авторизация'} # дополняем словарь для шаблона
```

<a name='auth_reg5'><h4>Декоратор login_required и класс LoginRequiredMixin</h4></a>

```python
from django.contrib.auth.decorators import login_required # применяется в виде
# декоратора к функциям вщ views.py, может принимать необязательный аргумент login_url

from django.contrib.auth.mixins import LoginRequiredMixin # применяется к класса,
# так же содержит атрибут login_url
```

<a name='auth_reg6'><h4>Регистрация пользователей через функции представления</h4></a>

- создаём шаблон регистрации register.html ->
```html
{% extends 'base.html' %} <!-- какой шаблон расширяем -->
{% block content %} <!-- именованый блок -->
<form method='POST'> <!-- форма регистрации -->
    {% csrf_token %} <!-- скрытый атрибут для проверки -->
    <input type='hidden' name='text' value='{{ next }}' /> <!-- скрытый атрибут перенаправит на страницу откуда пришел -->
    {{ form.as_p }} <!-- представдение формы в виде параграфа(быстрый for) -->
    <button class='form-button' type='submit'>Войти</button> <!-- кнопочку не забудь! -->
</form>
{% endblock content %}
```
- создаём форму регистрации forms.py ->
```python
class RegisterUserForm(forms.ModelForm):
    username = forms.CharField(label='Логин')
    password = forms.CharField(label='Пароль', widget=forms.PasswordInput())
    password_2 = forms.CharField(label='Повтор пароля', widget=forms.PasswordInput())

    class Meta:
        model = get_user_model() # возврат текущей модели пользователя
        # поля отображаемые в поле
        fields = ['username', 'password', 'password_2', 'email', 'first_name', 'last_name']
        labels  = {'email': 'E-mail', 'first_name': 'Имя', 'last_name': 'Фамилия'}

    def clean_pswd(self):
        """ Валидатор, проверяет равны ли пароли """
        cd = self.cleaned_data
        if cd['password'] != cd['password_2']:
            raise forms.ValidationError('Пароли не совпадают')
        return cd['password']

    def clean_email(self):
        """ Валидатор, проверяет почту в базе данных """
        email = self.cleaned_data['email']
        if get_user_model().objects.filter(email=email).exists():
            raise forms.ValidationError('Почтовый адрес не уникален')
        return email
```
- создаём обработчик формы views.py ->
```python
def register(request):
    if request.method == 'POST':
        form = RegisterUserForm(request.POST)
        if form.is_valid():
            user = form.save(commit=False)
            user.set_password(form.cleaned_data['password']) # возвращает хэш пароля для сохранения
            user.save()
            return render(request, 'users/register_done.html', {'title': 'добро пожаловать!'})
    form = RegisterUserForm()
    return render(request, 'users/register.html', {'form': form, 'title': 'Регистрация'})
```
- подключаем путь urls.py ->
```python
url_patterns.append(path('register/', register, name='register'))
```
- подключаем путь в базовом шаблоне templates/base.html ->
```html
<a class='menu-link' href='{% url 'users:register' %}'>Регистрация</a>
- создаем страничку успешной решистрации
```

<a name='auth_reg7'><h4>Класс UserCreationForm</h4></a>
- для формы регистрации есть специальный класс
- унаследовав нашу форму от нового класса можно не заботиться об уникальности логина и совпадении паролей - класс реализует эти проверки
- переписываем класс формы user/forms.py ->

```python
from django.contrib.auth.forms UserCreationForm
class RegisterUserForm(UserCreationForm):
    username = forms.CharField(label='Логин', widget=forms.TextInput(attrs={'class': 'form-input'}))
    password1 = forms.CharField(label='Пароль', widget=forms.PasswordInput(attrs={'class': 'form-input'}))
    password2 = forms.CharField(label='Повтор пароля', widget=forms.PasswordInput(attrs={'class': 'form-input'}))
    class Meta:
        model = get_user_model() # возврат текущей модели пользователя
        # поля отображаемые в поле
        fields = ['username', 'password1', 'password2', 'email', 'first_name', 'last_name']
        labels  = {'email': 'E-mail', 'first_name': 'Имя', 'last_name': 'Фамилия'}
        widgets = {'email': forms.TextInput(attrs={'class': 'form-input'}),
                   'first_name': forms.TextInput(attrs={'class': 'form-input'}),
                   'last_name': forms.TextInput(attrs={'class': 'form-input'}),
        } # добавляем виджет где прописываем стили полей, которые не переопределли
    def clean_email(self):
        """ Валидатор, проверяет почту в базе данных """
        email = self.cleaned_data['email']
        if get_user_model().objects.filter(email=email).exists():
            raise forms.ValidationError('Почтовый адрес не уникален')
        return email
```
- перепишем шаблон формы чтобы отображались стили register.html ->
```html
{% extends 'base.html' %}
{% block content %}
<div class='post'><div class='post-content'><form method='post'>
        {% csrf_token %}
        <input type='hidden' name='text' value='{{ next }}' />
        <div class='form-error'>{{ form.non_field_errors }}</div>
        {% for elem in form %}
            <label class='form-label' for='{{ elem.id_for_label }}'>{{elem.label}}: </label>{{ elem }}</p>
            <div class='form-error'>{{ felem.errors }}</div>
        {% endfor %}
        <button class='form-button' type='submit'>Регистрация</button></form></div></div>
{% endblock content %}
```
- заменим функцию на класс ждя регистрации и переопределим маршрут views.py + urls.py ->
```python
from django.views.generic import CreateView
class RegisterUser(CreateView):
    form_class = RegisterUserForm
    template_name = 'users/register.html'
    extra_context = {'title': "Регистрация"}
    success_url = reverse_lazy('users:login') # перенаправление при успешной решистрации

urlpatterns.append(path('register/', views.RegisterUser.as_view(), name='register'))
```

<a name='auth_reg8'><h4>Авторизация через email. Профиль пользователя</h4></a>
- реализуем возможность аутентефикации [не только через логин](https://docs.djangoproject.com/en/4.2/topics/auth/customizing/) но и по почте
- внесем данные для аутентификации в setings.py ->

```python
AUTHENTICATION_BACKENDS = ['django.contrib.auth.backends.ModelBackend', 'users.authentication.EmailAuthBackend',]
```
- создадим authentication.py для возможности аутонтефикации по почте->
```python
import contextlib
from django.contrib.auth import get_user_model, backends
class EmailAuthBackend(backends.BaseBackend):
    def authenticate(self, request, username=None, password=None, **kwargs):
        user_model = get_user_model()
        with contextlib.suppress(user_model.DoesNotExist, user_model.MultipleObjectsReturned):
            """полученме пользователя по почте и сверка пароля"""
            user = user_model.objects.get(email=username)
            if user.check_password(password):
                return user
        return None

    def get_user(self, user_id):
        user_model = get_user_model()
        try:
            return user_model.objects.get(pk=user_id)
        except user_model.DoesNotExist:
            return None
```
- Для возможности просмтатривать и редактировать свой профиль на понадобятся:
  - создадим форму forms.ProfileUserForm ->
  ```python
  class ProfileUserForm(forms.ModelForm):
    username = forms.CharField(disabled=True, label='Логин', widget=forms.TextInput(attrs={'class': 'form-input'}))
    email = forms.CharField(disabled=True, label='E-mail', widget=forms.TextInput(attrs={'class': 'form-input'}))
     class Meta:
        model = get_user_model()
        fields = ['username', 'email', 'first_name', 'last_name']
        labels = {'first_name': 'Имя', 'last_name': 'Фамилия'}
        widgets = {'first_name': forms.TextInput(attrs={'class': 'form-input'}),
                   'last_name': forms.TextInput(attrs={'class': 'form-input'}),}
  ```
  - создадим шаблон users/profile.html -> аналогична register/lohin!!! без next
  - создадим маршрут urls.py ->
  ```python
  path('profile/', views.ProfileUser.as_view(), name='profile')
  ```
  - создадим обработчик ProfileUser ->
  ```python
  class ProfileUser(LoginRequiredMixin, generic.UpdateView):
    model = get_user_model()
    form_class = ProfileUserForm
    template_name = 'users/profile.html'
    extra_context = {'title': 'Профиль'}
    def get_success_url(self):
        return reverse_lazy('users:profile', args=[self.request.user.pk])
    def get_object(self, queryset):
        return self.request.user
  ```
  - скорректируем базовый шаблон, чтобы авторизовавшись наш логин вел на страницу профиля ->
  ```html
  <a class='menu-link' href='{% url 'users:profile' %}'>{{ user.username }}</a> |
  ```

<a name='auth_reg9'><h4>Классы PasswordChangeView и PasswordChangeDoneView</h4></a>
- для измения пароля авторизованного пользователя силами django достаточно воспользоваться 2 базовыми классами и на странице профиля добавить маршрут:
- urls.py ->

```python
    path('change-password/', PasswordChangeView.as_view(), name='change-password'),
    path('change-password-done/', PasswordChangeDoneView.as_view(), name='change-password-done')
```
- profile.html ->
```python
<p class='post post-content'><a href='{% url 'users:change-password' %}'>Сменить пароль</a></p>
```
- Для реализации собственных представлениий нам нужно:
  - создать 2 шаблона change_password.html и change_password_done.html ->
  ```html
  <!--шаблон смены - тот же что и для профиля отличие только в кнопке -->
{% extends 'base.html' %}
{% block content %}
    <div class='post-content'>
        <p>Вы успешно изменили пароль.</p><br>
        <p><a href="{% url 'users:profile' %}">Вернуться в профиль.</a></p>
    </div>
{% endblock %}
  ```
  - создаем форму для смены пароля forms.UserPasswordChangeForm ->
  ```python
  from django.contrib.auth.forms import PasswordChangeForm
  class UserPasswordChangeForm(PasswordChangeForm):
    old_password = forms.CharField(label="Старый пароль", widget=forms.PasswordInput(attrs={'class': 'form-input'}))
    new_password1 = forms.CharField(label="Новый пароль", widget=forms.PasswordInput(attrs={'class': 'form-input'}))
    new_password2 = forms.CharField(label="Подтверждение", widget=forms.PasswordInput(attrs={'class': 'form-input'}))
  ```
  - создаем класс обработчика views.UserPasswordChange ->
  ```python
  from django.contrib.auth.views import PasswordChangeView
  class UserPasswordChange(PasswordChangeView):
    form_class = UserPasswordChangeForm
    success_url = reverse_lazy("users:change_password_done")
    template_name = "users/change_password.html"
    extra_context = {'title': "Изменение пароля"}
  ```
  - urls.py ->
  ```python
  path('change_password/', UserPasswordChange.as_view(), name='change_password'), # за обработку теперь отвечает наш класс
  path('change_password_done/', PasswordChangeDoneView.as_view
         (template_name='users/change_password_done.html',
          title='успешно'), name='change_password_done'),# done принимает новый шаблон, его можно передавать в ()
  ```


#### <a name='gloss'><h3>Глоссарий</h3></a>
```python
&                           # логическое И
|                           # логическое ИЛИ
~                           # логическое НЕ
301                         # страница перемещена на другой постоянный URL-адрес
302                         # страница перемещена временно на другой URL-адрес
403                         # доступ запрещен
404                         # страница не найдена
500                         # ошибка сервера

<button type="submit">...</button>  # тег для описания кнопки отправки данных формы на сервер
<form>...</form>           # тег для описания HTML-формы
<input type="checkbox">    # тег для описания checkbox'a
<input type="number">      # тег для описания числового поля ввода
<input type="password">    # тег для описания поля ввода пароля
<input type="text">        # тег для описания текстового поля ввода
<label>...</label>         # тег для формирования подписей у полей ввода
<textarea>...</textarea>   # тег для описания полнотекстового, многострочного ввода

{% block [название] %} ... {% endblock %}   # формирование блока с возможностью его переопределения
{% extends <путь к шаблону> %}  # наследование от базового шаблона
{% for %} {% endfor %}          # реализация циклов
{% if %} {% endif %}            # проверка условия
{% include <путь к шаблону> %}  # включение одного шаблона в другой
{% url <параметры> %}           # формирование URL-адресов

@admin.action               # для регистрации нового (пользовательского) действия в админ-панели
@admin.display              # для настройки отображения вычисляемого поля в админ-панели
@admin.register             # для регистрации модели в админ-панели

Avg                         # подсчет среднего значения по указанному полю выборки
actions                     # список пользовательских действий
add                         # целочисленное прибавление к переменной заданного значения
admin.action                # определение нового действия в админ-панели
admin.display               # определение нового отображаемого поля в админ-панели
admin.py                    # для настройки админ-панели сайта
admin.register              # регистрация модели в админ-панели
admin.site.index_title      # атрибут для уточненного названия админ-панели (подзаголовок)
admin.site.register         # декоратор для регистрации моделей в админ-панели
admin.site.site_header      # атрибут для общего названия админ-панели
aggregate                   # вызов агрегирующих функций
all()                       # выбор всех записей из таблицы БД
allow_empty                 # флаг разрешения отображения страницы с пустым списком записей (для класса ListView)
annotate                    # формирование новых вычисляемых полей
apps.py                     # для настройки (конфигурирования) текущего приложения
authenticate()              # выполняет поиск пользователя (как правило, в БД) по паре username/password и возвращает объект пользователя, если он был найден
AUTHENTICATION_BACKENDS     # список бэкендов авторизации

BooleanField                # поле типа CheckBox
block                       # для создания блока замещаемой информации в дочерних шаблонах

CASCADE                     # задает удаление всех записей из вторичной модели, связанных с удаляемой записью первичной модели
CharField                   # текстовое поле типа TextInput (однострочное)
ChoiceField                 # поле выбора из множества значений типа Select
Count                       # подсчет числа записей в выборке
CreateView                  # для создания (добавления) новых записей в БД с использованием форм
capfirst                    # преобразование первого символа строки в заглавную букву
choices                     # список из кортежей в формате (значение, метка)
collectstatic               # сбор статических файлов проекта в единый каталог
context_object_name         # имя переменной для записи или списка записей внутри шаблона
count                       # общее число элементов в списке для разбивки по страницам/вычисление общего числа записей в выборке
count()                     # вычисление числа записей в выборке
create                      # создание новой записи в таблице БД
createsuperuser             # создание суперпользователя
cut                         # удаление указанного фрагмента из строки

DATABASES                   # настройки используемой СУБД в проекте
DO_NOTHING                  # удаление записей в первичной модели не вызывает никаких действий во вторичной модели
DateField                   # поле для даты
DateTimeField               # поле для даты и времени
DeleteView                  # для удаления записей из БД
DetailView                  # для отображения одной записи в шаблоне по GET-запросу
default                     # задает значение по умолчанию для ложных выражений
delete()                    # удаление записей из БД
divisibleby                 # проверка целочисленной делимости одного числа на другое

EmailField                  # поле для адреса электронной почты
earliest()                  # выбор записи с самой ранней датой (наименьшей)
elem                        # объект поля
elem.errors                 # список ошибок заполнения поля формы
elem.id_for_label           # идентификатор для тега label текущего поля
elem.label                  # наименование поля в HTML-форме
error_messages              # определяет набор сообщений об ошибках при заполнении разных полей
exclude                     # список исключаемых полей при редактировании записи в админ-панели
exclude()                   # выбор всех записей, кроме тех, что удовлетворяют критерию
exists()                    # проверка существования хотя бы одной записи в выборке
extends                     # для реализации наследования от базовых шаблонов/ для формирования URL-адресов по именам маршрутов
extra_context               # атрибут, содержащий набор переменных, передаваемых в шаблон

FileField                   # поле выбора произвольных файлов
FloatField                  # поле для вещественных значений
ForeignKey                  # для связей Many to One (многие к одному)
FormView                    # для обработки форм (используются GET и POST-запросы)
fields                      # список полей, используемых для отображения в HTML-форме
filter()                    # выбор записей по указанному критерию
first()                     # выбор первой записи
for                         # для включения одного шаблона в другой
forloop.counter             # номер итерации цикла (начиная с единицы)
forloop.counter0            # номер итерации цикла (начиная с нуля)
forloop.first               # флаг первой итерации цикла (True - для первой; False - для всех остальных)
forloop.last                # флаг последней итерации цикла (True - для последней; False - для всех остальных)
forloop.revcounter          # обратный счет числа итераций, доходя до единицы
forloop.revcounter0         # обратный счет числа итераций, доходя до нуля
form.non_field_errors       # список ошибок, общих при некорректном заполнении формы
form_class                  # ссылка на класс формы

get                         # метод, срабатывающий при поступлении GET-запроса
get()                       # получение строго одной записи из БД по определенному критерию
get_absolute_url            # вычисляет и возвращает URL-адрес конкретной записи объекта модели
get_context_data            # метод для формирования переменных для шаблона
get_object_or_404           # возвращает запись из таблицы БД, если она есть, иначе генерируется исключение 404 - страница не найдена
get_page                    # метод для получения объекта страницы с указанным номером, при неверном номере возвращается последняя страница
get_queryset                # метод для формирования выборки записей

Http404                     # для формирования исключения с кодом 404
HttpResponse                # для формирования ответа с кодом 200
HttpResponsePermanentRedirect   # для выполнения редиректа с кодом 301
HttpResponseRedirect        # для выполнения редиректа с кодом 302
handler400                  # невозможно обработать запрос
handler403                  # доступ запрещен
handler404                  # страница не найдена
handler500                  # ошибка сервера
has_next                    # метод проверки существования следующей страницы
has_other_pages             # метод проверки существования разбивки на страницы (наличие более одной страницы)
has_previous                # метод проверки существования предыдущей страницы

INSTALLED_APPS              # список зарегистрированных приложений
ImageField                  # поле выбора файла изображения
IntegerField                # поле для целочисленных значений
if                          # для проверки условий
include                     # для включения одного шаблона в другой / подключает список маршрутов из указанного файла
initial                     # определяет начальное значение поля
int                         # любое положительное целое число, включая 0
is_valid()                  # проверяет данные формы

join                        # объединение элементов коллекции в строку с указанным разделителем

LOGIN_REDIRECT_URL          # задает URL-адрес, на который следует перенаправлять пользователя после успешной авторизации
LOGIN_URL                   # определяет URL-адрес, на который следует перенаправить неавторизованного пользователя при попытке посетить закрытую страницу сайта
LOGOUT_REDIRECT_URL         # задает URL-адрес, на который перенаправляется пользователя после выхода из системы
ListView                    # для отображения нескольких записей в шаблоне по GET-запросу
label                       # определяет название поля в HTML-форме
labels                      # список из меток, заданных в классе перечисления
last                        # выделение последнего элемента коллекции
last()                      # выбор последней записи
latest()                    # выбор записи с самой поздней датой (наибольшей)
length                      # определение числа элементов коллекции
list_display                # список отображаемых полей модели в админ-панели
list_display_links          # список кликабельных полей модели в админ-панели
list_editable               # список редактируемых полей модели в админ-панели
list_filter                 # список полей для фильтрации записей в админ-панели
list_per_page               # число отображаемых записей на странице в админ-панели
login()                     # выполняет авторизацию пользователя на сайте
login_required              # ограничение доступа для неавторизованных пользователей
logout()                    # выход авторизованного пользователя из системы (перестает быть авторизованным)
lower                       # перевод в нижний регистр (все буквы малые)

MEDIA_ROOT                  # каталог размещения медиа-файлов (для загрузки и чтения)
ManyToManyField             # для связей Many to Many (многие ко многим)
Max                         # нахождение максимального значения в указанном поле выборки
Min                         # нахождение минимального значения в указанном поле выборки
ModelChoiceField            # поле выбора из множества значений, полученных из модели
makemigrations              # создание файлов миграций
max_length                  # задает максимальную длину символов, которые можно ввести в поле
migrate                     # применение файлов миграций
min_length                  # задает минимальную длину символов, которые можно ввести в поле
model                       # модель, из которой будут выбираться записи (для класса ListView)
models.py                   # для хранения моделей связи с базой данных

next_page_number            # метод получения номера следующей страницы
num_pages                   # общее число страниц, на которые разбит список записей

OneToOneField               # для связей One to One (один к одному)
objects                     # стандартный менеджер для обращения к БД
object_list                 # список записей текущей страницы
order_by                    # сортировка записей по указанным полям
order_by()                  # сортировка записей по указанным полям

PROTECT                     # запрещает удаление записи из первичной модели, если она используется во вторичной модели
page                        # метод для получения объекта страницы с указанным номером, при неверном номере генерируется исключение EmptyPage
page_range                  # итератор для перебора номеров страниц
paginate_by                 # число записей на страницу (при пагинации)
path                        # любая не пустая строка, включая символ ‘/’
path                        # служит для формирования шаблона маршрута
post                        # метод, срабатывающий при поступлении POST-запроса
previous_page_number        # метод получения номера предыдущей страницы

readonly_fields             # список полей, доступных только для просмотра, при редактировании записи в админ-панели
redirect()                  # выполняет перенаправление по указанному URL-адресу
render()                    # загружает файл шаблона, обрабатывает его и возвращает результат в виде объекта HTTP-ответа
render_to_string            # загружает файл шаблона, обрабатывает его и возвращает результат в виде строки
required                    # отвечает за обязательность заполнения поля
reverse                     # формирует URL-адрес на основе имени маршрута или представления
reverse_lazy                # отложенное (ленивое) формирование URL-адреса на основе имени маршрута или представления
runserver                   # запуск тестового веб-сервера

SET_DEFAULT                 # при удалении записи из первичной модели устанавливает значение по умолчанию у соответствующих записей вторичной модели
SET_NULL                    # при удалении записи из первичной модели устанавливает значение foreign key в NULL у соответствующих записей вторичной модели
STATICFILES_DIRS            # список дополнительных (нестандартных) маршрутов к статическим файлам, используемых для сбора и для режима отладки
STATIC_ROOT                 # путь к общей папке со статическими файлами (содержимое формируется командой collectstatic)
STATIC_URL                  # строка с префиксом URL-адреса для статических файлов
SlugField                   # поле для слага (slug)
Sum                         # подсчет суммы значений по указанному полю выборки
save()                      # сохранение/изменение записи в таблице БД
search_fields               # список полей, по которым осуществляется поиск в админ-панели
set_password()              # возвращает хэш пароля, использует ключ из сеттингс!
slug                        # слаг, то есть, латиница ASCII таблицы, символы дефиса и подчеркивания
slugify                     # конвертирование строки в слаг (slug)
startapp                    # создание нового приложения
startproject                # создание нового проекта
static                      # для подключения статических файлов

str                         # любая не пустая строка, исключая символ ‘/’
success_url                 # служит для указания URL-маршрута перенаправления после успешной обработки формы

TEMPLATES                   # настройки шаблонизатора Django
TemplateDoesNotExist        # класс исключения "шаблон не найден"
TemplateSyntaxError         # класс исключения для неправильно записанных в шаблоне команд
TemplateView                # для отображения шаблона по GET-запросу
template_name               # путь к файлу шаблона
tests.py                    # модуль с тестирующими процедурами

UpdateView                  # для изменения существующих записей в БД с использованием форм
User.check_password()       # проверка корректности пароля пользователя (сравниваются хэши)
User.is_authenticated()     # проверка, что пользователь аутентифицирован на данном сайте
User.objects                # стандартный менеджер записей для работы с таблицей user
User.save()                 # сохранение (изменение) данных пользователя в БД
User.set_password()         # установка нового пароля пользователя (вычисляется хэш)
update                      # изменение одного или нескольких полей записей выборки
update()                    # изменение значения поля для выбранных записей
upper                       # перевод в верхний регистр (все буквы заглавные)
url                         # для реализации наследования от базовых шаблонов
uuid                        # цифры, малые латинские символы ASCII, дефис

View                        # общий базовый класс для представлений
validators                  # задает список валидаторов для проверок корректности заполнения текущего поля
values                      # определение набора полей, фигурирующих в выборке
values                      # список из значений, заданных в классе перечисления
verbose_name                # атрибут для названия модели в единственном числе
verbose_name_plural         # атрибут для названия модели во множественном числе
views.py                    # для хранения представлений текущего приложения
widget                      # отвечает за настройку внешнего вида поля формы
```
