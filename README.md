# Books API + Redis Cache

Книжный каталог с Redis-кешированием и JPA-связями.

## Стек технологий

| Слой | Технологии |
|------|-----------|
| Backend | Java 17, Spring Boot 3.x |
| База данных | PostgreSQL, Spring Data JPA |
| Кеш | Redis, Spring Cache (@Cacheable, @CacheEvict) |
| Прочее | Lombok |

## Как запустить

### 1. Требования
- Java 17+, Maven, PostgreSQL, Redis

### 2. Создать БД

```sql
CREATE DATABASE books_api;
```

### 3. Настроить `application.yaml`

```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/books_api
    username: postgres
    password: ваш_пароль
  redis:
    host: localhost
    port: 6379
  cache:
    redis:
      time-to-live: 600000  # TTL 10 минут
```

### 4. Запустить Redis

```bash
docker run -d -p 6379:6379 redis
```

### 5. Запустить приложение

```bash
mvn spring-boot:run
```

## API

| Метод | Эндпоинт | Описание |
|-------|----------|----------|
| GET | `/api/books` | Все книги |
| GET | `/api/books/{id}` | Книга по ID (кешируется) |
| GET | `/api/books/author/{author}` | Книги по автору |
| GET | `/api/books/category/{name}` | Книги по категории |
| POST | `/api/books` | Создать книгу |
| PUT | `/api/books/{id}` | Обновить (инвалидирует кеш) |
| DELETE | `/api/books/{id}` | Удалить (инвалидирует кеш) |

## Кеширование

- `@Cacheable(value = "bookById", key = "#id")` — кеш на 10 минут по ID
- `@CacheEvict` — автоматическая инвалидация при обновлении и удалении
- При создании книги с новой категорией — `Category` создаётся автоматически

## Запуск тестов

```bash
mvn test
```
