Паттерн "Удаленный вызов процедур" (Remote Procedure Invocation, RPI) описывает способ коммуникации между сервисами в микросервисной архитектуре, позволяя им обрабатывать запросы от клиентов и взаимодействовать друг с другом.

## Контекст

В микросервисной архитектуре сервисы должны обрабатывать запросы от клиентов приложения и иногда сотрудничать для их выполнения. Для этого они используют протоколы межпроцессного взаимодействия.

## Проблема

Основная задача заключается в том, как сервисы в микросервисной архитектуре могут эффективно общаться друг с другом.

## Решение

Решение состоит в использовании RPI для межсервисной коммуникации. Клиент использует протокол запроса/ответа для отправки запросов к сервису.

## Примеры

Одним из примеров технологий RPI является `RegistrationServiceProxy`, написанный на Scala, который делает REST-запросы с использованием `RestTemplate` из Spring Framework.

```
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.beans.factory.annotation.Value
import org.springframework.http.HttpStatus
import org.springframework.stereotype.Component
import org.springframework.web.client.HttpClientErrorException
import org.springframework.web.client.RestTemplate
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand

@Component
class RegistrationServiceProxy @Autowired() (restTemplate: RestTemplate) extends RegistrationService {

  @Value("${user_registration_url}")
  var userRegistrationUrl: String = _

  @HystrixCommand(commandProperties = Array(new HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "800")))
  override def registerUser(emailAddress: String, password: String): Either[RegistrationError, String] = {
    try {
      val response = restTemplate.postForEntity(
        userRegistrationUrl,
        RegistrationBackendRequest(emailAddress, password),
        classOf[RegistrationBackendResponse]
      )

      response.getStatusCode match {
        case HttpStatus.OK => Right(response.getBody.id)
      }
    } catch {
      case e: HttpClientErrorException if e.getStatusCode == HttpStatus.CONFLICT =>
        Left(DuplicateRegistrationError)
    }
  }
}
```

## Результирующий контекст

**Преимущества:**

- Простота и знакомство: использование запроса/ответа легко воспринимается.
- Отсутствие промежуточного брокера упрощает систему.

**Недостатки:**

- Обычно поддерживает только паттерн запроса/ответа, не позволяя использовать другие взаимодействия, такие как уведомления или асинхронные ответы.
- Сниженная доступность, так как клиент и сервис должны быть доступны на протяжении всего взаимодействия.

**Проблемы:**

- Клиенту необходимо обнаруживать местоположение экземпляров сервиса.

## Связанные паттерны

- Паттерн "Специфичный для домена протокол" является альтернативным решением.
- Паттерн "Сообщения" также представляет собой альтернативный подход.
- Конфигурация с внешними источниками предоставляет логическое сетевое местоположение сервиса.
- Клиент использует либо клиентское, либо серверное обнаружение для нахождения экземпляра сервиса.
- Паттерн "Цепочка разрывов" может использоваться для повышения надежности.

Этот паттерн обеспечивает эффективное взаимодействие между сервисами в микросервисной архитектуре, однако требует внимательного подхода к управлению доступностью и обнаружению сервисов.

[Ссылка на источник](https://microservices.io/patterns/communication-style/rpi.html)