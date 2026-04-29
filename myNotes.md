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
9. [Spring MVC](#9-spring-mvc) *(Coming Soon)*
10. [Spring Security](#10-spring-security) *(Coming Soon)*

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

> 🚧 *Coming Soon — Next Topic*

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

---

*Notes banaye gaye hain: Spring Core Interview Preparation ke liye*  
*Topics covered: Spring Core vs Boot, IoC & DI, Bean Lifecycle, Bean Scopes, Annotations, AOP, ApplicationContext, Spring Data JPA, Spring MVC*  
*Aage: Spring Security*
