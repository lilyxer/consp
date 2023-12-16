## [DRF](https://www.django-rest-framework.org/)
```
rest clinet -> request.http
GET http://localhost:8000 - > send request ### 
POST http://localhost:8000 - > send request ### 
pip install djangorestframework  # DRF -> INSSTALLED_APPS [rest_framework]
from rest_framework import generics
.CreateAPIView                  # создание POST
.ListAPIView                    # возврат списка GET
.RetrieveAPIView                # возврат записи GET
.DestroyAPIView                 # удаление записи DELETE
.UpdateAPIView                  # изменение записи PUT + PATCH
.ListCreateAPIView              # чтение и возврат списков GET POST
.RetrieveUpdateAPIView          # чтение и изменение записи GET PUT + PATCH
.RetrieveDestroyAPIView         # чтение и удаление записи GET DELETE
.RetrieveUpdateDestroyAPIView   # чтение удаление и изменение записи  GET, PUT, PATCH, DELETE
```

#### [ViewSet](https://www.django-rest-framework.org/api-guide/viewsets/)
```
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
```

#### [Permissions](https://www.django-rest-framework.org/api-guide/permissions/)
```
AllowAny # Полный доступ
IsAuthenticated # авторизованный пользователь
IsAdminUser # администатор
IsAuthenticatedOrReadOnly # авторизованный пользователь/только чтение
```

#### [Авторизация и Аутентефикация](https://www.django-rest-framework.org/api-guide/authentication/)
```
Session-based authentication # на основе кукис и сессии
Token-based authentication # на основе токенов // Djoser
JSON WEB Token  // Simple JWT 
Django REST framework OAth # через соцсети
```

#### [Пагинация](https://www.django-rest-framework.org/api-guide/pagination/)
```
setting.py ->
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.LimitOffsetPagination',  # offset - начиная с 
    # 'rest_framework.pagination.PageNumberPagination' # обычная постраничная
    'PAGE_SIZE': 100
}
для каждого отображения можем подключать собственную пагинацию
наследуясь от PageNumberPagination
```

#### [Валидация](https://www.django-rest-framework.org/api-guide/validators/)
```
serializers.py ->
from rest_framework.exceptions  import ValidationError
def validate(self, data): логика валидации raise ValidationError / retrun data 
create/update - так же перегружаются при необходимости
```

#### [Фильтрация](https://www.django-rest-framework.org/api-guide/filtering/)
```
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
```