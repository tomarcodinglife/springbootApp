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

