# Реализации

## Синглтон
```python
class Singleton:
    _instance = None

    def __new__(cls, *args, **kwargs):
        if cls._instance is None:                       # при первом вызове создаем объект
            cls._instance = object.__new__(cls)
        return cls._instance                            # склько бы раз не вызывался конструктор - объект будет только 1 в памяти
```
## Вызываемые объекты
```python
1. альтернатива замыканиям
class LineGenerator:                                |   def line_generator(k, b):
    def __init__(self, k, b):                       |       def func(x):
        self.k = k                                  |           return k * x + b
        self.b = b                                  |       return func
    def __call__(self, x):                          |line_func1 = line_generator(2, 5)          # получаем функцию y = 2*x + 5
        return self.k * x + self.b                  |print(line_func1(10))                      # выводим значение 2*10 + 5 = 25
2. реализация декораторов на основе класса
class UppercaseDecorator:                           |   def uppercase_decorator(func):
    def __init__(self, func):                       |       def wrapper(*args, **kwargs):
        self.func = func                            |           return func(*args, **kwargs).upper()
    def __call__(self, *args, **kwargs):            |       return wrapper  
        return self.func(*args, **kwargs).upper()   |
@UppercaseDecorator                                 |@uppercase_decorator
                            def greet(name): return f'Привет, {name}'
3. полезен в классах, чьи экземпляры часто изменяют своё состояние
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    def __call__(self, x, y): self.x, self.y = x, y
```
## Работа с атрибутами
```python
class Cat:
    def __init__(self, name, **kwargs) -> None: 
        self.name = name 
        if kwargs:
            for key, value in kwargs.items():
                object.__setattr__(self, key, value)
    def __getattribute__(self, attr):                   # получение значения атрибута attr объекта self
        if attr.startswith('_'):
            raise AttributeError('Доступ невозможен')
        return object.__getattribute__(self, attr)      # если его нет рейзится исключение !!! НЕЛЬЗЯ self.__dict__[attr] !!!
    def __getattr__(self, attr):                        # вызывается если __getattribute__ не прописан или вощбудил исключение
        return None                                     # возвращает значение по умолчанию
    def __setattr__(self, attr, value) -> None:         # устанавливает атрибуту attr значение value
        if attr in self.__dict__:                       # логика, например запрещение менять значение атрибута
            raise AttributeError('Изменение невозможно')
        elif attr.startswith('_'):
            raise AttributeError('Доступ невозможен')
        object.__setattr__(self, attr, value)           # <==> self.__dict__[attr] = value !!! НЕЛЬЗЯ self.attr = value !!!
    def __delattr__(self, attr):                        
        object.__delattr__(self, attr)                  # <==> del self.__dict__[attr]
```
## Быстрое сравнение ключей
```python
    def fast_match(key, target_key):                    # key и target_key — сравниваемые ключи
        if key is target_key:
            return True                                 # ключи равны, если это один и тот же объект                 
        if hash(key) != hash(target_key):
            return False                                # ключи не равны, если не равны их хеш-значения
        return key == target_key
```
## Протокол итерируемых объектов и итераторов
```python
class Counter:                                          # класс итератора 
    def __init__(self, low, high):                      # генерирует числа от лоу до хай
        self.low = low
        self.high = high
    def __iter__(self):
        return self
    def __next__(self): 
        if self.low > self.high:                        # при исчерпании рейзит стоп итер
            raise StopIteration                         # for ловит эту ошибку
        self.low += 1
        return self.low - 1

class EvenNumbers:                                      # бесконечный итератор           
    def __init__(self, begin):                 
        self.begin = begin + begin % 2                  # генерирует чётные числа
    def __iter__(self):
        return self
    def __next__(self):
        value = self.begin
        self.begin += 2
        return value

class Factorials:                                       # бесконечный итератор        
    def __init__(self):
        self.value = 1                                  # генериует факториал числа
        self.index = 1
    def __iter__(self):
        return self
    def __next__(self):
        self.value *= self.index
        self.index += 1
        return self.value

class Order:
    def __init__(self, cart, customer):
        self.cart = list(cart)          # список покупок
        self.customer = customer        # имя покупателя
    def __iter__(self):                 
        yield from self.cart            # или (elem for elem in self.cart) или iter(self.cart)
```
## Протокол последовательности
```python
class Order:
    def __init__(self, cart, customer):
        self.cart = list(cart)                  # список покупок
        self.customer = customer                # имя покупателя
    def __len__(self):
        return len(self.cart)
    def validate_index(self, x: int):           # проверка индекса
        if not isinstance(x, int|slice):
            raise TypeError('Индекс должен быть целым числом')
        length = len(self.cart) + 1
        if isinstance(x, int) and not x in range(-length, length):
            raise IndexError('Неверный индекс')
        return x
    def __getitem__(self, key):                 # получение значения по индексу
        if isinstance(key, slice):
            return Order(self.cart[x], self.customer)
        key = self.validate_index(key)
        return self.cart[key]
    def __setitem__(self, key, value):          # присваивание значения по индексу
        key = self.validate_index(key)
        self.cart[key] = value
    def __delitem__(self, key):                 # удаление значения по индексу
        key = self.validate_index(key)
        del self.cart[key]
    def __contains__(self, item):               # проверка значения в объекте
        return item in self.cart
    def __iter__(self):                         # получение итератора для прохождения циклов
        yield from self.cart
```
## Оператор with
```python
file = open('output.txt',                       |   with open('output.txt', mode='w',
            mode='w', encoding='utf-8')         |             encoding='utf-8') as file:
try:                                            |   file.write('Python generation!')
    file.write('Python generation!')            |
finally:                                        |
    file.close()                                |
```
## Протокол контекстных менеджеров
```python
import os                                                           # Встроенный модуль os содержит функцию scandir(), которая возвращает итератор типа ScandirIterator, 
with os.scandir('.') as entries:                                    # поддерживающий протокол контекстного менеджера. Эта функция специально разработана для обеспечения 
    for entry in entries:                                           # оптимальной производительности при перемещении по структуре папок.
        print(entry.name, '--->', entry.stat().st_size, 'bytes')    # ducks.zip ---> 641592 bytes

from tempfile import TemporaryFile                                  # Встроенный модуль tempfile (https://docs.python.org/3/library/tempfile.html) содержит контекстный менеджер 
with TemporaryFile(mode='r+') as file:                              # TemporaryFile, который позволяет создавать и удалять временные файлы автоматически.
    file.write('Python generation!')                                # временный файл создается при входе в тело блока контекста и удаляется при выходе. Такой подход позволяет 
    file.seek(0)                                                    # избежать утечек памяти и ошибок, связанных с неправильным удалением временных файлов.
    content = file.read()
    print(content)

class WritableTextFile:
    def __init__(self, path):                                       # Контекстный менеджер WritableTextFile позволяет работать 
        self.path = path                                            # с открытыми для записи текстовыми файлами в кодировке UTF-8
    def __enter__(self):
        self.file = open(self.path, mode='w', encoding='utf-8')     # with WritableTextFile('output.txt') as file:
        return self.file                                            #     file.write('Python generation!')
    def __exit__(self, exc_type, exc_value, traceback):
        if self.file:
            self.file.close()

import sys
class RedirectedStdout:                                             # Контекстный менеджер RedirectedStdout временно перенаправляет 
    def __init__(self, new_output):                                 # стандартный вывод sys.stdout на некоторый файл на диске
        self.new_output = new_output
    def __enter__(self):                                            # with open('output.txt', mode='w', encoding='utf-8') as file:
        self.standard_output = sys.stdout                           #     with RedirectedStdout(file):
        sys.stdout = self.new_output                                #         print('Python generation!')
    def __exit__(self, exc_type, exc_value, traceback):             #     print('Возврат к стандартному потоку вывода')
        sys.stdout = self.standard_output 

from time import perf_counter
class Timer:      ## Многоразовый ##                                # Контекстный менеджер Timer позволяет измерять время выполнения блока кода
    def __enter__(self):
        self.start = perf_counter()                                 # from time import sleep
        return self                                                 # with Timer() as timer:
    def __exit__(self, exc_type, exc_value, traceback):             #     sleep(0.7)
        self.elapsed = perf_counter() - self.start                  # print('Затраченное время:', timer.elapsed)

Декоратор @contextmanager – элегантный и практически полезный инструмент, который сводит воедино три совершенно разных механизма Python: 
декоратор функции, генератор и оператор with.
Обычно с помощью декоратора @contextmanager создают относительно несложные контекстные менеджеры.
Функция, к которой применяется декоратор @contextmanager, обязательно должна иметь инструкцию yield
                                Примеры создания контекстных менеджеров
1. Контекстный менеджер Trace выводит информацию перед входом в блок with и после выхода из блока with, включая информацию об исключении, 
если оно было возбуждено во время выполнения блока with.
class Trace:                                                |   @contextmanager
    def __enter__(self):                                    |   def trace():
        print('Начало выполнения блока with')               |       print('Начало выполнения блока with')
    def __exit__(self, exc_type, exc_value, traceback):     |       try:
        if exc_value:                                       |           yield
            print(f'возникло исключение {exc_value}')       |       except Exception as error:
        print('Конец выполнения блока with')                |           print(f'Во время выполнения блока with возникло исключение {error}')
        return True    # обрабатываем все типы исключений   |       finally:
from contextlib import contextmanager                       |           print('Конец выполнения блока with')

2. Контекстный менеджер WritableTextFile позволяет работать с открытыми для записи текстовыми файлами в кодировке UTF-8
class WritableTextFile:                                     |   from contextlib import contextmanager
    def __init__(self, path):                               |   @contextmanager
        self.path = path                                    |   def writable_text_file(path):
    def __enter__(self):                                    |       file = open(path, mode='w', encoding='utf-8')
        self.file = open(self.path, mode='w', encoding='utf-8')
        return self.file                                    |       yield file
    def __exit__(self, exc_type, exc_value, traceback):     |       file.close()
        self.file.close()

3. Контекстный менеджер RedirectedStdout временно перенаправляет стандартный вывод sys.stdout на некоторый файл на диске:
import sys                                                  |   import sys
class RedirectedStdout:                                     |   from contextlib import contextmanager
    def __init__(self, new_output):                         |   @contextmanager
        self.new_output = new_output                        |   def redirected_stdout(new_output):
    def __enter__(self):                                    |       standart_output = sys.stdout
        self.standart_output = sys.stdout                   |       sys.stdout = new_output
        sys.stdout = self.new_output                        |       yield
    def __exit__(self, exc_type, exc_value, traceback):     |       sys.stdout = standart_output
        sys.stdout = self.standart_output 
                                with open('output.txt', mode='w', encoding='utf-8') as file:
                                    with redirected_stdout(file):
                                        print('Python generation!')
                                    print('Возврат к стандартному потоку вывода')

4. Контекстный менеджер Timer позволяет измерять время выполнения блока кода
from time import perf_counter                               |   from time import perf_counter
class Timer:                                                |   from contextlib import contextmanager
    def __enter__(self):                                    |   @contextmanager
        self.start = perf_counter()                         |   def timer():
        return self                                         |       start = perf_counter()
    def __exit__(self, exc_type, exc_value, traceback):     |       yield
        self.elapsed = perf_counter() - self.start          |       end = perf_counter()
                                                            |       elapsed = end - start
                                                            |       print('Затраченное время:', elapsed)
                                                    from time import sleep
                                                    with timer():
                                                        sleep(0.7)
                                                        sleep(1.5)
```
## Декорирование
```python
import functools
class decorator:                                    # класс декоратор принимает в себя декорируемую фун-ию
    def __init__(self, func):
        functools.update_wrapper(self, func)        # сохранение информации о декорируемой функции
        self.func = func                            # декорируемая функция
    def __call__(self, *args, **kwargs):
        for _ in range(2):
            value = self.func(*args, **kwargs)      # вызов декорируемой функции
        return value

class do_n_times:                                   # декортатор с аргументами 
    def __init__(self, n):                          # на вход принимаем аргументы
        self.n = n
    def __call__(self, func):                       # метод принимает функцию для декорирования
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            for _ in range(self.n):
                value = func(*args, **kwargs)
            return value
        return wrapper

def add_attr(cls):
    cls.attr = None                                 # добавляем декорируемому классу атрибут attr
    return cls                                      # возвращаем декорируемый класс
@add_attr
class MyClass: ...

import functools
def add_attr_to_instances(cls):
    old_init = cls.__init__                         # сохраняем исходный инициализатор
    @functools.wraps(old_init)
    def new_init(self, *args, **kwargs):
        old_init(self, *args, **kwargs)             # вызываем исходный инициализатор
        self.attr = None                            # добавляем экземпляру класса атрибут attr
    cls.__init__ = new_init                         # заменяем исходный инициализатор новым
    return cls
@add_attr_to_instances
class MyClass: ...

import functools

def add_attr_to_instances(**attrs):
    def decorator(cls):
        old_init = cls.__init__
        @functools.wraps(old_init)
        def new_init(self, *args, **kwargs):
            old_init(self, *args, **kwargs)
            self.__dict__.update(attrs)             # добавляем атрибуты экземпляру декорируемого класса
        cls.__init__ = new_init
        return cls
    return decorator
@add_attr_to_instances(first_attr=1, second_attr=2)
class MyClass: ...
```
## Классы данных
```python
from dataclasses import dataclass, make_dataclass, field   # реализованы __init__, __repr__, __eq__
@dataclass                                          |   Person = make_dataclass('Person', ['name', 'surname', 'age'])
class Person:                                       |   person = Person('Гвидо', 'ван Россум', 67)
    name: str
    surname: str = field(default='')
    age: int = None                                 # знач по умолчанию
    friends: list = field(default_factory=list)     # класс создается 1 раз, если просто [] - будет общим для всех
person = Person('Гвидо', 'ван Россум', 67)
# флаги декортатора:
    (frozen=False)      # изменяемость 
    (order=True)        # сравнение
    ```