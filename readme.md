# 📘 Spring Framework Complete Roadmap.

---

## 🟢 Spring Framework Core

### Introduction
- Overview of Spring Framework
- Why Spring is used

### Message From Faisal
- Course overview / motivation

### Tight and Loose Coupling
- Tight Coupling: Direct dependency between classes
- Loose Coupling: Uses abstraction (interfaces)

### Dependency Injection (DI)
- Inject dependencies instead of creating them
- Types:
  - Constructor Injection
  - Setter Injection

### Intro to Web Framework & Spring Framework
- What is a Web Framework?
- Why Spring?

### Spring Concepts
- IOC (Inversion of Control)
- Beans
- Application Context

### Setting up a Spring Project
- Using Maven / Gradle
- Project structure

### Build Tools: Maven & Gradle
- Dependency management
- Build lifecycle

### Creating Your First Bean
- Define and use a bean

### Challenge: Manual DI → Spring DI
- Convert traditional object creation to Spring

### IOC, DI & Problem Spring Solves
- Removes tight coupling
- Improves maintainability

---

## 🟢 Annotations & Layered Architecture

### Problems with Manual Config
- XML complexity
- Maintainability issues

### Core Annotations
- `@Component`
- `@ComponentScan`
- `@Configuration`

### @Autowired
- Automatic dependency injection

### Stereotype Annotations
- `@Service`
- `@Repository`
- `@Controller`

### Multiple Implementation Problem
- Solutions:
  - `@Primary`
  - `@Qualifier`

### Bean Lifecycle
- Initialization → Usage → Destruction

### Mixing @Bean & @Component
- Java config + Annotation config

### Layered Architecture
- Controller → Service → Repository

---

## 🟢 Spring Boot Introduction

### What is Spring Boot?
- Auto configuration
- Embedded server

### Creating a Project
- Spring Initializr

### Spring Boot Starters
- Predefined dependencies

### application.properties
- Configuration file

### CommandLineRunner / ApplicationRunner
- Run code at startup

### Interview Questions
- Common concepts

---

## 🟢 First REST API

### Introduction to REST
- REST principles

### Create First API
- Basic controller

### HTTP Methods & Status Codes
- GET, POST, PUT, DELETE

### JSON Response
- Object → JSON

### @RequestMapping
- Map HTTP requests

### Request Flow
- DispatcherServlet → Controller → Service

---

## 🟢 POST, PUT, DELETE

### Data Modification APIs
- CRUD operations

### POST
- Create data

### PUT
- Update data

### DELETE
- Remove data

### ResponseEntity
- Control response & status codes

---

## 🟢 Path Variables & Query Params

### Dynamic URLs
- Passing data in URL

### Path Variables
- `@PathVariable`

### Query Parameters
- `@RequestParam`

### Multiple Parameters
- Handle multiple inputs

### @RequestHeader
- Access headers

### Combine All
- Use together in one API

---

## 🟢 Exception Handling & Logging

### Exceptions in Spring Boot
- Runtime error handling

### Service Layer Structure
- Clean architecture

### Inbuilt Exception Handling
- Default exceptions

### @ExceptionHandler
- Handle specific errors

### Global Exception Handling
- `@RestControllerAdvice`

### Custom Exceptions
- User-defined errors

### Logging
- Levels:
  - INFO
  - DEBUG
  - ERROR

---

## 🟢 JDBC Fundamentals

### Why JDBC?
- Understand database basics

### Setup with MySQL
- Add dependency

### Database Connection
- Establish connection

### Try-with-Resources
- Auto close resources

### CRUD Operations
- INSERT
- SELECT
- UPDATE
- DELETE

---

## 🟢 Transactions & Prepared Statements

### PreparedStatement
- Prevent SQL Injection

### Transactions
- Group operations

### Commit & Rollback
- Maintain consistency

---

## 🟢 Hibernate & ORM

### ORM Introduction
- Object Relational Mapping

### JPA vs JDBC
- Abstraction vs manual SQL

### Hibernate Architecture
- Session
- SessionFactory

### Hibernate Project
- CRUD operations

---

## 🟢 Spring Boot + JPA Integration

### JPA Deep Dive
- Advanced concepts

### EntityManager
- Core JPA operations

### Spring Boot Integration
- Combine with Spring Boot

### Layered Structure
- Controller → Service → Repository

---

## 🟢 JPA Relationships

### Why Relationships?
- Connect tables

### One-to-One
- Single mapping

### One-to-Many / Many-to-One
- Parent-child relation

### Many-to-Many
- Multiple connections

### Cascading
- Auto save/update/delete

### Level Up
- Advanced mappings

---

## 🚀 Final Goal
- Build production-ready Spring Boot applications
- Master REST APIs, JPA, and backend architecture
