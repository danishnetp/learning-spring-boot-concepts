# Spring, Spring Boot, Transactions, and REST API Interview Questions and Answers

This document contains a practical and current set of interview questions and answers for **Spring**, **Spring Boot**, **major Spring modules**, **transaction management**, and **REST APIs**. It is designed for quick revision as well as deeper interview preparation.

---

## Table of Contents

1. [Spring Core](#spring-core)
2. [Spring Boot](#spring-boot)
3. [Spring Modules](#spring-modules)
4. [Transactions](#transactions)
5. [REST API](#rest-api)
6. [Code Examples](#code-examples)

---

## Spring Core

### 1. What is Spring Framework?

**Answer:**
Spring Framework is a comprehensive Java framework used to build enterprise applications. Its main goals are:

- reduce tight coupling between components
- simplify application development
- support testability and maintainability
- provide infrastructure support for web, data access, security, messaging, and more

Spring is based heavily on:

- **IoC (Inversion of Control)**
- **DI (Dependency Injection)**
- **AOP (Aspect-Oriented Programming)**

**Example:**
Instead of creating dependencies manually using `new`, Spring creates and injects them.

```java
@Service
public class OrderService {

	private final PaymentService paymentService;

	public OrderService(PaymentService paymentService) {
		this.paymentService = paymentService;
	}
}
```

---

### 2. What is IoC in Spring?

**Answer:**
IoC means the control of object creation and dependency management is moved from the application code to the Spring container.

In a traditional approach, a class creates its own dependent objects. In Spring, the container manages those objects and injects them where needed.

**Benefits:**

- loose coupling
- easy testing
- centralized configuration
- better maintainability

---

### 3. What is Dependency Injection?

**Answer:**
Dependency Injection is a design pattern where required dependencies are supplied to a class externally instead of the class creating them.

**Types of DI in Spring:**

1. Constructor injection - recommended
2. Setter injection
3. Field injection - generally discouraged in production code

**Why constructor injection is preferred:**

- makes dependencies mandatory
- easier to test
- supports immutability
- avoids hidden dependencies

**Example:**

```java
@Component
public class EmailService {
	public void send(String message) {
		System.out.println("Sending: " + message);
	}
}

@Service
public class NotificationService {
	private final EmailService emailService;

	public NotificationService(EmailService emailService) {
		this.emailService = emailService;
	}
}
```

---

### 4. What is a Spring Bean?

**Answer:**
A Spring Bean is an object that is created, managed, configured, and destroyed by the Spring container.

Beans can be defined using:

- `@Component`
- `@Service`
- `@Repository`
- `@Controller`
- `@Bean` inside a `@Configuration` class

**Example:**

```java
@Configuration
public class AppConfig {

	@Bean
	public RestTemplate restTemplate() {
		return new RestTemplate();
	}
}
```

---

### 5. What are Bean scopes in Spring?

**Answer:**
Common bean scopes are:

- `singleton` - one instance per Spring container
- `prototype` - new instance every time requested
- `request` - one instance per HTTP request
- `session` - one instance per HTTP session
- `application` - one instance per servlet context

**Most commonly used:** `singleton`

**Example:**

```java
@Component
@Scope("prototype")
public class ReportGenerator {
}
```

---

### 6. What is the difference between `@Component`, `@Service`, `@Repository`, and `@Controller`?

**Answer:**
All of them are stereotype annotations and register a bean in the Spring container, but each gives semantic meaning.

- `@Component` - generic Spring-managed component
- `@Service` - business logic layer
- `@Repository` - persistence layer; also helps exception translation
- `@Controller` - web MVC controller
- `@RestController` - controller + `@ResponseBody`

This improves readability and architecture clarity.

---

### 7. What is `ApplicationContext`?

**Answer:**
`ApplicationContext` is the core Spring container responsible for:

- bean creation
- dependency injection
- lifecycle management
- event handling
- property resolution

It is more advanced than `BeanFactory` and is the preferred container in real applications.

---

### 8. What is the difference between `BeanFactory` and `ApplicationContext`?

**Answer:**

| Feature              | BeanFactory | ApplicationContext                |
|----------------------|-------------|-----------------------------------|
| Bean loading         | Lazy        | Usually eager for singleton beans |
| Enterprise features  | Limited     | Full support                      |
| Events               | No          | Yes                               |
| Internationalization | No          | Yes                               |
| Annotation support   | Basic       | Full                              |

In interviews, the common answer is: **`ApplicationContext` is richer and used in almost all modern Spring applications.**

---

### 9. What is AOP in Spring?

**Answer:**
AOP stands for Aspect-Oriented Programming. It is used to separate cross-cutting concerns from business logic.

Common cross-cutting concerns:

- logging
- security
- transaction management
- auditing
- performance monitoring

**Key terms:**

- Aspect
- Advice
- Join Point
- Pointcut
- Weaving

**Example:**
Logging method execution time using an aspect.

---

### 10. What are the types of advice in Spring AOP?

**Answer:**

- `@Before`
- `@After`
- `@AfterReturning`
- `@AfterThrowing`
- `@Around`

`@Around` is the most powerful because it can control whether the target method executes.

**Example:**

```java
@Aspect
@Component
public class LoggingAspect {

	@Around("execution(* com.example..service..*(..))")
	public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
		long start = System.currentTimeMillis();
		Object result = joinPoint.proceed();
		long end = System.currentTimeMillis();
		System.out.println(joinPoint.getSignature() + " took " + (end - start) + " ms");
		return result;
	}
}
```

---

### 11. What is autowiring in Spring?

**Answer:**
Autowiring means Spring automatically resolves and injects collaborating beans.

It can happen by:

- constructor
- setter
- field

If multiple beans of the same type exist, use:

- `@Qualifier`
- `@Primary`

**Example:**

```java
@Bean
@Primary
public PaymentService cardPaymentService() {
	return new CardPaymentService();
}
```

---

### 12. What is the bean lifecycle in Spring?

**Answer:**
Typical bean lifecycle:

1. instantiate bean
2. inject dependencies
3. call aware interfaces if implemented
4. call `BeanPostProcessor` methods
5. call init methods
6. bean is ready for use
7. call destroy methods before container shutdown

You can define init/destroy methods using:

- `@PostConstruct`
- `@PreDestroy`
- `initMethod`
- `destroyMethod`

---

## Spring Boot

### 13. What is Spring Boot?

**Answer:**
Spring Boot is an opinionated extension of Spring that simplifies setup, configuration, and deployment.

It helps developers create production-ready applications quickly by providing:

- auto-configuration
- starter dependencies
- embedded servers
- production-ready features via Actuator
- minimal configuration

**In one line:**
Spring Boot reduces boilerplate and lets developers focus on business logic.

---

### 14. What is the difference between Spring and Spring Boot?

**Answer:**

| Spring                          | Spring Boot                      |
|---------------------------------|----------------------------------|
| Core framework                  | Built on top of Spring           |
| Requires more configuration     | Minimal configuration            |
| External server often needed    | Embedded server support          |
| Dependency management is manual | Starter dependencies simplify it |
| Setup takes more effort         | Quick bootstrapping              |

**Interview summary:**
Spring is the foundation; Spring Boot makes Spring application development faster and easier.

---

### 15. What is auto-configuration in Spring Boot?

**Answer:**
Auto-configuration means Spring Boot automatically configures beans based on:

- classpath dependencies
- existing beans
- application properties

For example, if Spring MVC is on the classpath, Spring Boot configures dispatcher servlet and web MVC infrastructure automatically.

**Important:**
Auto-configuration is intelligent defaults, not magic. It can be overridden.

---

### 16. What are Spring Boot starters?

**Answer:**
Starters are curated dependency bundles for specific use cases.

Examples:

- `spring-boot-starter-web`
- `spring-boot-starter-data-jpa`
- `spring-boot-starter-security`
- `spring-boot-starter-test`
- `spring-boot-starter-validation`

**Benefit:**
You do not need to manually manage a long list of compatible dependencies.

---

### 17. What is `@SpringBootApplication`?

**Answer:**
`@SpringBootApplication` is a convenience annotation that combines:

- `@Configuration`
- `@EnableAutoConfiguration`
- `@ComponentScan`

It is usually placed on the main application class.

**Example:**

```java
@SpringBootApplication
public class LearningSpringBootConceptsApplication {
	public static void main(String[] args) {
		SpringApplication.run(LearningSpringBootConceptsApplication.class, args);
	}
}
```

---

### 18. What is an embedded server in Spring Boot?

**Answer:**
Spring Boot can package applications with an embedded servlet container, such as:

- Tomcat
- Jetty
- Undertow

This allows the application to run as a standalone JAR.

**Benefit:**
No need to deploy WAR files manually to an external application server in many cases.

---

### 19. What is Spring Boot Actuator?

**Answer:**
Spring Boot Actuator provides production-ready endpoints for monitoring and managing the application.

Common endpoints:

- `/actuator/health`
- `/actuator/info`
- `/actuator/metrics`
- `/actuator/env`
- `/actuator/loggers`

**Use cases:**

- health checks
- observability
- diagnostics
- metrics for dashboards

---

### 20. What is the purpose of `application.properties` or `application.yml`?

**Answer:**
These files store application configuration such as:

- server port
- database configuration
- logging levels
- custom properties
- profile-specific configuration

**Example:**

```properties
server.port=8081
spring.application.name=learning-spring-boot-concepts
logging.level.org.springframework=INFO
```

---

### 21. What are Spring Profiles?

**Answer:**
Profiles allow loading different configurations for different environments, such as:

- dev
- test
- qa
- prod

**Example:**

```properties
spring.profiles.active=dev
```

You can have files like:

- `application-dev.properties`
- `application-prod.properties`

**Example bean:**

```java
@Service
@Profile("dev")
public class DevEmailService implements EmailService {
}
```

---

### 22. What is the difference between `@Value` and `@ConfigurationProperties`?

**Answer:**

- `@Value` is good for injecting a small number of property values.
- `@ConfigurationProperties` is better for mapping grouped configuration into a typed object.

**Preferred in larger applications:** `@ConfigurationProperties`

**Example:**

```java
@ConfigurationProperties(prefix = "app.mail")
public class MailProperties {
	private String host;
	private int port;

	public String getHost() { return host; }
	public void setHost(String host) { this.host = host; }
	public int getPort() { return port; }
	public void setPort(int port) { this.port = port; }
}
```

---

### 23. How do you handle global exceptions in Spring Boot?

**Answer:**
Use `@ControllerAdvice` or `@RestControllerAdvice` with `@ExceptionHandler` methods.

**Benefits:**

- centralized error handling
- consistent API responses
- cleaner controller code

**Example:**

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

	@ExceptionHandler(ResourceNotFoundException.class)
	public ResponseEntity<String> handleNotFound(ResourceNotFoundException ex) {
		return ResponseEntity.status(HttpStatus.NOT_FOUND).body(ex.getMessage());
	}
}
```

---

### 24. How do you improve startup and maintainability in Spring Boot applications?

**Answer:**
Common best practices:

- use constructor injection
- keep controllers thin and move logic to services
- separate DTOs from entities
- enable profiles
- externalize configuration
- use Actuator for visibility
- add validation and exception handling
- avoid unnecessary component scanning breadth
- keep transactions small
- prefer pagination for large result sets

---

## Spring Modules

### 25. What are the major modules of Spring Framework?

**Answer:**
Important Spring modules include:

- Spring Core Container
- Spring AOP
- Spring Data
- Spring JDBC
- Spring ORM
- Spring Transaction Management
- Spring MVC
- Spring WebFlux
- Spring Security
- Spring Test
- Spring Integration
- Spring Batch
- Spring Cloud (ecosystem)

In interviews, mention that Spring is modular and teams usually use only the modules required by the application.

---

### 26. What is Spring MVC?

**Answer:**
Spring MVC is the traditional servlet-based web framework in Spring.

It follows the Model-View-Controller pattern:

- **Model** - application data
- **View** - UI rendering
- **Controller** - request handling

For REST services, Spring MVC is also used with `@RestController`.

**Key components:**

- `DispatcherServlet`
- Controllers
- ViewResolvers
- HandlerMappings

---

### 27. What is `DispatcherServlet`?

**Answer:**
`DispatcherServlet` is the front controller of Spring MVC.

It receives all incoming HTTP requests and delegates them to the proper handler methods.

Responsibilities include:

- mapping requests to controllers
- invoking handlers
- processing exceptions
- resolving views or HTTP responses

---

### 28. What is Spring Data JPA?

**Answer:**
Spring Data JPA simplifies database access by reducing boilerplate code required for persistence.

It provides:

- repository abstraction
- derived query methods
- pagination and sorting
- support for JPQL/native queries

**Example:**

```java
public interface UserRepository extends JpaRepository<User, Long> {
	Optional<User> findByEmail(String email);
}
```

---

### 29. What is the difference between `CrudRepository`, `PagingAndSortingRepository`, and `JpaRepository`?

**Answer:**

- `CrudRepository` - basic CRUD operations
- `PagingAndSortingRepository` - CRUD + pagination and sorting
- `JpaRepository` - extends the above and adds JPA-specific functionality

In most Spring Boot JPA applications, `JpaRepository` is commonly used.

---

### 30. What is Spring Security?

**Answer:**
Spring Security is a powerful framework for authentication and authorization.

Features include:

- authentication
- role-based authorization
- password encoding
- method-level security
- CSRF protection
- OAuth2 and JWT support

**Common use case:**
Securing REST APIs using JWT-based authentication.

---

### 31. What is the difference between authentication and authorization?

**Answer:**

- **Authentication** = who you are
- **Authorization** = what you are allowed to do

**Example:**

- user logs in with username and password -> authentication
- user can access `/admin` only if they have admin role -> authorization

---

### 32. What is Spring JDBC?

**Answer:**
Spring JDBC simplifies database access using JDBC by providing templates and exception handling.

The most common class is `JdbcTemplate`.

**Benefits:**

- reduces boilerplate code
- handles connection/resource cleanup
- translates SQL exceptions into Spring exceptions

---

### 33. What is Spring Batch?

**Answer:**
Spring Batch is used for large-scale batch processing such as:

- importing/exporting data
- scheduled jobs
- report generation
- ETL processing

It supports:

- chunk processing
- retry and skip logic
- transaction management
- job restartability

---

### 34. What is Spring Boot WebFlux and how is it different from Spring MVC?

**Answer:**
Spring WebFlux is the reactive web framework in Spring.

**Difference:**

- Spring MVC -> blocking, servlet-based
- WebFlux -> non-blocking, reactive

WebFlux is useful for highly concurrent applications or streaming use cases.

**Important interview note:**
Do not choose WebFlux unless your application benefits from reactive programming. MVC is still the right choice for many business applications.

---

### 35. What is Spring Test?

**Answer:**
Spring Test supports unit and integration testing.

Common annotations:

- `@SpringBootTest`
- `@WebMvcTest`
- `@DataJpaTest`
- `@MockBean`
- `@Transactional` in tests

**Best practice:**
Use slice tests when full application context is unnecessary, because they are faster.

---

## Transactions

### 36. What is a transaction?

**Answer:**
A transaction is a logical unit of work that must either complete fully or fail fully.

Transactions ensure data consistency.

**ACID properties:**

- **Atomicity** - all or nothing
- **Consistency** - valid state before and after transaction
- **Isolation** - concurrent transactions do not interfere incorrectly
- **Durability** - committed changes persist

---

### 37. What is `@Transactional`?

**Answer:**
`@Transactional` is used to define transaction boundaries declaratively in Spring.

Spring manages starting, committing, or rolling back transactions automatically.

**Example:**

```java
@Service
public class BankService {

	@Transactional
	public void transfer(Long fromId, Long toId, BigDecimal amount) {
		// debit
		// credit
	}
}
```

If an exception occurs, Spring can roll back the transaction.

---

### 38. When does Spring roll back a transaction?

**Answer:**
By default, Spring rolls back on:

- unchecked exceptions (`RuntimeException`)
- `Error`

It does **not** roll back by default on checked exceptions unless configured.

**Example:**

```java
@Transactional(rollbackFor = Exception.class)
public void process() throws Exception {
	// rollback even for checked exception
}
```

---

### 39. What are transaction propagation types?

**Answer:**
Propagation defines how a method should behave if a transaction already exists.

Common types:

- `REQUIRED` - join existing or create new
- `REQUIRES_NEW` - always create new transaction
- `SUPPORTS` - use existing if available
- `NOT_SUPPORTED` - execute without transaction
- `MANDATORY` - must have existing transaction
- `NEVER` - must not have a transaction
- `NESTED` - nested transaction if supported

**Most common:** `REQUIRED`

---

### 40. What are isolation levels?

**Answer:**
Isolation level defines how transaction changes are visible to other concurrent transactions.

Common isolation levels:

- `READ_UNCOMMITTED`
- `READ_COMMITTED`
- `REPEATABLE_READ`
- `SERIALIZABLE`

They protect against issues such as:

- dirty reads
- non-repeatable reads
- phantom reads

**Trade-off:**
Higher isolation gives better consistency but may reduce performance.

---

### 41. What are common pitfalls of `@Transactional`?

**Answer:**
Very common interview topic.

Pitfalls include:

1. **Self-invocation problem**
   - calling a transactional method from another method in the same class may bypass the proxy
2. **Private methods**
   - transactional behavior typically does not apply to private methods via proxies
3. **Checked exceptions**
   - no rollback by default
4. **Long-running transactions**
   - can hold locks and hurt performance
5. **Wrong layer placement**
   - usually better on service layer than controller layer

---

### 42. Why is transaction management usually done in the service layer?

**Answer:**
The service layer typically represents a business use case and may coordinate multiple repository calls.

This is the ideal place to define a single business transaction.

**Example:**
Creating an order may require:

- saving order header
- saving order items
- updating inventory
- recording payment

All of this should often succeed or fail together.

---

### 43. What is the difference between programmatic and declarative transaction management?

**Answer:**

- **Declarative** - use `@Transactional`; recommended in most cases
- **Programmatic** - manually manage transactions using `TransactionTemplate` or transaction manager APIs

Declarative transaction management is more readable and less error-prone.

---

### 44. What is optimistic locking and pessimistic locking?

**Answer:**

- **Optimistic locking** assumes conflicts are rare; usually uses a version field
- **Pessimistic locking** locks data earlier to prevent concurrent modification

**Optimistic locking example:**

```java
@Version
private Long version;
```

Optimistic locking is often preferred in scalable applications because it avoids excessive database locking.

---

## REST API

### 45. What is REST?

**Answer:**
REST stands for Representational State Transfer. It is an architectural style for designing networked applications.

REST principles include:

- client-server separation
- statelessness
- resource-based URIs
- standard HTTP methods
- cacheability
- uniform interface

---

### 46. What is a RESTful API?

**Answer:**
A RESTful API is an API that follows REST principles and exposes resources via HTTP.

**Example resources:**

- `/users`
- `/orders`
- `/products/101`

RESTful APIs usually exchange JSON.

---

### 47. What HTTP methods are commonly used in REST APIs?

**Answer:**

- `GET` - fetch data
- `POST` - create data
- `PUT` - fully update data
- `PATCH` - partially update data
- `DELETE` - remove data

**Example:**

- `GET /users/1`
- `POST /users`
- `PUT /users/1`
- `PATCH /users/1`
- `DELETE /users/1`

---

### 48. What is idempotency in REST?

**Answer:**
An operation is idempotent if performing it multiple times has the same result as performing it once.

Usually:

- `GET` is idempotent
- `PUT` is idempotent
- `DELETE` is generally idempotent
- `POST` is not necessarily idempotent

This is important in distributed systems and retries.

---

### 49. What are common HTTP status codes used in REST APIs?

**Answer:**

- `200 OK` - success
- `201 Created` - resource created
- `204 No Content` - success with no body
- `400 Bad Request` - invalid request
- `401 Unauthorized` - authentication required/failed
- `403 Forbidden` - authenticated but access denied
- `404 Not Found` - resource not found
- `409 Conflict` - conflict, duplicate, version mismatch
- `500 Internal Server Error` - unexpected server failure

Choosing correct status codes is a key sign of a well-designed API.

---

### 50. What is the difference between `@Controller` and `@RestController`?

**Answer:**

- `@Controller` is typically used for MVC applications returning views
- `@RestController` returns response bodies directly, usually JSON

`@RestController` = `@Controller` + `@ResponseBody`

---

### 51. How do you create REST endpoints in Spring Boot?

**Answer:**
Use:

- `@RestController`
- `@RequestMapping`
- `@GetMapping`
- `@PostMapping`
- `@PutMapping`
- `@PatchMapping`
- `@DeleteMapping`

**Example:**

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

	@GetMapping("/{id}")
	public ResponseEntity<UserDto> getUser(@PathVariable Long id) {
		return ResponseEntity.ok(new UserDto(id, "Alice", "alice@example.com"));
	}
}
```

---

### 52. What is the difference between `@PathVariable` and `@RequestParam`?

**Answer:**

- `@PathVariable` reads values from the URI path
- `@RequestParam` reads values from the query string

**Example:**

```java
@GetMapping("/{id}")
public String byId(@PathVariable Long id) {
	return "User: " + id;
}

@GetMapping
public String search(@RequestParam String name) {
	return "Searching: " + name;
}
```

---

### 53. What is `@RequestBody`?

**Answer:**
`@RequestBody` maps the HTTP request body, usually JSON, into a Java object.

**Example:**

```java
@PostMapping
public ResponseEntity<UserDto> createUser(@RequestBody CreateUserRequest request) {
	UserDto dto = new UserDto(1L, request.name(), request.email());
	return ResponseEntity.status(HttpStatus.CREATED).body(dto);
}
```

---

### 54. How do you validate request payloads in Spring Boot?

**Answer:**
Use Bean Validation with:

- `@Valid`
- `@NotBlank`
- `@Email`
- `@Size`
- `@Min`
- `@Max`

**Example:**

```java
public record CreateUserRequest(
	@jakarta.validation.constraints.NotBlank String name,
	@jakarta.validation.constraints.Email String email
) {}

@PostMapping
public ResponseEntity<String> create(@Valid @RequestBody CreateUserRequest request) {
	return ResponseEntity.status(HttpStatus.CREATED).body("Created");
}
```

---

### 55. How should exceptions be returned in REST APIs?

**Answer:**
They should be returned in a consistent and structured format.

Typical fields:

- timestamp
- status
- error
- message
- path

**Best practice:**
Use a standard error response DTO and a global exception handler.

---

### 56. What are DTOs and why should they be used?

**Answer:**
DTO stands for Data Transfer Object.

Use DTOs to:

- avoid exposing internal entities directly
- control response shape
- improve API stability
- support validation

**Interview note:**
Returning JPA entities directly is often discouraged in production APIs.

---

### 57. How do you version a REST API?

**Answer:**
Common approaches:

- URI versioning: `/api/v1/users`
- header versioning
- media type versioning

**Most common in practice:** URI versioning because it is simple and explicit.

---

### 58. How do you handle pagination in REST APIs?

**Answer:**
Pagination avoids loading massive datasets in one response.

Common query parameters:

- `page`
- `size`
- `sort`

**Spring example:**

```java
@GetMapping
public Page<UserDto> getUsers(Pageable pageable) {
	return userService.getUsers(pageable);
}
```

---

### 59. What is the difference between `PUT` and `PATCH`?

**Answer:**

- `PUT` usually replaces the full resource
- `PATCH` partially updates the resource

Use `PATCH` when only some fields need modification.

---

### 60. What are best practices for designing REST APIs?

**Answer:**

- use nouns, not verbs, in endpoints
- keep URIs consistent
- use proper HTTP methods
- return correct status codes
- validate input
- use DTOs
- add pagination/filtering/sorting where needed
- secure endpoints
- document APIs using OpenAPI/Swagger
- make error responses consistent
- keep APIs backward compatible when possible

---

## Code Examples

### Example 1: Simple REST Controller

```java
@RestController
@RequestMapping("/api/products")
public class ProductController {

	@GetMapping("/{id}")
	public ResponseEntity<ProductDto> getById(@PathVariable Long id) {
		ProductDto dto = new ProductDto(id, "Laptop", 75000.0);
		return ResponseEntity.ok(dto);
	}

	@PostMapping
	public ResponseEntity<ProductDto> create(@Valid @RequestBody CreateProductRequest request) {
		ProductDto dto = new ProductDto(1L, request.name(), request.price());
		return ResponseEntity.status(HttpStatus.CREATED).body(dto);
	}
}

record ProductDto(Long id, String name, Double price) {}
record CreateProductRequest(
	@jakarta.validation.constraints.NotBlank String name,
	@jakarta.validation.constraints.Positive Double price
) {}
```

---

### Example 2: Service Layer with Transaction

```java
@Service
public class OrderService {

	private final OrderRepository orderRepository;
	private final InventoryRepository inventoryRepository;

	public OrderService(OrderRepository orderRepository, InventoryRepository inventoryRepository) {
		this.orderRepository = orderRepository;
		this.inventoryRepository = inventoryRepository;
	}

	@Transactional
	public void placeOrder(Order order) {
		orderRepository.save(order);
		inventoryRepository.reduceStock(order.getProductId(), order.getQuantity());
	}
}
```

**Why transaction here?**
Because saving the order and updating inventory should behave as one business unit.

---

### Example 3: Global Exception Handler

```java
@RestControllerAdvice
public class ApiExceptionHandler {

	@ExceptionHandler(IllegalArgumentException.class)
	public ResponseEntity<Map<String, Object>> handleBadRequest(IllegalArgumentException ex) {
		Map<String, Object> error = new HashMap<>();
		error.put("status", 400);
		error.put("message", ex.getMessage());
		return ResponseEntity.badRequest().body(error);
	}
}
```

---

### Example 4: Repository with Spring Data JPA

```java
@Entity
public class User {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;
	private String name;
	private String email;
}

public interface UserRepository extends JpaRepository<User, Long> {
	Optional<User> findByEmail(String email);
}
```

---

### Example 5: Security Concept Example

```java
@RestController
@RequestMapping("/api/admin")
public class AdminController {

	@GetMapping("/dashboard")
	public String dashboard() {
		return "Admin dashboard";
	}
}
```

This endpoint would typically be protected so only authenticated users with an admin role can access it.

---

## Quick Interview Revision Tips

- Understand **why** Spring Boot is used, not just what it is.
- Be ready to explain **IoC**, **DI**, **AOP**, and **`@Transactional`** clearly.
- Learn the difference between **entity**, **DTO**, **DAO/repository**, and **service**.
- Be ready for real-world questions like:
  - how to design a clean REST API
  - where to place transactions
  - how to handle exceptions globally
  - how to secure APIs
  - how to optimize database access
- For senior interviews, expect follow-up questions on:
  - microservices
  - observability
  - caching
  - resilience patterns
  - event-driven architecture
  - async processing

---

## Final Note

This file is intentionally written as a practical interview companion. For best preparation, revise each answer in your own words and practice writing small examples for:

- `@RestController`
- `@Service`
- `@Repository`
- `@Transactional`
- `JpaRepository`
- `@ControllerAdvice`
- validation with `@Valid`

That combination covers a large percentage of Spring and Spring Boot interview discussions.

