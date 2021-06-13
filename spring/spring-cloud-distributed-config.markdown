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

