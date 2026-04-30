# 🌱 Spring Core + Spring Boot — Interview Preparation

> **Ye notes interview ke liye hain — concepts + code + questions sab ek jagah**

---

## 📌 Table of Contents

1. [Spring Core vs Spring Boot](#1-spring-core-vs-spring-boot)
2. [IoC & Dependency Injection](#2-ioc--dependency-injection)
3. [Bean Lifecycle](#3-bean-lifecycle)
4. [Bean Scopes](#4-bean-scopes)
5. [Spring Annotations](#5-spring-annotations)
6. [AOP](#6-aop)
7. [ApplicationContext & BeanFactory](#7-applicationcontext--beanfactory)
8. [Spring Data JPA](#8-spring-data-jpa)
9. [Spring MVC](#9-spring-mvc)
10. [Spring Security](#10-spring-security)
11. [Spring Boot Auto-Configuration](#11-spring-boot-auto-configuration)
12. [Profiles](#12-profiles)
13. [Testing](#13-testing)

---

## 1. Spring Core vs Spring Boot

### Spring Core kya hai?
Spring Framework ka **foundation** — IoC, DI, Bean Management sab yahan se aata hai.

### Spring Boot kya hai?
Spring ke upar ek **layer** jo development easy banata hai — auto-configuration, embedded server, starter dependencies.

### Relationship
```
Spring Boot
    └── Spring Core (Foundation)
    └── Spring MVC (Web)
    └── Spring Data (Database)
    └── Spring Security (Auth)
```

### Comparison Table

| Feature | Spring Core | Spring Boot |
|---|---|---|
| Configuration | Manual (XML/Java) | Auto-configuration |
| Server Setup | Alag se karo | Built-in Tomcat/Jetty |
| Project Setup | Complex | `start.spring.io` se minutes mein |
| Learning Curve | Steep | Relatively easy |
| Control | Full control | Convention over configuration |

> ✅ **Spring Boot, Spring Core ko replace nahi karta** — uske upar build hota hai

---

## 2. IoC & Dependency Injection

### Problem (Bina Spring ke)
```java
public class OrderService {
    // Khud object bana rahe ho — tightly coupled
    PaymentService paymentService = new PaymentService();
}
```
**Problems:**
- Tightly coupled code
- Testing mushkil (mock nahi laga sakte)
- Badlav karna mushkil

### IoC ka Solution
> **"Object mat banao khud — Spring ko bolo, wo banayega aur dega"**

Control aapke haath se Spring ke haath mein jaata hai — isliye **Inversion of Control**

### DI ke 3 Types

#### ✅ 1. Constructor Injection (Best Practice)
```java
@Service
public class OrderService {
    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

#### 2. Setter Injection
```java
@Service
public class OrderService {
    private PaymentService paymentService;

    @Autowired
    public void setPaymentService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

#### 3. Field Injection (Avoid karo)
```java
@Service
public class OrderService {
    @Autowired
    private PaymentService paymentService;
}
```

### Interview Questions

**Q1. IoC aur DI mein kya difference hai?**
> IoC ek **concept** hai (control Spring ko do), DI ek **technique** hai jisse IoC implement hota hai

**Q2. Constructor injection best kyun hai?**
> - Object immutable rehta hai (`final` use kar sakte ho)
> - Testing easy hoti hai
> - Circular dependency startup pe pakdi jaati hai

**Q3. `@Autowired` kaise kaam karta hai?**
> Spring IoC container mein us type ka Bean dhundhta hai aur inject kar deta hai

### Summary Table

| Term | Matlab |
|---|---|
| IoC | Control Spring ko do, khud mat rakho |
| DI | Spring dependencies inject karta hai |
| IoC Container | Wo box jisme Spring saare beans rakhta hai |
| Bean | Wo object jo Spring manage karta hai |

---

## 3. Bean Lifecycle

### Poora Flow
```
Start
  ↓
1. Bean Definition Read karo (class scan karo)
  ↓
2. Bean ka Object banao (instantiation)
  ↓
3. Dependencies inject karo (@Autowired wali)
  ↓
4. @PostConstruct method call karo
  ↓
5. Bean ready hai — use karo ✅
  ↓
6. Application band ho rahi hai...
  ↓
7. @PreDestroy method call karo
  ↓
8. Bean destroy
End
```

### Code Example
```java
@Component
public class DatabaseService {

    public DatabaseService() {
        System.out.println("1. Constructor called - Object ban gaya");
    }

    @Autowired
    private UserRepository userRepository;

    @PostConstruct
    public void init() {
        System.out.println("2. @PostConstruct - DB connection open karo");
    }

    @PreDestroy
    public void cleanup() {
        System.out.println("3. @PreDestroy - DB connection close karo");
    }
}
```

### Interview Questions

**Q1. @PostConstruct aur Constructor mein kya difference hai?**
> Constructor mein dependencies inject nahi hoti, `@PostConstruct` mein hoti hain — isliye initialization ka kaam `@PostConstruct` mein karo

**Q2. @PostConstruct kab use karte hain?**
> Jab initialization ke liye injected dependencies chahiye — jaise cache load karna, DB connection open karna

---

## 4. Bean Scopes

### 1. Singleton (Default) ⭐
```java
@Component
@Scope("singleton") // ye default hai, likhna zaruri nahi
public class UserService {
    // Poori application mein SIRF EK object
}
```

### 2. Prototype
```java
@Component
@Scope("prototype")
public class ReportGenerator {
    // Jitni baar maango, utni baar NAYA object
}
```

### 3. Request (Web apps)
```java
@Component
@Scope("request")
public class RequestContext {
    // Har HTTP Request ke liye naya object
}
```

### 4. Session (Web apps)
```java
@Component
@Scope("session")
public class UserSession {
    // Har User Session ke liye naya object
}
```

### Singleton vs Prototype

| | Singleton | Prototype |
|---|---|---|
| Object kitne | Sirf 1 | Har baar naya |
| Default | ✅ Haan | ❌ Nahi |
| Memory | Kam use | Zyada use |
| Use case | Services, Repos | Report, Email objects |
| @PreDestroy | ✅ Call hoti | ❌ Call nahi hoti |

### Interview Questions

**Q1. Default scope kya hota hai?**
> **Singleton** — poori app mein sirf ek object

**Q2. Prototype bean ka @PreDestroy kyun call nahi hota?**
> Spring prototype bean ko track nahi karta baad mein — dene ke baad bhool jaata hai

**Q3. Singleton bean thread-safe hota hai kya?**
> **Nahi** — ek hi object multiple threads use karte hain, isliye state (instance variables) singleton mein nahi rakhna chahiye

---

## 5. Spring Annotations

### Ye 4 main annotations hain

```
@Component      ← Generic bean
@Service        ← Business logic layer
@Repository     ← Database layer
@Controller     ← Web layer (HTTP requests handle karna)
```

> **Technically** — chaaron kaam ek jaisa karte hain (bean banate hain), lekin **semantic meaning** alag hai aur kuch extra features bhi hain

---

### 1. @Component — Generic
```java
@Component
public class EmailValidator {
    // Koi bhi utility class jo kisi layer mein fit nahi hoti
    public boolean isValid(String email) {
        return email.contains("@");
    }
}
```
> Jab class kisi specific layer mein fit na ho — `@Component` use karo

---

### 2. @Service — Business Logic
```java
@Service
public class OrderService {
    public void placeOrder(Order order) {
        // validate karo
        // payment karo
        // DB mein save karo
    }
}
```
> Business logic wali classes pe lagao — code readable rehta hai

---

### 3. @Repository — Database Layer
```java
@Repository
public class UserRepository {
    public User findById(Long id) {
        // SQL query ya JPA
    }
}
```

**Extra Feature — Exception Translation:**
```
SQLException (DB specific)
      ↓  @Repository magic
DataAccessException (Spring generic)
```
> Iska fayda — code kisi bhi DB (MySQL, PostgreSQL) se independent rehta hai

---

### 4. @Controller vs @RestController

```java
// HTML return karta hai
@Controller
public class PageController {
    @GetMapping("/home")
    public String home(Model model) {
        return "home"; // home.html
    }
}
```

```java
// JSON return karta hai — REST APIs ke liye
@RestController  // = @Controller + @ResponseBody
public class UserController {
    @GetMapping("/users")
    public List<User> getUsers() {
        return userService.getAllUsers();
    }
}
```

---

### Layer Diagram

```
[ Client / Browser ]
        ↓
  @Controller / @RestController   ← HTTP Request yahan aati hai
        ↓
     @Service                     ← Business logic yahan hoti hai
        ↓
    @Repository                   ← DB se data yahan se aata hai
        ↓
   [ Database ]
```

---

### @Configuration + @Bean

```java
@Configuration
public class AppConfig {

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

> **Kab use karo?** Jab third-party library ki class ko bean banana ho — usmein `@Component` nahi laga sakte (source code available nahi hota)

---

### @Primary vs @Qualifier

**Problem — 2 same type ke beans hain:**
```java
@Component
public class UPIPaymentService implements PaymentService { }

@Component
public class CardPaymentService implements PaymentService { }

@Autowired
PaymentService paymentService; // ❌ ERROR — Spring confuse!
```

**Solution 1 — @Primary (default wala set karo):**
```java
@Component
@Primary
public class UPIPaymentService implements PaymentService { }

@Autowired
PaymentService paymentService; // ✅ UPIPaymentService inject hoga
```

**Solution 2 — @Qualifier (specific wala maango):**
```java
@Autowired
@Qualifier("cardPaymentService")
PaymentService paymentService; // ✅ CardPaymentService inject hoga
```

---

### @Value — Properties inject karna

```properties
# application.properties
app.name=MyApp
app.timeout=5000
```

```java
@Component
public class AppConfig {

    @Value("${app.name}")
    private String appName;

    @Value("${app.timeout}")
    private int timeout;
}
```

---

### Saare Annotations Summary

| Annotation | Kaam |
|---|---|
| `@Component` | Generic bean |
| `@Service` | Business logic bean |
| `@Repository` | DB layer bean + exception translation |
| `@Controller` | Web layer — HTML return |
| `@RestController` | Web layer — JSON return |
| `@Configuration` | Bean definitions wali class |
| `@Bean` | Method jo bean return kare |
| `@Autowired` | Dependency inject karo |
| `@Qualifier` | Specific bean choose karo |
| `@Primary` | Default bean set karo |
| `@Value` | Property value inject karo |

---

### Interview Questions

**Q1. @Component, @Service, @Repository mein kya difference hai?**
> Technically same kaam karte hain — bean banate hain. Lekin `@Repository` mein DB exception translation extra milti hai. Baaki dono **semantic clarity** ke liye alag hain

**Q2. @Controller vs @RestController?**
> `@Controller` HTML return karta hai, `@RestController` JSON. `@RestController` = `@Controller` + `@ResponseBody`

**Q3. @Primary vs @Qualifier?**
> `@Primary` — default bean set karta hai, `@Qualifier` — specific bean manually choose karte hain. `@Qualifier` ki priority zyada hoti hai

**Q4. @Bean aur @Component mein difference?**
> `@Component` — apni class pe lagate hain, `@Bean` — third-party class ke liye method pe lagate hain jab source code edit nahi kar sakte

---

## 6. AOP (Aspect Oriented Programming)

### AOP kya hai?

> **"Jo kaam baar baar alag alag jagah likhna padta hai — use ek jagah likho"**

Ye kaam har jagah repeat hote hain:
- Logging — method call se pehle/baad log karo
- Security check — user authorized hai kya?
- Transaction management — DB transaction handle karo
- Performance monitoring — method kitni der laga?

Inka koi business logic se relation nahi — phir bhi har class mein likhna padta tha.

**AOP bolta hai — ek jagah likho, sab jagah apply ho jaayega!**

---

### AOP ke Main Terms

| Term | Matlab | Example |
|---|---|---|
| **Aspect** | Cross-cutting concern wali class | `LoggingAspect` |
| **Advice** | Kab aur kya karna hai | `@Before`, `@After`, `@Around` |
| **Pointcut** | Kis method pe apply karna hai | `execution(* com.app.service.*.*(..))` |
| **JoinPoint** | Woh jagah jahan advice apply hoti hai | Method call |

---

### Advice ke Types

```
Method call hone wali hai...
        ↓
   @Before           ← Pehle chalaao
        ↓
   Method runs...
        ↓
   @After            ← Baad mein chalaao (hamesha)
   @AfterReturning   ← Sirf success pe
   @AfterThrowing    ← Sirf exception pe
        ↓
   @Around           ← Pehle bhi, baad bhi — full control
```

---

### Code Example — Logging Aspect

```java
@Aspect
@Component
public class LoggingAspect {

    // Pointcut — service package ke saare methods pe apply karo
    @Before("execution(* com.app.service.*.*(..))")
    public void logBefore(JoinPoint joinPoint) {
        System.out.println("Method call: " + joinPoint.getSignature().getName());
    }

    @AfterReturning(
        pointcut = "execution(* com.app.service.*.*(..))",
        returning = "result"
    )
    public void logAfter(JoinPoint joinPoint, Object result) {
        System.out.println("Method completed: " + joinPoint.getSignature().getName());
    }

    @AfterThrowing(
        pointcut = "execution(* com.app.service.*.*(..))",
        throwing = "error"
    )
    public void logException(JoinPoint joinPoint, Throwable error) {
        System.out.println("Exception in: " + joinPoint.getSignature().getName() 
                           + " — " + error.getMessage());
    }
}
```

---

### @Around — Sabse Powerful

```java
@Aspect
@Component
public class PerformanceAspect {

    @Around("execution(* com.app.service.*.*(..))")
    public Object measureTime(ProceedingJoinPoint joinPoint) throws Throwable {
        
        long start = System.currentTimeMillis();
        
        Object result = joinPoint.proceed(); // Actual method chalaao
        
        long end = System.currentTimeMillis();
        System.out.println(joinPoint.getSignature().getName() 
                           + " took: " + (end - start) + "ms");
        
        return result;
    }
}
```

> `@Around` mein `joinPoint.proceed()` call karna **zaruri** hai — warna actual method run nahi hogi!

---

### Real World Use Cases

```java
// 1. Security Check
@Before("execution(* com.app.admin.*.*(..))")
public void checkAdminAccess() {
    if (!SecurityContext.isAdmin()) {
        throw new UnauthorizedException("Admin only!");
    }
}

// 2. Transaction Management (Spring khud karta hai @Transactional se)
@Around("@annotation(Transactional)")
public Object manageTransaction(ProceedingJoinPoint jp) throws Throwable {
    // begin transaction
    Object result = jp.proceed();
    // commit transaction
    return result;
}
```

---

### Interview Questions

**Q1. AOP kya hai aur kyun use karte hain?**
> Cross-cutting concerns (logging, security, transactions) ko business logic se alag karne ke liye — DRY principle follow hota hai

**Q2. @Before, @After, @Around mein difference?**
> `@Before` — method se pehle, `@After` — method ke baad (hamesha), `@Around` — pehle bhi baad bhi, method ko control karta hai

**Q3. @Around mein proceed() call karna kyun zaruri hai?**
> `proceed()` nahi karoge toh actual method execute nahi hogi — `@Around` poora control leta hai

**Q4. AOP kahan use hota hai real projects mein?**
> Logging, Security/Authorization, Transaction Management, Performance Monitoring, Caching

---

## 7. ApplicationContext & BeanFactory

### BeanFactory kya hai?
> Spring ka **sabse basic container** — sirf beans banata hai aur deta hai, bas!

```java
// BeanFactory — purana tarika
BeanFactory factory = new XmlBeanFactory(new FileSystemResource("beans.xml"));
UserService userService = (UserService) factory.getBean("userService");
```

**Features:**
- Beans **lazily** load hote hain — jab maango tab banao
- Bahut lightweight
- Sirf basic DI support

---

### ApplicationContext kya hai?
> BeanFactory ka **advanced version** — extra features ke saath

```java
// ApplicationContext — modern tarika
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
UserService userService = context.getBean(UserService.class);
```

**Extra Features jo BeanFactory mein nahi hain:**
- Beans **eagerly** load hote hain — app start hote hi sab ban jaate hain
- **Event publishing** support
- **Internationalization (i18n)** support
- **AOP** integration
- `@PostConstruct` / `@PreDestroy` automatically handle karta hai

---

### BeanFactory vs ApplicationContext

| Feature | BeanFactory | ApplicationContext |
|---|---|---|
| Bean Loading | Lazy (maango tab bano) | Eager (start pe sab bano) |
| AOP Support | ❌ Nahi | ✅ Haan |
| Event Publishing | ❌ Nahi | ✅ Haan |
| @PostConstruct | ❌ Nahi | ✅ Haan |
| i18n Support | ❌ Nahi | ✅ Haan |
| Use in Production | ❌ Almost never | ✅ Hamesha |

> ✅ **Real projects mein hamesha ApplicationContext use karo**

---

### ApplicationContext ke Types

```java
// 1. Java config se — aaj kal sabse zyada use
ApplicationContext context =
    new AnnotationConfigApplicationContext(AppConfig.class);

// 2. XML config se — purana tarika
ApplicationContext context =
    new ClassPathXmlApplicationContext("applicationContext.xml");

// 3. Spring Boot mein — automatically banta hai
ApplicationContext context = SpringApplication.run(MyApp.class, args);
```

---

### Eager vs Lazy Loading

```
Eager (Default):
App Start → Saare Beans ek saath ban jaate hain → App Ready ✅
(Startup thoda slow, runtime fast)

Lazy:
App Start → Koi bean nahi bana → Pehli baar maango → Tab bano
(Startup fast, pehli request slow)
```

```java
@Component
@Lazy  // Jab pehli baar maanga jayega tab banega
public class HeavyService { }
```

---

### ApplicationContext ke Useful Methods

```java
ApplicationContext ctx = SpringApplication.run(MyApp.class, args);

// Bean lena
UserService us = ctx.getBean(UserService.class);

// Bean exist karta hai kya?
boolean exists = ctx.containsBean("userService");

// Saare bean names
String[] beans = ctx.getBeanDefinitionNames();

// Environment property
String port = ctx.getEnvironment().getProperty("server.port");
```

---

### Interview Questions

**Q1. BeanFactory aur ApplicationContext mein kya difference hai?**
> `BeanFactory` basic container hai — lazy loading, sirf DI. `ApplicationContext` advanced hai — eager loading, AOP, events, i18n, `@PostConstruct` support. Production mein hamesha `ApplicationContext` use karte hain

**Q2. Eager aur Lazy loading mein difference?**
> Eager — app start pe sab beans ban jaate hain (default). Lazy — pehli baar request aane pe bean banta hai. `@Lazy` se specific bean lazy kar sakte hain

**Q3. Spring Boot mein ApplicationContext kaise milta hai?**
> `SpringApplication.run()` ApplicationContext return karta hai. Ya `@Autowired ApplicationContext context` inject kar sakte hain

---

### Summary Table

| Concept | Matlab |
|---|---|
| BeanFactory | Basic container — sirf beans deta hai |
| ApplicationContext | Advanced container — production use |
| Eager Loading | App start pe sab beans ready |
| Lazy Loading | Pehli baar maango tab bano |
| `@Lazy` | Specific bean ko lazy karo |

---

## 8. Spring Data JPA

### JPA kya hai?
> **JPA (Java Persistence API)** — ek specification hai jo batati hai ki Java objects ko database mein kaise save/load karo

```
Java Object  ←→  JPA  ←→  Database Table
(User.java)           (users table)
```

**Bina JPA ke:**
```java
String sql = "SELECT * FROM users WHERE id = ?";
PreparedStatement ps = connection.prepareStatement(sql);
ps.setLong(1, id);
ResultSet rs = ps.executeQuery();
// manually mapping karo...
```

**JPA ke saath:**
```java
User user = userRepository.findById(id); // Bas itna!
```

---

### Stack samjho

```
Spring Data JPA
      ↓
    JPA (Specification)
      ↓
  Hibernate (Implementation) ← Actually DB se baat karta hai
      ↓
  Database (MySQL, PostgreSQL etc.)
```

---

### @Entity — Java class ko Table se map karo

```java
@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY) // Auto increment
    private Long id;

    @Column(name = "full_name", nullable = false)
    private String name;

    @Column(unique = true)
    private String email;

    private int age; // Column name automatically "age" hoga

    // Getters, Setters, Constructors
}
```

---

### Repository — CRUD operations free milte hain

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    // JpaRepository<Entity Type, Primary Key Type>
    // Bas extend karo — sab methods already available!
}
```

**Free methods:**
```java
userRepository.save(user);        // Insert / Update
userRepository.findById(1L);      // ID se dhundho
userRepository.findAll();         // Sab records
userRepository.deleteById(1L);    // Delete
userRepository.count();           // Total count
userRepository.existsById(1L);    // Exist karta hai?
```

---

### Custom Query Methods — Method naam se SQL auto-generate!

```java
public interface UserRepository extends JpaRepository<User, Long> {

    User findByEmail(String email);
    // SELECT * FROM users WHERE email = ?

    List<User> findByAge(int age);
    // SELECT * FROM users WHERE age = ?

    List<User> findByNameAndAge(String name, int age);
    // SELECT * FROM users WHERE name = ? AND age = ?

    List<User> findByAgeGreaterThan(int age);
    // SELECT * FROM users WHERE age > ?

    List<User> findByNameContaining(String keyword);
    // SELECT * FROM users WHERE name LIKE '%keyword%'

    List<User> findByAgeOrderByNameAsc(int age);
    // SELECT * FROM users WHERE age = ? ORDER BY name ASC
}
```

> **Magic!** — Method ka naam dekh ke Spring khud SQL banata hai 🪄

---

### @Query — Custom JPQL/SQL

```java
public interface UserRepository extends JpaRepository<User, Long> {

    // JPQL — Table naam nahi, Class naam use karo
    @Query("SELECT u FROM User u WHERE u.email = :email")
    User findByEmailCustom(@Param("email") String email);

    // Native SQL
    @Query(value = "SELECT * FROM users WHERE age > :age", nativeQuery = true)
    List<User> findUsersOlderThan(@Param("age") int age);

    // Update ke liye
    @Modifying
    @Transactional
    @Query("UPDATE User u SET u.name = :name WHERE u.id = :id")
    int updateUserName(@Param("id") Long id, @Param("name") String name);
}
```

---

### @Transactional — Important!

```java
@Service
public class OrderService {

    @Transactional
    public void placeOrder(Order order) {
        orderRepository.save(order);         // Step 1
        inventoryService.reduceStock(order);  // Step 2
        paymentService.charge(order);         // Step 3
        // Koi bhi step fail → sab rollback ✅
    }
}
```

> **Bina `@Transactional`** — Step 2 fail hua toh Step 1 ka data save reh jaata (inconsistent!)
> **`@Transactional` ke saath** — sab fail, sab rollback ✅

---

### Relationships

**One-to-Many (Ek User ke kai Orders):**
```java
@Entity
public class User {
    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL)
    private List<Order> orders;
}

@Entity
public class Order {
    @ManyToOne
    @JoinColumn(name = "user_id")
    private User user;
}
```

**Many-to-Many (Students aur Courses):**
```java
@Entity
public class Student {
    @ManyToMany
    @JoinTable(name = "student_course",
        joinColumns = @JoinColumn(name = "student_id"),
        inverseJoinColumns = @JoinColumn(name = "course_id"))
    private List<Course> courses;
}
```

---

### application.properties — JPA Config

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=password

spring.jpa.hibernate.ddl-auto=update   # Dev mein
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

**ddl-auto options:**

| Value | Matlab | Use |
|---|---|---|
| `create` | Har start pe naye tables | Testing |
| `update` | Changes apply karo, data safe | Dev |
| `validate` | Sirf check karo | Staging |
| `none` | Kuch mat karo | Production ✅ |

---

### Interview Questions

**Q1. JPA aur Hibernate mein kya difference hai?**
> JPA ek **specification** hai (rules define karta hai), Hibernate ek **implementation** hai (actually kaam karta hai). Spring Data JPA, JPA ke upar convenience layer hai

**Q2. `save()` insert karega ya update?**
> Agar `id` null hai — INSERT. Agar `id` set hai — UPDATE

**Q3. `findById()` vs `getById()` difference?**
> `findById()` — `Optional<T>` return karta hai, turant DB hit. `getById()` — lazy proxy return karta hai, DB tab hit hota hai jab data access karo

**Q4. @Transactional kahan lagana chahiye?**
> Service layer pe — Repository pe nahi

**Q5. N+1 Problem kya hota hai?**
> Ek query se list aati hai, phir har item ke liye alag query — bahut slow. `JOIN FETCH` ya `@EntityGraph` se fix karte hain

---

### Summary Table

| Concept | Matlab |
|---|---|
| `@Entity` | Java class = DB Table |
| `@Id` | Primary Key |
| `@GeneratedValue` | Auto increment ID |
| `JpaRepository` | CRUD methods free milte hain |
| Custom Methods | Method naam se SQL auto-generate |
| `@Query` | Custom JPQL/SQL likhna |
| `@Transactional` | Sab success ya sab rollback |
| `ddl-auto=none` | Production mein use karo |

---

## 9. Spring MVC

### MVC Pattern kya hota hai?
> **Model - View - Controller** — code ko 3 parts mein todta hai

```
Request aaya
     ↓
Controller  ← Request handle karo
     ↓
Service/Model  ← Business logic, data
     ↓
View  ← Response (JSON ya HTML)
     ↓
Response gaya
```

---

### Request ka Poora Flow

```
Client (Browser/Postman)
        ↓
  DispatcherServlet     ← Spring ka Front Controller — sab requests yahan aati hain
        ↓
  Handler Mapping       ← Kaunsa Controller handle karega?
        ↓
  Controller Method     ← @GetMapping / @PostMapping wala method
        ↓
  Service Layer         ← Business logic
        ↓
Client ko Response
```

---

### Controller banana

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping
    public List<User> getAllUsers() {
        return userService.getAllUsers();
    }

    @GetMapping("/{id}")
    public User getUserById(@PathVariable Long id) {
        return userService.getUserById(id);
    }

    @PostMapping
    public User createUser(@RequestBody User user) {
        return userService.createUser(user);
    }

    @PutMapping("/{id}")
    public User updateUser(@PathVariable Long id, @RequestBody User user) {
        return userService.updateUser(id, user);
    }

    @DeleteMapping("/{id}")
    public void deleteUser(@PathVariable Long id) {
        userService.deleteUser(id);
    }
}
```

---

### Important Annotations

```java
// URL: /users/42
@GetMapping("/users/{id}")
public User getUser(@PathVariable Long id) { }
// PathVariable — URL path se value lo

// URL: /users?page=1&size=10
@GetMapping("/users")
public List<User> getUsers(
    @RequestParam(defaultValue = "0") int page,
    @RequestParam(defaultValue = "10") int size) { }
// RequestParam — query string se value lo

// POST /users   Body: {"name":"Rahul","email":"rahul@gmail.com"}
@PostMapping("/users")
public User createUser(@RequestBody User user) { }
// RequestBody — JSON body → Java object (Jackson library)

// Header se value lo
@GetMapping("/data")
public String getData(@RequestHeader("Authorization") String token) { }
```

---

### ResponseEntity — Status Code Control

```java
// 201 Created
@PostMapping
public ResponseEntity<User> createUser(@RequestBody User user) {
    User saved = userService.createUser(user);
    return ResponseEntity.status(HttpStatus.CREATED).body(saved);
}

// 404 Not Found
@GetMapping("/{id}")
public ResponseEntity<User> getUser(@PathVariable Long id) {
    User user = userService.findById(id);
    if (user == null) return ResponseEntity.notFound().build();
    return ResponseEntity.ok(user);
}

// 204 No Content
@DeleteMapping("/{id}")
public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
    userService.deleteUser(id);
    return ResponseEntity.noContent().build();
}
```

---

### Global Exception Handling — @ControllerAdvice

```java
@ControllerAdvice  // Poori app ki exceptions ek jagah handle karo
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<String> handleUserNotFound(UserNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(ex.getMessage());
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleGeneral(Exception ex) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                             .body("Kuch gadbad ho gayi!");
    }
}
```

> Har controller mein try-catch likhne ki zaroorat nahi — ek jagah sab handle!

---

### Validation — @Valid

```java
public class User {
    @NotNull(message = "Name required hai")
    @Size(min = 2, max = 50)
    private String name;

    @Email(message = "Valid email do")
    private String email;

    @Min(value = 18, message = "Age 18+ hona chahiye")
    private int age;
}

@PostMapping("/users")
public User createUser(@Valid @RequestBody User user) {
    // Validation fail → 400 Bad Request automatically
    return userService.createUser(user);
}
```

---

### Interview Questions

**Q1. DispatcherServlet kya karta hai?**
> Spring MVC ka Front Controller — saari incoming requests yahan aati hain, phir sahi Controller ko route karta hai

**Q2. @Controller vs @RestController?**
> `@Controller` HTML/View return karta hai. `@RestController` = `@Controller` + `@ResponseBody` — JSON return karta hai

**Q3. @PathVariable vs @RequestParam?**
> `@PathVariable` — URL path se value leta hai (`/users/5`). `@RequestParam` — query string se (`/users?id=5`)

**Q4. @RequestBody kya karta hai?**
> HTTP request body ka JSON automatically Java object mein convert karta hai — Jackson library use hoti hai

**Q5. @ControllerAdvice kya hai?**
> Global exception handler — poori application ki exceptions ek jagah handle karo, har controller mein try-catch nahi likhna

---

### Summary Table

| Annotation | Kaam |
|---|---|
| `@RestController` | JSON REST API controller |
| `@RequestMapping` | Base URL set karo |
| `@GetMapping` | HTTP GET |
| `@PostMapping` | HTTP POST |
| `@PutMapping` | HTTP PUT |
| `@DeleteMapping` | HTTP DELETE |
| `@PathVariable` | URL se value `/users/{id}` |
| `@RequestParam` | Query param `?page=1` |
| `@RequestBody` | JSON body → Java object |
| `@Valid` | Request validate karo |
| `@ControllerAdvice` | Global exception handler |
| `ResponseEntity` | Status code + body control |

---

## 10. Spring Security

### Authentication vs Authorization
```
Authentication  → "Kaun ho tum?" (Login — username/password verify)
Authorization   → "Tumhe permission hai?" (Role check — ADMIN/USER)
```

---

### SecurityFilterChain — Modern way (Spring Boot 3+)

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable())  // REST API ke liye CSRF disable
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/public/**").permitAll()    // Sab access kar sakte
                .requestMatchers("/api/admin/**").hasRole("ADMIN") // Sirf ADMIN
                .requestMatchers("/api/users/**").hasRole("USER")  // Sirf USER
                .anyRequest().authenticated()  // Baaki sab login chahiye
            )
            .httpBasic(Customizer.withDefaults());

        return http.build();
    }
}
```

---

### JWT Flow

```
User login karta hai
        ↓
Server token generate karta hai (JWT)
        ↓
User har request mein ye token bhejta hai (Header mein)
        ↓
Server token verify karta hai
        ↓
Access milta hai ✅
```

```
JWT Token structure:
eyJhbGciOiJIUzI1NiJ9    ← Header (algorithm)
.eyJ1c2VyIjoiUmFodWwifQ ← Payload (user data)
.SflKxwRJSMeKKF2QT4fw   ← Signature (verify ke liye)
```

---

### UserDetailsService — DB se users load karo

```java
@Service
public class CustomUserDetailsService implements UserDetailsService {

    @Autowired
    private UserRepository userRepository;

    @Override
    public UserDetails loadUserByUsername(String username)
            throws UsernameNotFoundException {

        User user = userRepository.findByUsername(username)
                .orElseThrow(() -> new UsernameNotFoundException("User not found!"));

        return org.springframework.security.core.userdetails.User
                .withUsername(user.getUsername())
                .password(user.getPassword())  // Encrypted password
                .roles(user.getRole())
                .build();
    }
}
```

---

### Password Encoding — Plain text kabhi nahi!

```java
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}

@PostMapping("/register")
public User register(@RequestBody User user) {
    // "mypassword123" → "$2a$10$xyz..." (encrypted)
    user.setPassword(passwordEncoder.encode(user.getPassword()));
    return userRepository.save(user);
}
```

---

### Method Level Security

```java
@Configuration
@EnableMethodSecurity
public class SecurityConfig { }

@Service
public class AdminService {

    @PreAuthorize("hasRole('ADMIN')")
    public List<User> getAllUsers() {
        return userRepository.findAll();
    }

    @PreAuthorize("hasRole('USER') or hasRole('ADMIN')")
    public User getOwnProfile(Long id) {
        return userRepository.findById(id).orElseThrow();
    }
}
```

---

### Interview Questions

**Q1. Authentication vs Authorization kya hai?**
> Authentication — "Kaun ho tum?" (login verify). Authorization — "Tumhe permission hai?" (role check). Pehle authentication, phir authorization

**Q2. JWT kya hota hai aur kaise kaam karta hai?**
> JSON Web Token — stateless authentication. Login pe token milta hai, har request mein header mein bhejte hain, server verify karta hai. Session DB mein save nahi hota

**Q3. BCrypt kyun use karte hain?**
> One-way hash — original password recover nahi ho sakta. Slow by design — brute force mushkil. Salt automatically add hota hai

**Q4. CSRF kya hai aur REST APIs mein disable kyun karte hain?**
> Cross-Site Request Forgery attack. REST APIs JWT use karte hain jo cookies mein nahi hota, isliye CSRF attack possible nahi — safely disable kar sakte hain

**Q5. @PreAuthorize vs URL-based security?**
> URL-based — `SecurityFilterChain` mein (coarse-grained). `@PreAuthorize` — method level pe (fine-grained). Dono saath use kar sakte hain

---

### Summary Table

| Concept | Matlab |
|---|---|
| Authentication | Login verify — kaun ho tum |
| Authorization | Permission check — kya kar sakte ho |
| JWT | Stateless token-based auth |
| BCrypt | Password hashing — one way |
| `SecurityFilterChain` | Security rules configure karo |
| `UserDetailsService` | DB se users load karo |
| `@PreAuthorize` | Method level security |
| `permitAll()` | Sab access kar sakte |
| `hasRole()` | Specific role chahiye |

---

## 11. Spring Boot Auto-Configuration

### Auto-Configuration kya hai?
> Spring Boot dependencies dekh ke **khud decide karta hai** ki kya configure karna hai — aapko kuch nahi karna!

```
Pehle (Spring Core):
- DataSource manually configure karo
- TransactionManager manually configure karo
- EntityManagerFactory manually configure karo
- Sab XML/Java config mein likhna padta tha 😫

Spring Boot ke saath:
- spring-boot-starter-data-jpa add karo
- application.properties mein DB URL do
- Ho gaya! ✅ Sab auto-configure ho jaata hai
```

---

### Kaise kaam karta hai?

```java
@SpringBootApplication
public class MyApp {
    public static void main(String[] args) {
        SpringApplication.run(MyApp.class, args);
    }
}
```

`@SpringBootApplication` = teen annotations ek saath:
```java
@SpringBootConfiguration   // Configuration class hai
@EnableAutoConfiguration   // Auto-config enable karo ← YE MAGIC KARTA HAI
@ComponentScan             // Beans scan karo
```

---

### Auto-Configuration andar kaise hoti hai?

```
1. Spring Boot classpath scan karta hai
        ↓
2. spring.factories / AutoConfiguration.imports file padta hai
        ↓
3. Conditions check karta hai (@ConditionalOnClass, @ConditionalOnMissingBean)
        ↓
4. Agar condition match — auto-configure karo
        ↓
5. Agar aapne khud configure kiya — Spring Boot skip kar deta hai
```

---

### @Conditional Annotations

```java
// Sirf tab configure karo jab ye class classpath mein ho
@ConditionalOnClass(DataSource.class)

// Sirf tab configure karo jab ye bean already na ho
@ConditionalOnMissingBean(DataSource.class)

// Sirf tab jab property set ho
@ConditionalOnProperty(name = "feature.enabled", havingValue = "true")

// Sirf tab jab web application ho
@ConditionalOnWebApplication
```

**Example — DataSource auto-configuration:**
```java
@Configuration
@ConditionalOnClass(DataSource.class)          // JPA dependency hai toh
@ConditionalOnMissingBean(DataSource.class)    // Aapne khud nahi banaya toh
public class DataSourceAutoConfiguration {

    @Bean
    public DataSource dataSource() {
        // application.properties se config read karo
        // DataSource automatically configure karo
    }
}
```

---

### Auto-Configuration debug karo

```properties
# application.properties mein ye add karo
debug=true
```

Console mein dikhaega:
```
============================
CONDITIONS EVALUATION REPORT
============================
Positive matches (auto-configured):
   DataSourceAutoConfiguration - @ConditionalOnClass matched ✅
   
Negative matches (skipped):
   MongoAutoConfiguration - @ConditionalOnClass did not find MongoDB ❌
```

---

### Auto-Configuration override karo

```java
// Aapka khud ka DataSource — Spring Boot ka skip ho jaayega
@Bean
public DataSource dataSource() {
    HikariDataSource ds = new HikariDataSource();
    ds.setJdbcUrl("jdbc:mysql://localhost:3306/mydb");
    return ds;
}
```

> **Convention over Configuration** — Spring Boot ka default use karo, zaroorat ho tab override karo

---

### Interview Questions

**Q1. Spring Boot Auto-Configuration kaise kaam karta hai?**
> `@EnableAutoConfiguration` classpath scan karta hai, `spring.factories` file se auto-config classes load karta hai, `@Conditional` annotations se decide karta hai kya configure karna hai

**Q2. Auto-Configuration disable karna ho toh?**
> `@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})`

**Q3. @ConditionalOnMissingBean kya karta hai?**
> Sirf tab bean banata hai jab us type ka bean already exist na kare — isliye aap override kar sakte ho

**Q4. spring.factories file kya hoti hai?**
> META-INF/spring.factories mein auto-configuration classes listed hoti hain — Spring Boot inhe automatically load karta hai

---

### Summary Table

| Concept | Matlab |
|---|---|
| `@EnableAutoConfiguration` | Auto-config enable karo |
| `@ConditionalOnClass` | Class hai toh configure karo |
| `@ConditionalOnMissingBean` | Bean nahi hai toh configure karo |
| `spring.factories` | Auto-config classes ki list |
| Override | Khud @Bean banao — Boot ka skip |

---

## 12. Spring Profiles

### Profiles kya hote hain?
> Alag alag environments ke liye alag alag configuration — ek hi code, alag config!

```
Development  → Local DB, debug logs, fake email
Testing      → In-memory DB (H2), mock services
Production   → Real DB, minimal logs, real email
```

---

### application.properties — Profile wise

```
src/main/resources/
    application.properties          ← Common config (sab mein)
    application-dev.properties      ← Dev config
    application-test.properties     ← Test config
    application-prod.properties     ← Production config
```

```properties
# application-dev.properties
spring.datasource.url=jdbc:mysql://localhost:3306/devdb
spring.jpa.show-sql=true
logging.level.root=DEBUG

# application-prod.properties
spring.datasource.url=jdbc:mysql://prod-server:3306/proddb
spring.jpa.show-sql=false
logging.level.root=ERROR
```

---

### Profile activate kaise karo?

```properties
# application.properties mein
spring.profiles.active=dev
```

```bash
# Command line se
java -jar myapp.jar --spring.profiles.active=prod

# Environment variable se
export SPRING_PROFILES_ACTIVE=prod
```

---

### @Profile — Bean sirf specific profile mein

```java
// Sirf dev profile mein ye bean banega
@Component
@Profile("dev")
public class MockEmailService implements EmailService {
    public void sendEmail(String to, String body) {
        System.out.println("FAKE EMAIL to: " + to); // Actually send nahi karta
    }
}

// Sirf prod profile mein ye bean banega
@Component
@Profile("prod")
public class RealEmailService implements EmailService {
    public void sendEmail(String to, String body) {
        // Actually email bhejta hai
        smtpClient.send(to, body);
    }
}
```

> Spring automatically sahi bean inject karega profile ke hisaab se — code change nahi karna!

---

### Multiple Profiles

```java
@Component
@Profile({"dev", "test"})  // Dev aur Test dono mein
public class H2DataInitializer { }

@Component
@Profile("!prod")  // Production CHHOD KE sab mein
public class DevToolsConfig { }
```

---

### @ActiveProfiles — Testing mein

```java
@SpringBootTest
@ActiveProfiles("test")  // Test profile use karo
public class UserServiceTest {
    // application-test.properties use hogi
}
```

---

### Interview Questions

**Q1. Spring Profiles kab use karte hain?**
> Jab alag environments (dev/test/prod) mein alag configuration chahiye — DB URL, logging level, external services

**Q2. Profile kaise activate karte hain?**
> `spring.profiles.active=prod` properties mein, ya command line `--spring.profiles.active=prod`, ya environment variable

**Q3. @Profile("!prod") kya matlab?**
> Production CHHOD KE baaki sab profiles mein ye bean banega

**Q4. Default profile kya hota hai?**
> Koi profile active na ho toh `default` profile use hoti hai — `application.properties` load hoti hai

---

### Summary Table

| Concept | Matlab |
|---|---|
| Profile | Environment-specific config |
| `application-dev.properties` | Dev profile ki config |
| `spring.profiles.active` | Profile activate karo |
| `@Profile("dev")` | Sirf dev mein ye bean bano |
| `@Profile("!prod")` | Prod chhod ke sab mein |
| `@ActiveProfiles` | Test mein profile set karo |

---

## 13. Spring Testing

### Testing kyun zaroori hai?
> Code likha — kaam karta hai guarantee nahi. Tests likho — har baar guarantee!

```
Unit Test      → Ek class/method test karo (fast, isolated)
Integration Test → Multiple layers saath test karo (slow, realistic)
```

---

### Dependencies

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
    <!-- JUnit 5, Mockito, AssertJ sab include hain -->
</dependency>
```

---

### Unit Test — @ExtendWith(MockitoExtension.class)

```java
@ExtendWith(MockitoExtension.class)  // Spring context load nahi hota — fast!
class UserServiceTest {

    @Mock
    private UserRepository userRepository;  // Fake repository

    @InjectMocks
    private UserService userService;  // Real service, fake dependencies ke saath

    @Test
    void getUserById_success() {
        // GIVEN — setup karo
        User mockUser = new User(1L, "Rahul", "rahul@gmail.com");
        when(userRepository.findById(1L)).thenReturn(Optional.of(mockUser));

        // WHEN — method call karo
        User result = userService.getUserById(1L);

        // THEN — verify karo
        assertThat(result.getName()).isEqualTo("Rahul");
        verify(userRepository, times(1)).findById(1L);
    }

    @Test
    void getUserById_notFound_throwsException() {
        when(userRepository.findById(99L)).thenReturn(Optional.empty());

        assertThrows(RuntimeException.class,
            () -> userService.getUserById(99L));
    }
}
```

---

### Integration Test — @SpringBootTest

```java
@SpringBootTest  // Poora Spring context load hota hai
@AutoConfigureMockMvc  // MockMvc automatically configure hoti hai
class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;  // HTTP requests simulate karo

    @Autowired
    private UserRepository userRepository;

    @Test
    void getAllUsers_returnsOk() throws Exception {
        mockMvc.perform(get("/api/users")
                .contentType(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.length()").value(0));
    }

    @Test
    void createUser_returnsCreated() throws Exception {
        String userJson = """
            {"name": "Rahul", "email": "rahul@gmail.com"}
            """;

        mockMvc.perform(post("/api/users")
                .contentType(MediaType.APPLICATION_JSON)
                .content(userJson))
                .andExpect(status().isCreated())
                .andExpect(jsonPath("$.name").value("Rahul"));
    }
}
```

---

### @MockBean — Spring Context mein mock inject karo

```java
@SpringBootTest
@AutoConfigureMockMvc
class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean  // Spring context mein real bean ki jagah mock lagao
    private UserService userService;

    @Test
    void getUser_returnsMockedUser() throws Exception {
        User mockUser = new User(1L, "Rahul", "rahul@gmail.com");
        when(userService.getUserById(1L)).thenReturn(mockUser);

        mockMvc.perform(get("/api/users/1"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.name").value("Rahul"));
    }
}
```

---

### Repository Test — @DataJpaTest

```java
@DataJpaTest  // Sirf JPA layer test karo — H2 in-memory DB use hoti hai
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    void findByEmail_returnsUser() {
        // GIVEN
        User user = new User(null, "Rahul", "rahul@gmail.com");
        userRepository.save(user);

        // WHEN
        User found = userRepository.findByEmail("rahul@gmail.com");

        // THEN
        assertThat(found).isNotNull();
        assertThat(found.getName()).isEqualTo("Rahul");
    }
}
```

---

### Test Annotations Quick Reference

```java
@Test               // Ye method ek test hai
@BeforeEach         // Har test se pehle run karo
@AfterEach          // Har test ke baad run karo
@BeforeAll          // Sab tests se pehle ek baar
@AfterAll           // Sab tests ke baad ek baar
@Disabled           // Ye test skip karo
@ParameterizedTest  // Same test alag alag data se chalao
```

```java
@BeforeEach
void setUp() {
    userRepository.deleteAll(); // Har test fresh start se
}

@ParameterizedTest
@ValueSource(strings = {"rahul@gmail.com", "john@yahoo.com"})
void validEmail_test(String email) {
    assertThat(emailValidator.isValid(email)).isTrue();
}
```

---

### Interview Questions

**Q1. Unit test aur Integration test mein kya difference hai?**
> Unit test — ek class izolate karke test karo, fast, Mockito use karo. Integration test — poora system saath test karo, slow, `@SpringBootTest` use karo

**Q2. @Mock vs @MockBean kya difference hai?**
> `@Mock` — pure Mockito, Spring context nahi hota. `@MockBean` — Spring context mein real bean ki jagah mock inject karta hai

**Q3. @DataJpaTest kab use karte hain?**
> Sirf Repository layer test karni ho — fast, H2 in-memory DB use hoti hai, poora Spring context load nahi hota

**Q4. MockMvc kya hota hai?**
> HTTP requests simulate karne ke liye — actual server start kiye bina Controller layer test kar sakte ho

**Q5. Given-When-Then pattern kya hai?**
> Test structure — Given (setup), When (action), Then (assert). Code readable rehta hai

---

### Summary Table

| Annotation | Kaam |
|---|---|
| `@ExtendWith(MockitoExtension)` | Pure unit test — no Spring |
| `@SpringBootTest` | Full integration test |
| `@DataJpaTest` | Sirf JPA/Repository test |
| `@AutoConfigureMockMvc` | MockMvc auto setup |
| `@Mock` | Mockito fake object |
| `@InjectMocks` | Real class with mocks |
| `@MockBean` | Spring context mein mock |
| `MockMvc` | HTTP requests simulate karo |
| `@ActiveProfiles("test")` | Test profile use karo |

---

## 🎯 Quick Revision Cheatsheet

| Concept | Ek Line Mein |
|---|---|
| IoC | Control Spring ko do |
| DI | Spring dependencies inject karta hai |
| Bean | Spring-managed object |
| @Autowired | Spring ko bolo — ye inject karo |
| @PostConstruct | Injection ke baad run karo |
| @PreDestroy | Destroy se pehle cleanup karo |
| Singleton | Ek hi object — sab share karein |
| Prototype | Har baar fresh object |
| Constructor Injection | Best practice for DI |
| @Component | Generic bean |
| @Service | Business logic bean |
| @Repository | DB bean + exception translation |
| @RestController | @Controller + @ResponseBody |
| @Primary | Default bean jab 2 same type hon |
| @Qualifier | Specific bean manually choose karo |
| @Value | application.properties se value inject karo |
| AOP | Cross-cutting concerns alag karo |
| @Aspect | AOP class |
| @Before / @After | Method se pehle / baad mein |
| @Around | Pehle bhi baad bhi — full control |
| BeanFactory | Basic container — sirf beans deta hai |
| ApplicationContext | Advanced container — production use |
| Eager Loading | App start pe sab beans ready |
| @Lazy | Specific bean ko lazy karo |
| @Entity | Java class = DB Table |
| JpaRepository | CRUD free milta hai |
| @Transactional | Sab success ya sab rollback |
| @Query | Custom SQL/JPQL likhna |
| N+1 Problem | Har row ke liye alag query — avoid karo |
| DispatcherServlet | Spring MVC ka Front Controller |
| @PathVariable | URL path se value lo |
| @RequestParam | Query string se value lo |
| @RequestBody | JSON → Java object |
| @ControllerAdvice | Global exception handler |
| ResponseEntity | HTTP status + body control |
| Authentication | Login verify — kaun ho tum |
| Authorization | Permission check — kya kar sakte ho |
| JWT | Stateless token-based auth |
| BCrypt | Password hashing — one way |
| @PreAuthorize | Method level security |
| @EnableAutoConfiguration | Auto-config enable karo |
| @ConditionalOnClass | Class hai toh configure karo |
| @ConditionalOnMissingBean | Bean nahi hai toh configure karo |
| Profile | Environment-specific config |
| @Profile("dev") | Sirf dev mein ye bean bano |
| spring.profiles.active | Profile activate karo |
| @SpringBootTest | Full integration test |
| @DataJpaTest | Sirf JPA layer test |
| @MockBean | Spring context mein mock inject |
| MockMvc | HTTP requests simulate karo |

---

## ✅ Topics Complete!

```
✅ 1.  Spring Core vs Boot
✅ 2.  IoC & Dependency Injection
✅ 3.  Bean Lifecycle
✅ 4.  Bean Scopes
✅ 5.  Spring Annotations
✅ 6.  AOP
✅ 7.  ApplicationContext & BeanFactory
✅ 8.  Spring Data JPA
✅ 9.  Spring MVC
✅ 10. Spring Security
✅ 11. Auto-Configuration
✅ 12. Profiles
✅ 13. Testing
```

---

*Notes banaye gaye hain: Spring Core + Boot Interview Preparation ke liye*  
*Ye notes interview ke liye complete hain — All the best! 🚀*
