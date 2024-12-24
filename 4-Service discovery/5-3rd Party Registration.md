Паттерн "3rd Party Registration" описывает подход, при котором экземпляры сервисов регистрируются и отменяют регистрацию в реестре сервисов с помощью стороннего регистратора.

## Контекст

При использовании паттернов клиентской или серверной службы обнаружения экземпляры сервисов должны быть зарегистрированы в реестре при запуске и отменены при завершении работы, чтобы их можно было обнаружить.

## Проблема

Как экземпляры сервисов могут быть зарегистрированы и отменены в реестре сервисов?

## Силы

- Экземпляры сервисов должны регистрироваться в реестре на старте и отменять регистрацию при завершении работы.
- Экземпляры, которые аварийно завершают работу, должны автоматически исключаться из реестра.
- Экземпляры, которые работают, но не могут обрабатывать запросы, также должны быть исключены.

## Решение

Сторонний регистратор отвечает за регистрацию и отмену регистрации экземпляра сервиса в реестре. При запуске экземпляр сервиса сообщает регистратору о своем старте, и тот регистрирует его в реестре. При завершении работы экземпляр сообщает регистратору, который отменяет его регистрацию.

## Примеры

Примеры паттерна 3rd Party Registration включают:

- **Netflix Prana** — приложение "сайдкар", которое работает вместе с не-JVM приложением и регистрирует его в Eureka.
- **AWS Autoscaling Groups** автоматически (аннулирует) регистрацию экземпляров EC2 с Elastic Load Balancer.
- **Joyent’s Container Buddy** работает в контейнере Docker как родительский процесс для сервиса и регистрирует его в реестре.
- **Registrator** — регистрирует и аннулирует регистрацию контейнеров Docker с различными реестрами сервисов.
- Кластерные фреймворки, такие как **Kubernetes** и **Marathon**, (аннулируют) регистрацию экземпляров сервисов с встроенным/неявным реестром.

## Результирующий контекст

Преимущества паттерна 3rd Party Registration:

- Код сервиса становится менее сложным по сравнению с паттерном саморегистрации, так как он не отвечает за свою регистрацию.
- Регистратор может выполнять проверки состояния экземпляра сервиса и регистрировать/аннулировать его на основе этих проверок.

Недостатки:

- Сторонний регистратор может иметь лишь поверхностное представление о состоянии экземпляра сервиса (например, RUNNING или NOT RUNNING) и может не знать, способен ли он обрабатывать запросы. Однако некоторые регистраторы, такие как Netflix Prana, выполняют проверки состояния для определения доступности экземпляра.
- Если регистратор не является частью инфраструктуры, это еще один компонент, который необходимо установить, настроить и поддерживать. Кроме того, поскольку это критически важный компонент системы, он должен быть высокодоступным.

## Связанные паттерны

- Паттерн "Service Registry".
- Паттерн "Client Side Discovery".
- Паттерн "Server Side Discovery".
- Паттерн "Self Registration" как альтернативное решение.

Этот паттерн позволяет упростить управление регистрацией сервисов, делая код более чистым и поддерживаемым.

[Ссылка на источник](https://microservices.io/patterns/3rd-party-registration.html)