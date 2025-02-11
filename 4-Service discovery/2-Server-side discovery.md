Паттерн "Server-Side Discovery" описывает подход к организации обнаружения микросервисов, при котором клиент не взаимодействует напрямую с сервисами, а использует сервер для маршрутизации запросов.

## Контекст

В микросервисной архитектуре приложения состоят из множества сервисов, которые могут динамически изменять свои местоположения. Это создает необходимость в механизме, позволяющем клиентам находить и взаимодействовать с доступными экземплярами сервисов.

## Проблема

Клиенты сталкиваются с несколькими трудностями:

- **Необходимость в маршрутизации**: Клиенты должны знать, как направлять запросы к различным сервисам.
- **Изменяющиеся адреса сервисов**: Местоположение экземпляров может изменяться, что усложняет взаимодействие.
- **Сложность управления трафиком**: Без централизованного подхода управление запросами становится громоздким.

## Решение

Решение заключается в использовании паттерна серверной службы обнаружения. В этом случае клиент отправляет запросы на сервер (например, API Gateway или балансировщик нагрузки), который затем определяет, к какому экземпляру сервиса перенаправить запрос. Сервер управляет списком доступных сервисов и их адресами.

## Преимущества Server-Side Discovery:

- **Скрытие сложности**: Клиенты не должны знать о внутренней структуре системы.
- **Централизованное управление**: Упрощает управление изменениями и обновлениями адресов сервисов.
- **Оптимизация производительности**: Сервер может выполнять кэширование и балансировку нагрузки.

## Недостатки:

- **Дополнительная задержка**: Каждый запрос проходит через дополнительный уровень, что может увеличить время отклика.
- **Увеличение сложности инфраструктуры**: Необходимо поддерживать сервер для маршрутизации и управления трафиком.

## Связанные паттерны

- Паттерн "Client-Side Discovery" предлагает альтернативный подход, при котором клиенты сами находят экземпляры сервисов.
- Паттерн "API Gateway" часто используется для реализации серверной службы обнаружения, обеспечивая единый входной пункт для всех запросов.

Этот паттерн позволяет эффективно управлять взаимодействием между клиентами и микросервисами, обеспечивая простоту и гибкость при доступе к различным функциональным возможностям системы.

[Ссылка на источник](https://microservices.io/patterns/server-side-discovery.html)