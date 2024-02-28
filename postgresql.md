[ДОКА](https://www.postgresql.org/)

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
```
DML -> SELECT, INSERT, UPDATE, DELETE   # управление данными
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
    -- 2 saample
    CREATE TABLE IF NOT EXISTS
```