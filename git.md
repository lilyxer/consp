<!-- Команды гита -->
#### При любом варианте для удаленного храненения нам нужно создать репозиторий в гитлабе либо гитхабе. Имя origin, url ...origin.git
```
CTRL + ALT + T
origin - фактически именованная ссылка на удалённый репозиторий, имя задаётся при привязке remote!
main - имя главной ветки локального репозитория
SSH - terminal -> ssh-keygen -> public key: ssh-rsa -> settings: SSH -> new key

```
<br>
[установка GitBash](https://git-scm.com/download/win)
<br>
[песочница](https://markdown-here.com/livedemo.html)
<br>
[статья на habrе](https://habr.com/ru/company/ruvds/blog/599929/"habr")

<!-- Преднастройки-->

#### Пути
```
.                               - обозначает текущий каталог
..                              - обозначает родительский каталог
pwd / ls                        - где я / что здесь
cd ~                            - уйти в корень
mkdir <папка>                   - создание папки
touch <_.py>                    - создание файла
rm <_.py>                       - удаление файла
rmdir -r                        - удаление папки -r рекурсивно со всеми файлами в папке 
mv <файл> <папка>               - перемещение файла
```

#### venv Виртуальное окружение
``` 
apt-get install git             - установка гита
python -m venv my_venv          - создание my_venv
my_venv\Scripts\activate        - win активация
source my_venv\bin\activate     - linux активация
deactivate                      - деактивация
```

#### Инициализация
```
git init                        - создание локального репозитория в текущей папке
git help -g                     - вызов справки
```

#### Создание связи локального и удаленного репозитория (УР)
```
git clone <УР.git>              - создаст в текущей папке клон УР либо можно указать имя папки 
git remote add origin <УР.git>  - привязывает удаленный с локальным (у вас что то есть на компе) origin - имя для УР
git remote -v                   - покажет какие УР привязаны к вашему локальному
```

#### git config Конфигурация
```
--global user.name 'Mikhail Borisov'        - установка имени
--global user.email "borisov.uk@gmail.com"  - установка имейла
--global core.autocrlf input                - true for win  - формат строк
--global core.safecrlf warn                 - формат строк
--global core.quotepath off                 - чтение строк в неправильной кодировке
--global user.password                      - токен с гитхаба
--list                                      - сводная информация
--global credential.helper cache            - кэш учетки, ненужно вводить логин пароль
--global core.editor "C:\Program Files\Notepad++\notepad++.exe" - редактор по умолчаннию
--global init.defaultBranch main            - по дефолту при ините будут создаваться ветки 'main'
```

#### git help Помощь
```
<команда> либо git <команда> --help либо man git-<команда>
-h для вывода краткой инструкции
```

<!-- Работа с репозиториями -->
#### git add Добавление отслеживания (Stage)
```
<файл>                                      - добавление в отслеживание конкретного файла
*.py                                        - маска для всех питоновских файлов
.                                           - всё что видишь в текщем катологе и подкаталогах
git rm --cached <файл>                      - удаление из отслеживания
git rm --cached -r <папка>                  - удаление из отслеживания папки
```

#### git commit Фиксация изменений
```
                                            - без флага откроет редактор по умолчанию
-m 'comment'                                - (--message) комментирование в строке
-a -m 'comment'                             - (--all) все ранее отслеживаемые и измененные файлы в коммит 
--amend -m 'comment'                        - внесение изменений в предыдущий коммит (добавление или изменение файла, комментария). удаляет и создает новый!
git revert <хэш> -m 'comment'               - откат до конкретного коммита
git revert HEAD                             - откат последненго коммита
```

#### git tag именованный коммит
```
                                            - без флага отобразит теги в репозитории
-a v1.0 -m 'version 1.0'                    - создание нового коммита с тегом
<commit_id>                                 - присовит имя коммиту
-d v1.0                                     - удаление из локали тега v1.0
--delete <репозиторий> v1.0                 - из УР
```

#### Истории, просмотры
```
git log                                     - История коммитов первые 7 символов коммита - хеш
git log --oneline                           - История в компактном виде
git log --all                               - поиск по всем веткам
git log --<файл>                            - История коммитов которые содержат [файл]
git log -p --<файл>                         - объединяет log и show по файлу
git log --oneline --graph                   - отрисует лог с ветками и коммитами
git log --grep 'word'                       - поиск по вхождению word в коммите
git log -S'code' -p                         - ищет кусок кода
git show -<хэш>                             - показывает детальный лог изменения
git show --<файл>                           - изменение по файлу
git diff <файл>                             - покажет изменения в файле
git blame <файл>                            - кто что и когда изменил в файле
git reflog                                  - лог изменений в HEAD
```

#### Статус
```
git status                                  - Показывает состояние локального репозитория
```

#### Синхронизация с УР
```
git push origin main --all                  - отправка изменений на УР
git push --tags зальет                      - именованые комиты
git push --delete origin main               - удалит ветку и УР
git push -u origin main                     - (--set-upstream) если ветки и имени не существует, например сразу после git remote
git push                                    - ^ после -u помнит УР и ЛР
git fetch origin                            - история изменений
git pull                                    - fetch + merge // esc: wq | ^X
```

#### Ветвление
```
*main                                       - ветка по умолчанию
git branch                                  - просмотр веток в репозитории
git branch <ветка>                          - создание новой ветки
git branch -a                               - просмотр всех веток включая УР
git branch -d <ветка>                       - удалит ветку если она больше ненужна
git branch -M main                          - переименует ветку в main
git checkout <ветка/комит>                  - переход в ветку либо коммит
git checkout --track                        - должен подтянуть отслеживаемую ветку из УР в локаль
git checkout -b <ветка>                     - создаст и перейдет
git merge <branch>                          - слияние ветки <branch> в ту где находишься  - не забудь уйти! git checkout main
git merge origin/<branch>                   - слияние ветки <branch> из УР (origin) в ту где находишься
git merge --no-ff -m 'comment'              - создаст коммит объединение
git rebase <main>                           - из ветвления мы копируемся и полностью переходим в главную 
git rebase --abort                          - отменит слияние при конфликте
git rebase --continue                       - разрешить конфликт через gid add
```

#### git reset Откат коммитов
```
[хеш]/HEAD~[№]                              - Отменяет все коммиты после заданного, оставляя все изменения
--hard [хеш]/HEAD~[№]                       - Сбрасывает всю историю коммита после заданного
--soft                                      - удалит метку коммита, но оставит данные
--mixed                                     - по умолчанию
```

- HEAD - указатель на состояние ветки в которой мы находимся  
- ISSUES - задачи к коду? тикеты