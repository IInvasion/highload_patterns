Паттерн "Distributed Tracing" описывает подход к отслеживанию запросов в микросервисной архитектуре, что позволяет анализировать поведение приложения и устранять проблемы.

## Контекст

В микросервисной архитектуре запросы часто охватывают несколько сервисов. Каждый сервис обрабатывает запрос, выполняя различные операции, такие как запросы к базе данных или публикация сообщений.

## Проблема

Как понять поведение приложения и устранять возникающие проблемы?

## Силы

- Внешний мониторинг предоставляет лишь общую информацию о времени отклика и количестве вызовов, не давая понимания о конкретных операциях.
- Решение должно иметь минимальные накладные расходы во время выполнения.
- Записи логов для одного запроса разбросаны по множеству логов.

## Решение

Необходимо инструментировать сервисы следующим образом:

- Присваивать каждому внешнему запросу уникальный идентификатор.
- Передавать этот идентификатор всем сервисам, участвующим в обработке запроса.
- Включать идентификатор в каждое сообщение лога.
- Записывать информацию (например, время начала и окончания) о запросах и операциях в централизованном сервисе.

Эта инструментализация может быть частью функциональности, предоставляемой фреймворком микросервисного шасси.

## Примеры

Примером приложения, использующего распределенное отслеживание, является **Microservices Example**, написанное на Scala с использованием Spring Boot и Spring Cloud. Оно использует Spring Cloud Sleuth для сбора информации о трассировке и отправки ее на сервер Zipkin.

## Конфигурация зависимостей в `build.gradle`:

```
dependencies {
    compile "org.springframework.cloud:spring-cloud-sleuth-stream"
    compile "org.springframework.cloud:spring-cloud-starter-sleuth"
    compile "org.springframework.cloud:spring-cloud-stream-binder-rabbit"
}
```

## Пример конфигурации `docker-compose.yml`:

```
environment:
  SPRING_RABBITMQ_HOST: rabbitmq
  SPRING_SLEUTH_ENABLED: "true"
  SPRING_SLEUTH_SAMPLER_PERCENTAGE: 1
  SPRING_SLEUTH_WEB_SKIPPATTERN: "/api-docs.*|/autoconfig|/configprops|/dump|/health|/info|/metrics.*|/mappings|/trace|/swagger.*|.*\\.png|.*\\.css|.*\\.js|/favicon.ico|/hystrix.stream"

```

## Пример сервера Zipkin:

```
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.zipkin.server.EnableZipkinServer;

@SpringBootApplication
@EnableZipkinServer
public class ZipkinServer {

    public static void main(String[] args) {
        // Launch the Zipkin server
        SpringApplication.run(ZipkinServer.class, args);
    }
}
```

Сервер Zipkin развертывается с использованием Docker.

## Результирующий контекст

Преимущества паттерна:

- Обеспечивает полезное понимание поведения системы, включая источники задержек.
- Позволяет разработчикам видеть, как обрабатывается отдельный запрос, путем поиска по агрегированным логам с использованием его идентификатора.

Недостатки:

- Агрегация и хранение трассировок могут требовать значительной инфраструктуры.

## Связанные паттерны

- Паттерн "Log Aggregation" включает внешний идентификатор запроса в каждое сообщение лога.

Этот паттерн помогает улучшить наблюдаемость системы, позволяя разработчикам более эффективно управлять производительностью и состоянием микросервисного приложения.

[Ссылка на источник](https://microservices.io/patterns/observability/distributed-tracing.html)