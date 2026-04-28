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
