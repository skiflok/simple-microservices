# product-service

REST APP

v0.0.1 init project

* Модели, репозитории и контроллер для Product
* dto response and request
* Docker compose для MongoDB

v0.0.2 tests

* [Testcontainers](https://testcontainers.com/guides/getting-started-with-testcontainers-for-java/)
  для тестов

v0.0.3 order service

v0.0.4 inventory service discovery service

* добавлен сервер инвентаризации, осуществляющий проверку наличия товара на складе
* добавлен discovery service на базе netflix-eureka-server. позволяет службам находить друг друга и
  взаимодействовать друг с другом без жесткого кодирования имени хоста и порта

v0.0.5 api-gateway

* [Устаревшая проблема Spring Security](https://stackoverflow.com/questions/76339307/spring-security-deprecated-issue)
* [Get started with Keycloak on Docker](https://www.keycloak.org/getting-started/getting-started-docker)
* `docker run -p 8181:8080 -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=admin quay.io/keycloak/keycloak:22.0.5 start-dev`
* spring-boot-microservices-realm
* Create client
* spring-cloud-client
* Client authentication ON
* [x] Service accounts roles
* [ ] Standard flow
* [ ] Direct access grants
* Realm Setting -> openid-configuration

spring.security.oauth2.resourceserver.jwt.issuer-uri=http://localhost:8181/realms/spring-boot-microservices-realm

postman authorization

* authorization -> Type Oauth 2.0
* Grant Type - Client Credentials
* Access Token URL
  -> http://localhost:8181/realms/spring-boot-microservices-realm/.well-known/openid-configuration
  ->
  "
  token_endpoint":"http://localhost:8181/realms/spring-boot-microservices-realm/protocol/openid-connect/token"
* Client ID -> spring-cloud-client
* Client Secret -> Clients -> spring-cloud-client -> Credentials -> Client secret

postman test

create product
POST http://localhost:8080/api/product

```json
{
  "name": "Iphone 14",
  "description": "iphone 14",
  "price": 1400
}
```

GET http://localhost:8080/api/product

mongo DB http://localhost:28081/

POST http://localhost:8080/api/order/

```json
{
  "orderLinesItemsDtoList": [
    {
      "scuCode": "iphone_13",
      "price": "1200",
      "quantity": 1
    },
    {
      "scuCode": "iphone_13_red",
      "price": "1200",
      "quantity": 2
    }
  ]
}
```

доступ к eureka
http://localhost:8080/eyreka/web
http://localhost:8761/

v0.0.6 Circuit Breaker Resilience4J

[Spring Cloud Circuit Breaker](https://spring.io/projects/spring-cloud-circuitbreaker)

[Resilience4J](https://github.com/resilience4j/resilience4j)

<details>
<summary><strong>Circuit Breaker описание</strong></summary>

Spring Cloud Circuit Breaker - это часть проекта Spring Cloud, предназначенная для обработки
ситуаций, когда одна из служб в вашем микросервисном приложении становится нестабильной или
недоступной. Это помогает в создании устойчивых и отказоустойчивых систем.

<p>Как это работает:</p>
<ol>
<li>
Circuit Breaker (Защитный выключатель): Circuit Breaker - это паттерн проектирования, который позволяет системе изолировать компонент или службу, когда она становится нестабильной. Вместо того, чтобы давать запросы к нестабильной службе, Circuit Breaker блокирует доступ к этой службе и предоставляет альтернативные данные или возвращает ошибку без вызова нестабильной службы.
</li>
<li>
Fallback (Резервный вариант): Если Circuit Breaker обнаруживает, что служба недоступна, он может использовать резервный вариант - заранее определенный ответ или логику, которая возвращает данные пользователю вместо результата вызова недоступной службы.
</li>
<li>
Мониторинг и восстановление: Circuit Breaker также обычно включает мониторинг. Если служба
восстанавливается и становится стабильной снова, Circuit Breaker может автоматически восстановить
доступ к ней.
</li>
</ol>

<p>Почему это важно:</p>
<lo>
<li>
Повышение устойчивости: Circuit Breaker предотвращает перегрузку нестабильных служб, предотвращая таким образом сбои в системе из-за одной нестабильной части.
</li>
<li>
Быстрое восстановление: Путем блокировки недоступной службы и использования резервных вариантов, система может продолжать работать даже при временных сбоях в других службах.
</li>
<li>
Предсказуемость поведения: Circuit Breaker предоставляет предсказуемое поведение при сбоях, позволяя приложению более гибко реагировать на проблемы с доступностью.
</li>
</lo>

<p>
Spring Cloud Circuit Breaker предоставляет абстрактный уровень для работы с различными реализациями Circuit Breaker, такими как Netflix Hystrix, Resilience4j и Spring Retry. Он упрощает конфигурацию и интеграцию Circuit Breaker в вашем микросервисном приложении, облегчая обработку ошибок и обеспечивая устойчивость системы.
</p>

</details>

[Examples of resilience4j](https://resilience4j.readme.io/docs/getting-started-3)

* для теста поднять ордер сервис на 8081
* http://localhost:8081/actuator/health
* остановить серсис инвентаризации
* сделать 5 запросов http://localhost:8080/api/order/
* resilience4j.timelimiter.instances.inventory.timeout-duration=3s
* устанавливает таймаут ожидание запроса
* resilience4j.retry.instances.inventory.max-attempts=3
* resilience4j.retry.instances.inventory.wait-duration=5s
* устанавливает количество попыток ретрая запроса интервал

проверить статистику
http://localhost:8081/actuator
http://localhost:8081/actuator/retryevents
http://localhost:8081/actuator/timelimiterevents

v0.0.7 Distributed Tracing Распределенная тарссировка

[Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth)

[zipkin](https://zipkin.io/pages/quickstart.html)

`docker run -d -p 9411:9411 openzipkin/zipkin`

v0.0.8 Event Driven Architecture using Kafka

[Apache Kafka® Quick Start](https://developer.confluent.io/quickstart/kafka-local/)

[Быстрый старт для Confluent Platform](https://docs.confluent.io/platform/current/platform-quickstart.html#ce-docker-quickstart)
