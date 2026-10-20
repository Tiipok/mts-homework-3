# Домашняя работа 3

Контейнеризация существующего REST-теста `test_create_patch_get_post` из
[работы 2](https://github.com/Tiipok/mts-homework-2).
API: `https://playground.nileslabs.com/api/v1`.
Тест создаёт пост, меняет заголовок, проверяет сохранённые данные и удаляет свой пост.

## Запуск

Нужны запущенный Docker и доступ в интернет. Команды выполняются из каталога с `Dockerfile`:

```bash
docker build -t api-test .
docker run --rm api-test
```

Тест запускается автоматически. Результат виден в консоли:
при успехе — `1 passed` и exit code `0`, при падении — ненулевой код.

Проверка неуспешного запуска с отключённой сетью (ожидается exit code `1`):

```bash
docker run --rm --network none api-test
```

## CI

Конфигурация: [`.github/workflows/ci.yml`](.github/workflows/ci.yml).
Запуск при `push`, `pull_request` и вручную через GitHub Actions.
Pipeline получает исходники, собирает Docker image и запускает тест в том же образе.
Ошибка сборки или теста делает job и pipeline неуспешными.

[Успешный запуск CI от 20.10.2026](https://github.com/Tiipok/mts-homework-3/actions/runs/39438327176/job/118681192576).
