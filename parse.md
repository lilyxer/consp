```
инструмент разработчика - F12/посмотреть код
view-source:https://... - со сматрфона
```

## DOM дерево HTML
-[Document Object Model](https://ru.wikipedia.org/wiki/Document_Object_Model) — набор правил, согласно которым формируется каждая веб-страница с использованием языка разметки HTML. DOM хранит структуру сайта в виде дерева и информацию, заключённую в тегах.
- Каждый элемент этого дерева называется узлом. Узлы могут иметь неограниченную вложенность.
- [пример HTML элементов](https://parsinger.ru/2.1/DOM/block_strok_elem.html)
- Дерево DOM служит нам инструментом для поиска нужного элемента на странице и взаимодействия с ним.
- пример парсера на основе BS

```python
import requests
from bs4 import BeautifulSoup
url = 'http://parsinger.ru/2.1/DOM/index2.html' # Отправляем GET-запрос на веб-страницу
response = requests.get(url)
response.encoding = 'utf-8'
if response.status_code == 200: # Проверяем статус-код ответа
    soup = BeautifulSoup(response.text, 'html.parser') # Инициализируем объект BeautifulSoup для парсинга HTML
    target_element = soup.find(id="text777") # Ищем элемент с ID "text777"
    if target_element: # Извлекаем текст из найденного элемента
        extracted_text = target_element.text
        print(f"Извлеченный текст: {extracted_text}")  # Вывод на экран
    else:
        print("Элемент с ID 'text777' не найден.")
else:
    print(f"Не удалось получить доступ к странице, статус-код: {response.status_code}")
```

- поиск элементов на странице
  - используя HTML-теги, CSS-селекторы, атрибуты и их значения
    - [id="name_id"] <==> #name_id: по идентификатору
    - [class="name_class"] <==> .name_class: по имени класса
    - <div>: по имени тега
    - [href="link"]: по атрибуту
    - [name_attr="item"]: по значению атрибута (ищет узлы где есть атрибут name_attr, который равен 'item')
  - комбинированный
    - .name_class[name_attr]
  - используя составные селекторы
    - составные селекторы (' ')
      - .parent .child: будет искать все классы child на любом уровне вложенности у родительского parent
      - .parent .child p: углубляемся, получим только данные с селктором р
    - Потомки / дочерние элементы (' > ')
      - .parent > .child: непосредственный потомок. глубина =1!
    - по порядковому номеру дочернего элемента
      - .parent > .child:nth-child(x): ищет конкретного потомка под номером х - отсчёт от 1!
    - если более 1 класса
      - .class1.class2: без пробела, найдет узел где именно эти 2 и более классов
  - используя XPath
    - XPath всегда начинается с символа / или //: Символ / аналогичен символу > , а символ // — пробелу в CSS.
      - Важная особенность: если начать XPath-запрос с символа /, корнем запроса всегда будет элемент с тегом <html>
      - Если же запрос начинается с //, это означает, что нужно найти все потомки корневого элемента, без уточнения, кто является корнем
        - ПРИМЕР: //article - найдет все узлы, /article найдет узлы только если он является прямым потомком
    - [<правило>] - Команда для фильтрации элементов.
      - по атрибутам [@атрибут='значение'] - фильтр по атрибутам у которое такое значение
      - по порядковому номеру [X] - фильтр по номеру Х, начинается от 1
      - по полному совпадению текста [text()='значение'] - например найдем все кнопки купить //button[text()="Купить"]
      - по частичному совпадению текста [contains(text(),'значение')] - ищем вхождение значения
      - по частичному совпадению атрибута [contains(@атрибут,'значение')] - ищем вхождение значения
      - символ * - команда выбора всех элементов
    - Навигация по поиску с атрибутами:
      - /child::* - находим все дочерние элементы
      - /parent::* - находим родительский элемент
      - /following-sibling::* - переход к следующему соседнему элементу
      - /preceding-sibling::* - переход к предыдущему соседнему элементу
      - /child::p[1] - переход к конкретному дочернему элементу
      - //child::<элемент> - поиск по вложенным элементам
    - Навигация по поиску без атрибутов:
      - //div/*[1] - поиск первого дочернего элемента
      - //div/*[last()] - поиск последнего дочернего элемента
      - //ul/li[position()=3] - поиск по порядковому номеру
      - //div[count(*)>0] - поиск элементов, имеющих дочерние элементы
      - //*/*/*/*[name()='p'] - поиск элементов на определенной глубине(4й уровень вложенности)

## Requests.get - работа с запросами
- auth
```python
from requests.auth import HTTPBasicAuth
# Указываем логин и пароль
response = requests.get('https://www.example.com', auth=HTTPBasicAuth('user', 'pass'))
```
- session
```python
with requests.Session() as s:
    s.get('https://www.example.com/login')
    response = s.get('https://www.example.com/data')
```
- upload
```python
# Открываем файл и отправляем его на сервер
with open('file.txt', 'rb') as f:
    files = {'file': f}
    response = requests.post('https://www.example.com/upload', files=files)
```
- потоковая передача
```python
# Загрузка файла с сервера по частям
with requests.get('https://www.example.com/file', stream=True) as r:
    with open('file.txt', 'wb') as f:
        for chunk in r.iter_content(chunk_size=8192):
            f.write(chunk)
```
- параметры get
```
url 	    Передает ссылку - цель, куда будет отправлен запрос. (Обязательный)
params  	Опциональный словарь или последовательность пар ключ-значение для отправки в строке запроса. (Необязательный)
headers  	Опциональный словарь для настройки HTTP-заголовков.  (Необязательный)
cookies  	Опциональный словарь или объект CookieJar с куки, которые будут отправлены.  (Необязательный)
auth  	  AuthObject для включения базовой аутентификации HTTP.  (Необязательный)
timeout  	Число с плавающей запятой, описывающее тайм-аут запроса.(Необязательный)
allow_redirects  	Логическое значение. Установите значение True, если разрешено перенаправление. (Необязательный)
proxies  	Протокол сопоставления словаря с URL-адресом прокси.  (Необязательный)
stream 	  Удерживает соединение открытым, пока не получен весь Response.content (Необязательный)
data 	    Опциональный параметр для отправки данных в теле запроса (не рекомендуется для GET-запросов). (Необязательный)
cert 	    Опциональный кортеж для указания пользовательского сертификата. (Необязательный)
```
- параметры response
```
status_code HTTP-код статуса ответа.
text 	      Текстовое представление содержимого ответа.
content 	  Содержимое ответа в виде байтов.
json 	      Метод для десериализации JSON-ответа.
headers 	  Заголовки HTTP, возвращаемые сервером.
url 	      Исходный URL-адрес, на который был выполнен запрос.
encoding 	  Кодировка ответа.
elapsed 	  Время, затраченное на выполнение запроса.
cookies 	  Куки, возвращаемые сервером.
history 	  Список объектов Response, представляющих историю перенаправлений.
ok 	        Логический атрибут, указывающий, был ли запрос успешным (коды 2xx).
reason 	    Сообщение статуса HTTP (например, "OK", "Not Found").
```
- headers
  - Общие заголовки (General Headers): Эти заголовки присутствуют и в запросах, и в ответах, но не относятся непосредственно к телу сообщения. Пример: Cache-Control, Connection.
  - Заголовки запроса (Request Headers): Отправляются только в HTTP-запросах. Они могут содержать информацию о ресурсе, который нужно получить, или о клиенте, который этот ресурс запрашивает. Примеры: Accept, User-Agent.
  - Заголовки ответа (Response Headers): Отправляются только в HTTP-ответах. Они обычно содержат информацию о сервере и о том, как интерпретировать тело ответа. Примеры: Server, WWW-Authenticate.
  - Сущностные заголовки (Entity Headers): Эти заголовки описывают тело сообщения, его тип и модификации. Примеры: Content-Type, Content-Encoding.
```python  # https://pypi.org/project/fake-headers/
import requests
from fake-headers import UserAgent
ua = UserAgent(browsers=['firefox', 'chrome'], os='linux', min_percentage=5)
headers = {  # Определение заголовков, которые будут отправлены с запросом
    'User-Agent': ua.random, # рандомно, либо прописываем жёстко
    'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36',  # какой "браузер" используется для запроса
    'Accept-Language': 'ru-RU,ru;q=0.9,en-US;q=0.8,en;q=0.7'  # какие языки предпочтительны для клиента,  
    'Accept-Encoding': 'gzip, deflate, br',  # какие методы сжатия контента клиент может обработать
    'Accept': 'text/html,application/xhtml+xml',  # какие типы медиа
    'Connection': 'keep-alive',  # соединение с сервером должно оставаться открытым для последующих запросов
    }
# Выполнение GET-запроса с установленными заголовками
response = requests.get('http://httpbin.org/user-agent', headers=headers)
print(response.text)
```
- timeout - определяет максимальное время (в секундах), которое метод .get() будет ждать ответа от сервера. 
```python 
from requests.exceptions import ConnectTimeout
```
- URL params - ?key1=value1&key2=value2 для фильтрации, пагинации, API, динамика
```python
params = {'key1': 'valkue1'}
```
Прокси proxies - подмена ip адреса, ищем бесплатные прокси и проверяем на работоспособность
```python
import requests
def get_working_ips(cleans: str, working: str, url: str) -> None:
    with (open(cleans, 'r', encoding='utf-8') as readable, 
        open(working, 'a+', encoding='utf-8') as writable):
        ips = readable.read().splitlines()
        for elem in ips:
            proxy = {
                'http': f'http://{elem}', 
                'https': f'https://{elem}'}
            try: 
                resp = requests.get(url=url, proxies=proxy, timeout=2)
                writable.write(f'{elem}\n')
                print(f'{resp.json()} succes')
            except Exception as _e:
                continue
```
- загрузка файлов
```python
response = requests.get(url=url, stream=True)
for piece in response.iter_content(chunk_size=100000):
```