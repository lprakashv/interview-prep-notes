# Spring Cloud Fundamentals: Intelligent Routing

## Challenges with Individual Services

- Different ports
- Different addresses
- Different APIs and paths

Solution: **"Intelligent Routing"**

## Routing Service

- Via Gateway service (or API Gateway)
- Single point of entry for all clients

## Gateway Service

Provides:

- Dynamic routing and delivery
- Security and Filtering
- Auditing and Logging
- Request Enhancement
- Load balancing
- Different APIs for different clients

## Netflix Zuul

Zuul is a gateway service that provides dynamic routing, monitoring, resiliency, security and more.

### Using Spring Cloud + Netflix Zuul Reverse Proxy

Application.java

```java
@SpringBootApplication
@EnableZuulProxy
public class Application {

  public static void main(String[] args) {
    SpringBootApplication.run(Application.class, args);
  }
}
```

### Using Spring Cloud + Netflix Zuul with Service Discovery

application.properties

```properties
spring.application.name=gateway-service
eureka.client.service-url.defaultZone=http://localhost:8761/eureka
```

application.yml

```yml
spring:
  application:
    name: gateway-service
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
```

### Using Spring Cloud + Netflix Zuul without Service Discovery

application.properties

```properties
spring.application.name=gateway-service
ribbon.eureka.enabled=false
```

application.yml

```yml
spring:
  application:
    name: gateway-service
ribbon:
  eureka:
    enabled: false
```

### Default Route to Service Resolution with Service Discovery

| Request          | maps to Service                      |
|------------------|--------------------------------------|
| `/foo`           | spring.application.name=foo          |
| `/categories/1`  | spring.application.name=categories   |

**NOTEs:**

- *Prefix is stripped by default. Use `zuul.stripPrefixes=false` to disable.*
- *All services are added by default. Use `zuul.ignoredServices=<pattern>` to ignore services.*

