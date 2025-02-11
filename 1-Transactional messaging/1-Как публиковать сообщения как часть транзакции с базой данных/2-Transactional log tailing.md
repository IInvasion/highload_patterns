Паттерн "Отслеживание журнала транзакций" (Transaction Log Tailing) предлагает метод для публикации сообщений и событий из базы данных в брокер сообщений, избегая сложностей, связанных с распределенными транзакциями.

## Контекст

Данный паттерн применяется в ситуациях, когда необходимо обеспечить согласованность между изменениями в базе данных и отправкой сообщений. Он актуален после внедрения паттерна "Транзакционный выходной ящик".

## Проблема

Основная задача заключается в том, как эффективно публиковать сообщения и события, которые были добавлены в _выходной ящик_ базы данных, в брокер сообщений.

## Решение

Решение состоит в отслеживании журнала транзакций базы данных и публикации каждого сообщения или события, вставленного в _выходной ящик_, в брокер сообщений. Механизм отслеживания зависит от используемой базы данных, например:

- **MySQL**: использование бинарного лога (binlog)
- **Postgres**: использование журналов изменений (WAL)
- **AWS DynamoDB**: использование потоков таблиц

## Преимущества

- Исключение необходимости использования 2PC (двухфазного коммита).
- Гарантия точности отправки сообщений.

## Недостатки

- Паттерн может быть относительно сложным для понимания, хотя становится все более распространенным.
- Требует специфических решений для каждой базы данных.
- Сложности с избежанием дублирования публикаций.

## Связанные паттерны

- Паттерн "Транзакционный выходной ящик" создает необходимость в использовании данного паттерна.
- Паттерн "Публикация с опросом" является альтернативным решением.

Этот паттерн позволяет эффективно управлять отправкой сообщений между сервисами, обеспечивая надежность и согласованность данных.

[Ссылка на источник](https://microservices.io/patterns/data/transaction-log-tailing.html)