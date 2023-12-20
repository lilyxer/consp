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