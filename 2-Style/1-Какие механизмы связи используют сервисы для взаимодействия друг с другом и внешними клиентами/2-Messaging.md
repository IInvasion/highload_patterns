Паттерн "Сообщения" (Messaging) описывает способ коммуникации между сервисами в микросервисной архитектуре, позволяя им обмениваться сообщениями асинхронно.

## Контекст

В микросервисной архитектуре сервисы должны обрабатывать запросы от клиентов приложения и часто сотрудничают для выполнения этих запросов. Для этого они используют протоколы межпроцессного взаимодействия.

## Проблема

Основная задача заключается в том, как сервисы в микросервисной архитектуре могут эффективно общаться друг с другом.

## Решение

Решение состоит в использовании асинхронной передачи сообщений для межсервисной коммуникации. Сервисы обмениваются сообщениями через каналы сообщений. Существует несколько стилей асинхронной коммуникации:

- **Запрос/ответ**: сервис отправляет запрос и ожидает ответ.
- **Уведомления**: отправитель отправляет сообщение, не ожидая ответа.
- **Запрос/асинхронный ответ**: сервис отправляет запрос и ожидает ответ в будущем.
- **Публикация/подписка**: сервис публикует сообщение для одного или нескольких получателей.
- **Публикация/асинхронный ответ**: сервис публикует запрос, некоторые получатели отправляют ответ.

## Пример

В приложении FTGO `OrderService` публикует событие `Order Created` при создании заказа:

```
import java.util.List;

public class OrderService {

    private final OrderAggregateEventPublisher orderAggregateEventPublisher;

    // Конструктор для внедрения зависимости
    public OrderService(OrderAggregateEventPublisher orderAggregateEventPublisher) {
        this.orderAggregateEventPublisher = orderAggregateEventPublisher;
    }

    public Order createOrder(long consumerId, long restaurantId, List<MenuItemIdAndQuantity> lineItems) {
        // Логика создания заказа
        Order order = new Order(consumerId, restaurantId, lineItems);
        
        // Публикация событий заказа
        orderAggregateEventPublisher.publish(order, order.getEvents());
        
        // Дополнительная логика (например, сохранение заказа в базе данных)
        
        return order; // Возврат созданного заказа
    }
}
```

## Результирующий контекст

**Преимущества:**

- **Слабая связь**: разъединяет отправителя сообщения и потребителя.
- **Повышенная доступность**: брокер сообщений может буферизовать сообщения до тех пор, пока потребитель не сможет их обработать.
- Поддержка различных паттернов коммуникации.

**Недостатки:**

- Дополнительная сложность из-за необходимости наличия высокодоступного брокера сообщений.
- Запрос/ответ требует более сложной реализации.

## Связанные паттерны

- Паттерн "Сага" и CQRS используют сообщения.
- Паттерн "Транзакционный выходной ящик" позволяет отправлять сообщения как часть транзакции базы данных.
- Паттерн "Внешняя конфигурация" обеспечивает логические имена каналов сообщений и местоположение брокера сообщений.

Этот паттерн обеспечивает эффективное взаимодействие между сервисами в микросервисной архитектуре, позволяя им работать асинхронно и независимо друг от друга.

[Ссылка на источник](https://microservices.io/patterns/communication-style/messaging.html)