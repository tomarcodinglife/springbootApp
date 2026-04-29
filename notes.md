# Strategy Design Pattern
Strategy Design Pattern ek behavioral design pattern hai jo allow karta hai ki tum algorithm ko runtime par change kar sako.
Simple words: Same kaam, multiple tareeke → aur tum choose kar sakte ho kaunsa use karna hai

##Real Life Example
###Payment system 💳
* Credit Card
* UPI
* PayPal
Payment same hai, but method different

## Without Strategy Pattern (Bad Code ❌)
```java
class PaymentService {
    public void pay(String type) {
        if(type.equals("credit")) {
            System.out.println("Paid using Credit Card");
        } else if(type.equals("upi")) {
            System.out.println("Paid using UPI");
        }
    }
}

```
Problem:
Har naya method → code change karna padega
Tight coupling

## With Strategy Pattern (Good Code ✅)
### Step 1: Interface

```java
interface PaymentStrategy {
    void pay();
}
```

### Step 2: Different Strategies

```java
class CreditCardPayment implements PaymentStrategy {
    public void pay() {
        System.out.println("Paid using Credit Card");
    }
}

class UPIPayment implements PaymentStrategy {
    public void pay() {
        System.out.println("Paid using UPI");
    }
}

```
### Step 3: Context Class

```java
class PaymentService {
    private PaymentStrategy strategy;

    public PaymentService(PaymentStrategy strategy) {
        this.strategy = strategy;
    }

    public void processPayment() {
        strategy.pay();
    }
}
```

### Use

```java
public class Main {
    public static void main(String[] args) {
        PaymentStrategy strategy = new UPIPayment();
        PaymentService service = new PaymentService(strategy);
        service.processPayment();
    }
}
```

Benefits
Easy to extend (new strategy add karo)
Code clean & maintainable
Open/Closed Principle follow karta hai


# Tight Coupling vs Loose Coupling

## Tight Coupling (Bad ❌)
👉 Jab ek class dusri class par strongly dependent hoti hai
```java
class Engine {}

class Car {
    Engine engine = new Engine(); // direct dependency
}
```
Problem:
* Change karna mushkil
* Testing hard
* Reusability low

## Loose Coupling (Good ✅)
👉 Jab dependency indirect / flexible hoti hai (interface use karke)

```java
interface Engine {}

class PetrolEngine implements Engine {}
class DieselEngine implements Engine {}

class Car {
    Engine engine;

    Car(Engine engine) {
        this.engine = engine;
    }
}
```

Ab easily change kar sakte h
```java
Car car = new Car(new PetrolEngine());
```


# Spring Bean kya hota hai?
Spring Boot me Bean ek object hota hai jise Spring Framework khud create, manage aur control karta hai.
Jo object Spring container banata aur handle karta hai = Bean

```java
@Component
public class MyService {
    public void show() {
        System.out.println("Hello Bean!");
    }
}
```

Yahan:
* MyService ek Spring Bean hai
* Kyunki humne @Component use kiya

# Spring Container kya karta hai?
Spring ka IoC Container (Inversion of Control):

* Object create karta hai
* Dependencies inject karta hai
* Lifecycle manage karta hai

# Inverse of control (IOC) Kya hai ?
Inversion of Control (IoC) ek design principle hai jo Spring Framework / Spring Boot ka core concept hai. IoC ka matlab hai control ka ulta ho jana.
Matlab:

Normally → object create karte ho ❌
IoC me → Spring object create karta hai ✅

## Without IOC
```java
class MyApp {
    public static void main(String[] args) {
        MyService service = new MyService(); // khud object banaya
        service.doWork();
    }
}

Problem:
Tight coupling (hard dependency)
Code flexible nahi hota

```
## With IoC (Spring Way) 

```java
@Component
class MyService {
    public void doWork() {
        System.out.println("Working...");
    }
}

@Component
class MyApp {

    @Autowired
    MyService service;

}
```

Yahan:

Tumne new keyword use nahi kiya
Spring khud object banake inject kar raha hai

IoC ka main kaam
Object create karna
Dependencies manage karna
Lifecycle control karna

Ye sab Spring Container (IoC Container) karta hai

# Application Context Kya hai?
ApplicationContext Spring Framework ka core container hai — yahi pura game control karta hai.
ApplicationContext = wo object jo Spring ke saare beans ko create, manage aur inject karta hai

## Normal Java - Easy Understanding
```java
Car car = new Car();
```
Spring me:
object khud nahi banate
Spring banata hai aur deta hai

ApplicationContext
* Bean Create karta hai - @Component, @Service, @Bean etc. se objects banata hai
* Dependency Inject karta hai - @Autowired ke through ek class ko dusri class se connect karta hai
* Bean Lifecycle Manage karta hai - Create → Initialize → Destroy
* Configuration Read karta hai - Java config / XML / annotations
* Central Container hai - Saare objects ek jagah managed hote hain

## Real Example 
```java
ApplicationContext context =
        new AnnotationConfigApplicationContext(AppConfig.class);

Car car = context.getBean(Car.class);
car.drive();
```

Yaha:
ApplicationContext = container
getBean() = Spring se object lena

❌Without Spring (Tight Coupling)
```java
Engine engine = new Engine();
Car car = new Car(engine);
```
✅ With Spring (Loose Coupling)
```java
@Autowired
Engine engine;
```
Spring khud inject karega

Types of ApplicationContext
AnnotationConfigApplicationContext → Java config
ClassPathXmlApplicationContext → XML config
WebApplicationContext → Web apps (Spring Boot)

# 🌱 Spring Core + Spring Boot — Interview Preparation

> **Ye notes interview ke liye hain — concepts + code + questions sab ek jagah**

---

## 📌 Table of Contents

1. [Spring Core vs Spring Boot](#1-spring-core-vs-spring-boot)
2. [IoC & Dependency Injection](#2-ioc--dependency-injection)
3. [Bean Lifecycle](#3-bean-lifecycle)
4. [Bean Scopes](#4-bean-scopes)
5. [Spring Annotations](#5-spring-annotations) *(Coming Soon)*
6. [AOP](#6-aop) *(Coming Soon)*

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

> 🚧 *Coming Soon — Next Topic*

---

## 6. AOP (Aspect Oriented Programming)

> 🚧 *Coming Soon*

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

---

*Notes banaye gaye hain: Spring Core Interview Preparation ke liye*  
*Topics aage badhte rahenge — Annotations, AOP, ApplicationContext*
