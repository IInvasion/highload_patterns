Паттерн "Circuit Breaker" (аварийный выключатель) описывает подход к повышению надежности в микросервисной архитектуре, предотвращая каскадные сбои между сервисами.

## Контекст

В микросервисной архитектуре сервисы часто взаимодействуют друг с другом для обработки запросов. Если один сервис вызывает другой, существует риск, что вызываемый сервис может быть недоступен или демонстрировать высокую задержку. Это может привести к исчерпанию ресурсов, таких как потоки, в вызывающем сервисе, что делает его неспособным обрабатывать другие запросы.

## Проблема

Как предотвратить сбой сети или сервиса от каскадирования на другие сервисы?

## Решение

Решение заключается в использовании прокси для вызова удаленного сервиса, который функционирует как электрический аварийный выключатель. Когда количество последовательных сбоев превышает заданный порог, аварийный выключатель срабатывает, и на время таймаута все попытки вызвать удаленный сервис завершаются неудачей. После истечения таймаута аварийный выключатель позволяет пройти ограниченному числу тестовых запросов. Если эти запросы успешны, аварийный выключатель восстанавливает нормальную работу; в противном случае таймаут начинается заново.

## Пример

В примере `RegistrationServiceProxy`, написанном на Scala, используется аварийный выключатель для обработки сбоев при вызове удаленного сервиса:

```
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.beans.factory.annotation.Value
import org.springframework.http.HttpStatus
import org.springframework.stereotype.Component
import org.springframework.web.client.HttpClientErrorException
import org.springframework.web.client.RestTemplate
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand

@Component
class RegistrationServiceProxy @Autowired()(restTemplate: RestTemplate) extends RegistrationService {

  @Value("${user_registration_url}")
  var userRegistrationUrl: String = _

  @HystrixCommand(commandProperties = Array(new HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "800")))
  override def registerUser(emailAddress: String, password: String): Either[RegistrationError, String] = {
    try {
      // Выполнение POST-запроса для регистрации пользователя
      val response = restTemplate.postForEntity(
        userRegistrationUrl,
        RegistrationBackendRequest(emailAddress, password),
        classOf[RegistrationBackendResponse]
      )

      // Проверка статуса ответа и возврат результата
      response.getStatusCode match {
        case HttpStatus.OK => Right(response.getBody.id)
      }
    } catch {
      // Обработка конфликта при регистрации
      case e: HttpClientErrorException if e.getStatusCode == HttpStatus.CONFLICT =>
        Left(DuplicateRegistrationError)
    }
  }
}
```

Аварийный выключатель активируется с помощью аннотации `@HystrixCommand`, а функциональность включается с помощью аннотации `@EnableCircuitBreaker` в конфигурационном классе.

## Результирующий контекст

Преимущества паттерна:

- Сервисы могут обрабатывать сбои других сервисов, которые они вызывают.

Недостатки:

- Сложность выбора значений таймаута без создания ложных срабатываний или избыточной задержки.

## Связанные паттерны

- Паттерн "Microservice Chassis" может реализовывать этот паттерн.
- API Gateway будет использовать этот паттерн для вызова сервисов.
- Маршрутизатор серверной службы обнаружения может использовать этот паттерн для вызова сервисов.

Этот паттерн помогает повысить устойчивость системы к сбоям и улучшить управление взаимодействиями между микросервисами.

[Ссылка на источник](https://microservices.io/patterns/reliability/circuit-breaker.html)