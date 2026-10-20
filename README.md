# Домашняя работа 3

Используется существующий REST-тест `test_create_patch_get_post` из
[домашней работы 2](https://github.com/Tiipok/mts-homework-2).
Сценарий перенесён без изменений. Общие HTTP-фикстуры и логирование взяты
из той же работы; неиспользуемые фикстуры авторизации и GraphQL исключены.

Тест обращается к публичному API `https://playground.nileslabs.com/api/v1`:

1. `POST /posts`: создаёт свой пост, проверяет `201` и получает динамический `id`.
2. `PATCH /posts/{id}`: меняет заголовок и проверяет, что тело поста сохранилось.
3. `GET /posts/{id}`: проверяет `200`, тот же `id`, новый заголовок и прежнее тело.
4. `DELETE /posts/{id}` в `finally`: удаляет созданный пост и проверяет `200`.


## Сборка и запуск в Docker

Все команды ниже выполняются из корня проекта, где находится `Dockerfile`.

```bash
docker build -t api-test .
docker run --rm api-test
```

`Dockerfile` устанавливает зависимости внутри образа и задаёт `CMD`, поэтому
тест запускается сразу при `docker run`. Результат виден в консоли.
Python 3.13.5 зафиксирован вместе с digest базового образа;
версии pytest и requests сохранены из lab2, а транзитивные зависимости
также зафиксированы в `requirements.txt`.

При успешном тесте ожидаются `1 passed` и код `0`.
При падении проверки или HTTP-запроса ожидаются `1 failed` и код `1`.


## GitHub Actions

Конфигурация: [`.github/workflows/ci.yml`](.github/workflows/ci.yml).
Workflow запускается при каждом `push`, при `pull_request` и вручную
через **Actions -> API test in Docker -> Run workflow**.

[Успешный запуск GitHub Actions от 20.10.2026](https://github.com/Tiipok/mts-homework-3/actions/runs/39438327176/job/118681192576).
Шаги `Build Docker image` и `Run API test in the built image` завершились успешно.

Job выполняет цепочку:

```text
push -> checkout -> docker build -> docker run -> API test -> pipeline result
```


**Корнем GitHub-репозитория должно быть содержимое каталога lab3**:
`Dockerfile`, `requirements.txt`, `pytest.ini`, `tests/` и `.github/`.
Workflow в подкаталоге общего репозитория GitHub не обнаружит.

## Результаты проверки

Локально проверено 20.10.2026:

| Проверка | Результат | Exit code |
| --- | --- | --- |
| Исходный REST-тест из lab2 до контейнеризации | `1 passed in 4.24s` | `0` |
| `python3 -m pytest -v` в lab3 | `1 passed in 2.28s` | `0` |
| `docker build --pull -t api-test .` | Образ собран | `0` |
| `docker run --rm api-test` | `1 passed in 1.29s` | `0` |
| `docker run --rm --network none api-test` | Ожидаемое падение: `1 failed in 0.10s` | `1` |

Дополнительно проверено, что `/app` в образе содержит только `requirements.txt`,
`pytest.ini` и два Python-файла из `tests/`, а версии установленных зависимостей
совпадают с `requirements.txt`.

Фактические результаты: [локальный JUnit XML](evidence/pytest-results.xml),
[вывод успешного контейнерного запуска](evidence/docker-success.txt),
[вывод ожидаемого неуспешного запуска](evidence/docker-failure.txt).
