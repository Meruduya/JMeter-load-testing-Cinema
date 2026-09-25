# JMeter load testing

Учебный проект по нагрузочному тестированию в Apache JMeter.

**Stack:** `Apache JMeter` · `Docker Compose` · `MySQL / MariaDB` · `CSV test data`

## Содержание репозитория

| Папка | Что внутри |
|-------|-----------|
| `cinema/` | тестовый стенд: веб-приложение кинотеатра, база данных и phpMyAdmin в Docker Compose |
| `test/` | JMeter-сценарии на основе открытого шаблона нагрузочного тестирования WordPress |
| `test/Add-comment.jmx` | собственный сценарий: добавление комментария к записи блога, 100 потоков |
| `test/modules/` | переиспользуемые модули: вход, главная страница, просмотр записи, поиск, RSS, комментарии, лайки |
| `test/*.properties` | параметры нагрузки и окружений (dev, test, qa, stag) |
| `test/dataset-*/` | тестовые данные в CSV для каждого окружения |

## Типы тестов

- **single-user.jmx** — прогон одним анонимным и одним авторизованным пользователем, чтобы получить базовое время отклика (baseline);
- **loadtest.jmx** с `loadtest-stag.properties` — нагрузочный тест с заданной интенсивностью действий;
- **loadtest.jmx** с `stresstest-stag.properties` — стресс-тест с повышенной нагрузкой.

Параметры нагрузки (время разгона, длительность, интенсивность действий, доля анонимных и авторизованных пользователей) задаются в `.properties`-файлах.

## Запуск тестового стенда (кинотеатр)

Нужны Docker и Docker Compose.

```
cd cinema
docker-compose up -d
```

После запуска:

- сайт: http://localhost:8000
- панель администратора: http://localhost:8000/admin
- phpMyAdmin: http://localhost:8081

Если сайт показывает ошибку `Array['errMessage']`, нужно загрузить данные в базу: открыть phpMyAdmin (логин `admin`, пароль `test`), выбрать базу `database` и выполнить скрипт `cinema/BD/id11870327_mysite.sql` на вкладке SQL.

Остановка стенда:

```
cd cinema
docker-compose down
```

## Запуск JMeter-сценариев

Сценарии из папки `test/` рассчитаны на WordPress, развёрнутый локально на `http://localhost`. Адрес сервера задаётся в `.properties`-файлах (`webserver`, `service_port`, `service_protocol`).

Нужно задать переменную окружения `JMETER_PATH` с путём к JMeter и запустить нужный `.bat`-файл из папки `test`, например:

```
%JMETER_PATH%\bin\jmeter.bat -p single-user-dev.properties -t single-user.jmx
```

