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
