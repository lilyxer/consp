[ДОКА](https://www.postgresql.org/)
[СПРАВОЧНИК](https://code.mu/ru/sql/manual/)

## установка
```bash
# PostgreSQL
sudo apt update && sudo apt install postgresql

# pgAdmin4
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee /etc/apt/sources.list.d/pgdg.list
sudo apt update && sudo apt install pgadmin4
```

[ТИПЫ ДАННЫХ](https://www.postgresql.org/docs/16/datatype.html)

## создание БД
```bash
createdb -U postgres my_base    # -U ключ от кого |  my_base - БД
sudo -u postgres psql -d mybase -h localhost # подключение к базе, можно без -d

ALTER USER user_name WITH PASSWORD 'new_password';
CREATE USER lilyxer WITH PASSWORD 'passlil';            # создаем юзера
CREATE DATABASE my_base;                                # создание БД через psql
GRANT ALL PRIVILEGES ON DATABASE "my_base" to lilyxer;  # пользователь может работать с базой
\с my_base;                                             # переходим в базу
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO "lilyxer";   # привелегии для работы с таблицами в базе

\?                              # список команд
exit                            # выход из БД
dropdb -U postgres my_base      # удаление БД
```

## Типы запросов
- DDL -> CREATE, ALTER, DROP      # управление БД
```sql
    alter table <name> ...
        -- добавить атрибут
        add column <col_name> <col_type>[constraints];
        -- переименовать таблицу
        rename to <new_table_name>;
        -- переименовать атрибут
        rename <col_name> to <new_col_name>;
        -- изменить тип атрибута
        alter column <col_name> set data type <col_type>;
        -- добавить ограничение
        add constraint <constraint_name> <constraint>;
        -- удалить ограничение
        drop constraint <constraint_name>;
        -- удалить атрибут
        drop column <col_name>;
    -- удалить таблицу
    drop table <name>;
```
- DML -> SELECT, INSERT, UPDATE, DELETE   # управление данными
```sql
    INSERT INTO course(name, description)
    VALUES('Python', 'Python с нуля');

    UPDATE course
       SET description = 'Java с нуля'
     WHERE id = 999;

    DELETE FROM course
     WHERE id = 999;
```
TCL -> COMMIT, ROCCLBACK, SAVEPOINT     # управление транзакциями
DCL -> GRANT, REVOKE, DENY      # управление доступом к БД
```

## создание таблицы
```sql
CREATE TABLE IF NOT EXISTS my_table (
    attr_name attr_type contains,
    ...
);
```

[CONTAINS](https://www.postgresql.org/docs/current/ddl-constraints.html)

## связи
[статья с примерами](https://habr.com/ru/articles/488054/)
- один-к-одному: пользователь и доп инофрмация о нем
```sql
    CREATE TABLE IF NOT EXISTS Students(
        id SERIAL PRIMARY KEY,
        email VARCHAR(60) UNIQUE NOT NULL,
        name VARCHAR(60) NOT NULL
    );

    CREATE TABLE IF NOT EXISTS StudentsInfo(
        id INTEGER PRIMARY KEY REFERENCES Students(id),
        birthday date
    );
```
- один-ко-многим: 1курс - много заданий
```sql
    CREATE TABLE IF NOT EXISTS Courses(
        id SERIAL PRIMARY KEY,
        name VARCHAR(60) NOT NULL
    );

    CREATE TABLE IF NOT EXISTS HWTasks(
        id SERIAL PRIMARY KEY,
        course_id INTEGER NOT NULL REFERENCES HWTasks(id),
        description TEXT NOT NULL
    );
```
- многие-ко-многим: много студентов - много курсов, реализация через промежуточную таблицу
```sql
    -- 1 sample
    CREATE TABLE IF NOT EXISTS CourseStudents(
        course_id INTEGER REFERENCES Courses(id),
        student_id INTEGER REFERENCES Students(id),
        CONSTRAINT pk PRIMARY KEY (course_id, student_id)
    );
    -- 2 sample
    CREATE TABLE IF NOT EXISTS HomeworkSolution (
	id SERIAL PRIMARY KEY,
	task_id INTEGER NOT NULL REFERENCES HomeworkTask(id),
	student_id INTEGER NOT NULL REFERENCES Student(id),
	solution TEXT NOT NULL);
```

## простая выборка
```sql
    SELECT <attr_name>
      FROM <table>
     WHERE <ограничение>
     ORDER BY <сортируем по полю> ASC\DESC
     LIMIT <первые н строк>
```
DISTINCT - уникальные значения <br>
IN / NOT IN ('first', 'second') - наличие в <br>
BETWEEN 0 AND 1 - промежуток <br>
LIKE / ILIKE 'some' - regex % _ <br>
AND/OR - И ИЛИ <br>
date('YYMMDD') - преобразование строки в дату <br>
CURDATE() / STR_TO_DATE(now(), '%Y-%m-%d') - текущая дата [пример работы с датами](http://lifeexample.ru/razrabotka-i-optimizacia-saita/sql-rabota-s-datami.html) <br>
EXTRACT (year from <date>)

## аггрегирующие функции
```
MAX
MIN
COUNT
AVG
SUM
```
- GROUP BY - группирует выборку по заданым трибутам, атрибут так же обязан бытьв SELECT
- HAVING - аналог WHERE, но в отличии от WHERE, HAVING фильтрует после груупировки, а WHERE ДО. так же они работают ДО SELECT!
- AS - псевдоним для столбца или таблицы

## объединение таблиц JOIN
```
[INNER] JOIN - объединение при совпадении
LEFT [OUTER] JOIN - все значения из левой заполняются NULL при отсутствии данных из правой
RIGHT [OUTER] JOIN - аналог LEFT наоборот
FULL [OUTER] JOIN - left+ right
CROSS JOIN - все со всеми

FROM table_a
JOIN table_b ON table_a.id = table_b.id
```
