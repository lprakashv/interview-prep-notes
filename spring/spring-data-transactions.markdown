# Data Transactions with Spring

## Transaction

Multiple actions performed as a single group

### ACID Transactions

- **A (Atomicity):** All or nothing (no partial).
- **C (Consistency):** Committed (written) when all actions (in a transaction) are completed.
- **I (Isolation):** Each transaction is isolated from other transactions, meaning uncorrupted data.
- **D (Durability):** Cannot be deleted/undone by a system failure.

### Transaction Types

- **Global:** Multiple resources manage the transaction. Usually managed by the web server.
- **Local:** One resource manages the transaction. e.g., JDBC connection.

## Spring Framework

Consistent programming model across global and local transactions.

### Spring Transaction management types

#### Programmatic

- Custom code for transaction management.

Example:

```java
public void saveTicket(Ticket ticket) {
  Session session = SessionFactory.getCurrentSession();
  session.getTransaction().begin();   //--
  session.save(ticket);               //  | <=== transaction
  session.getTransaction().end();     //--
}
```

#### Declarative

- Manages transaction using Spring-specific annotations.
- Separates transaction management from business code.

Example:

```java
@Transactional
public void scheduleRelease(Ticket ticket, Release release) {
  ticketDao.saveTicket(ticket);
  assignToRelease(ticket, release);
  doOtherStuff(ticket);
}
```

Manages everything:

1. Begin
2. Suspend
3. Commit
4. Rollback
5. Transactional parameters

### Transaction Managers

**Programmatic Transaction Management:**

1. **Transaction template:** Similar to Spring templates like `JdbcTemplate` and other available templates.
2. **Platform transaction manager:** Handles transactions across Hibernate, JDBC, JPA, JMS, etc.

**Spring Transaction Managers:**

1. Platform transaction manager
2. JTA
3. Hibernate
4. DataSource
5. **JPA**

### `@Transactional` annotation

- Proxy is created to hold transaction management code.
- Annotation used at the class, interface or method level.
- Transaction propagation is handled automatically.

**NOTE:** In Spring boot, it works without configuration.

### Spring Configuration

- JPA Transaction Manager: Registers a transaction manager for us.
- Additional configuration: Datasource, entity manager, repositories.
- Spring Data Repositories.

### Lifecycle and Scope

#### Database Transaction

**`@Transactional`** defines a single transaction, in the scope of a **persistence context**.

#### Persistence Context

- Defined in **JPA**
- Handles a set of entities that contain data to be persisted.

### How `@Transactional` works?

Via proxies!

**Without proxy implementation:**

![spring-proxy](./without-proxy.png)

**With Proxy implementation:**

![spring-proxy](./with-proxy.png)

#### Proxy

- **Transaction Interceptor:** Intercepts method calls.
- **Platform transaction manager:** Handles transactions.

Difference proxies in Spring (JPA?) transaction handling:

1. **Persistence context proxy**
2. **Entity manager proxy**
3. **Transaction aspect**
4. **Transaction manager**

### Rollbacks

Rollbacks will occur for `RuntimeException` or unchecked-exception only.

- Use **`@Transactional(rollbackFor=Exception.class)`** to state otherwise.
- Use **`@Transactional(noRollbackFor=SpecificException.class)`** to avoid rollback on a specific exception.

Rollback will happen in case of:

1. Throw an exception from code.
2. Spring catches an unhandled exception.
3. Spring determines to mark the transaction for "rollback".

**NOTE:**

To enable the transaction logging from Spring, set: `logging.level.org.springframework.transaction.interceptor=TRACE`

### Transaction management code

Configuration:

```java
private final TransactionTemplate transactionTemplate;

// constructor
public ReleaseService(PlatformTransactionManager transactionManager) {
  this.transactionTemplate = new TransactionTemplate(transactionManager);
  this.transactionTemplate.setPropagationBehaviorName("PROPAGATION_REQUIRES_NEW");
  this.transactionTemplate.setReadOnly(true);
}
```

A simple 2 step transaction with the above configuration:

```java
transactionTemplate.execute(new TransactionCallbackWithoutResult() {
  public void doInTransactionWithoutResult(TransactionStatus status) {
    try {

    } catch (NoSuchElementException exception) {
      exception.printStackTrace();
      status.setRollbackOnly();
    }
  }
});
```

Fine-grained control in programmatic transaction management, using platform transaction manager directly.

```java
public Release scheduleRelease(Release release) {
  TransactionDefinition transactionDefinition = new DefaultTransactionDefinition();
  TransactionStatus transactionStatus = transactionManager.getTransaction(transactionDefinition);

  try {
    createTickets(release);
    assignTicketsToRelease(release.getId(), release.getTickets());

    transactionManager.commit(transactionStatus);
  } catch (RuntimeException e) {
    transactionManager.rollback(transactionStatus);
    throw e;
  }

  return release;
}
```

#### Declarative transaction management (vs Programmatic)

- Manage transaction via configuration.
- Separate transaction logic from business logic.
- Easy to maintain.
- Preferred when a lot of transaction logic.
