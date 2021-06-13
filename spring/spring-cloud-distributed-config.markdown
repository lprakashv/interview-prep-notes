# Spring Cloud Fundamentals: Distributed Config

Configuration in a non-distributed to distributed explodes really rapidly, from a handlful of configuration files to many-many ones.

We can still live with configuration management tools like Puppet, Chef etc. But there are issues with typical configuration management:

- **Deployment oriented:** Need to kick-off deployments even for small or temporary changes.
- **Push-based is usally not dynamic enough:** Won't work in cloud environment, as we need to keep track of all the newly spun up instances and push to those as well.
- **Pull-based adds latency with temporal polling:** Usually works on polling mechanism, which adds time-interval latency.

**Configuration Server** can solve our problems!

## Application Configuration Server

- Dedicated, dynamic and centralized key/value store (maybe distributed).
- Authoritative source.
- Auditing.
- Versioning.
- Cryptography support.

Options available for configuration management backend:

1. Spring Cloud Consul
2. Spring Cloud Zookeeper
3. **Spring Cloud Config**

## Integration with Spring Applications

**Config Client:**

- Embedded in application
- Spring **`Environment`** abstraction:

```java
@Inject
Environment environment;
```

**Config Server:**

- Standalone (can be embedded)
- Spring **`PropertySource`** abstraction

```properties
classpath:file.properties
```

## Spring Cloud Config: Config Server

- HTTP REST Access
- Output formats
  - JSON (default)
  - Properties
  - YAML
- Backend Stores
  - Git (default)
  - SVN
  - File system
- Configuration scopes

### Using Spring Cloud Config Server

pom.xml

```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-dependencies</artifactId>
      <version>Camden-SR2</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```

pom.xml

```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-config-server</artifactId>
</dependency>
```

application.properties

```properties
server.port=8888
spring.cloud.config.server.git.uri=<uri_to_git_repo>
```

application.yml

```yml
server:
  port: 8888
spring:
  cloud:
    config:
      server:
        git:
          uri: <uri_to_git_repo>
```

Application.java

```java
@SpringBootApplication
@EnableConfigServer
public class Application {

  public static void main(String[] args) {
    SpringApplication.run(Application.class, args);
  }
}
```

**TIP:** Add Eureka client dependencies, service-url configuration and `@EnableDiscoveryClient` to make the config server discoverable.

**Configuring backend store:**

1. Create a folder to store configuration.
2. (optional) Add a `properties` or `yml` file with name **`application`**.
3. Add `properties` or `yml` file with name **`{application}-{profile}`**.
4. `git init`.
5. `git add` and `git commit`.
6. (optional) Setup remote git repository and `git push`.

**Additional Notes:**

- Git is the default mechanism/backend.
- Global configs goes into `application.yml`/`application.properties` and application specific config go into the named files as above.

**NOTE:** Don't forget to secure your Config Server!

- Easy to configure Spring Security.

### REST Endpoint Parameters

- **{application}** - maps to **`spring.application.name`** on **client**.
- **{profile}** - maps to **`spring.profiles.active`** on **client**.
- **{label}** - server side feature to refer to set of config files by name.

#### GET /{application}/{profile}[/{label}]

- `/myapp/dev/master`
- `/myapp/prod/v2`
- `/myapp/default`

#### GET /{application}-{profile}.(yml | properties)

- `/myapp-dev.yml`
- `/myapp-prod.properties`
- `/myapp-default.properties`

#### GET /{label}/{application}-{profile}.{yml | properties}

- `/master/myapp-dev.yml`
- `/v2/myapp-prod.properties`
- `/master/myapp-default.properties`

## Spring Cloud Config: Config Client

### Spring Config Client

pom.xml

```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

#### Config First

Specify the location of the config server.

**bootstrap.properties**:

```properties
spring.application.name=<your_application_name>
spring.cloud.config.discovery.enabled=true
```

**bootstrap.yml**:

```yml
spring:
  application:
    name: <your_application_name>
  cloud:
    config:
      discovery:
        enabled: true
```

#### Discovery First

Discover the location of the config server.

**bootstrap.properties**:

```properties
spring.application.name=<your_application_name>
spring.cloud.config.uri=http://localhost:8888/
```

**bootstrap.yml**:

```yml
spring:
  application:
    name: <your_application_name>
  cloud:
    config:
      uri: http://localhost:8888/
```

**NOTE:** Don't forget to add eureka client dependencies, service-url configuration and `@EnableDiscoveryClient`

### Refreshing configuration properties

#### \#1: Configuration changes

- Edit and save configuration file(s).
- Commit and/or push changes to a VCS.

#### \#2: Notify Application(s) to refresh configuration

- **`/refresh`** with **`spring-boot-actuator`**.
  - Explicit refresh.
- **`/bus/refresh`** with **`spring-cloud-bus`**.
  - Dynamic push refresh.
  - Automatic via Spring Cloud Bus broadcasting.
  - From RabbitMQ or Kafka?
- VCS + `/monitor` with **`spring-cloud-config-monitor`** and **`spring-cloud-bus`**.
  - Automatic and Smart refresh.
  - Via post commit hooks, Spring Cloud Config Monitor and Spring Cloud Config Bus broadcasting.

### Refreshing Config: What's covered and what's not

- [x] **`@ConfigurationProperties`**
- [x] All logging levels defined by **`logging.level.*`** are updated.
- [ ] Any **`@Bean`** or **`@Value`** that only gets its configuration upon initialization.

#### `@RefreshScope`

To refresh a `@Bean` or a `@Value` that only gets its configuration upon initialization.

```java
@Configuration
public class SomeConfiguration {
  @Bean
  @RefreshScope
  public FooService fooService(FooProperties properties) {
    return new FooService(properties.getConfigValue());
  }
}
```

### Additional Features Supported

- Encrypted configuration at rest and/or in-flight.
- An `/encrypt` endpoint to encrypt configuration.
- A `/decrypt` endpoint to decrypt configuration.
- Encrypting and decrypting with symmetric or asymmetric keys.

#### What does encrypted configuration look like?

application.properties

```properties
my.datasource.username=foobar
my.datasource.password={cypher}AASFDSFJSRFAFESECCSE
```

application.yml

```yml
my:
  datasource:
    usernmae: foobar
    password: '{cipher}AASFDSFJSRFAFESECCSE'
```

#### At what point the configuration is decrypted?

- Upon request at the server (default).
- Locally at the client on response.

Configure the Config Server with: `spring.cloud.config.server.encrypt.enabled=false`

TODO: encryption and decryption setup with examples.
