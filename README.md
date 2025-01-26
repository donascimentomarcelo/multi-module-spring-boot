# How to implement multi-module in Spring Boot

I designed this project after facing challenges while setting up Spring modules. To address those issues, I decided to create a proof of concept (POC) to document every step.

### Requirements

- Java 21
- Spring Boot 3.4.x

### Project Structure

I designed my project structure with five modules: the root project (parent module), the shared module (which contains classes shared among other modules), and three application-specific modules (app1, app2, and app3, all of which depend on the shared module).

    root/
    ├── pom.xml
    ├── shared/
    │   └── pom.xml
    ├── app1/
    │   └── pom.xml
    ├── app2/
    │   └── pom.xml
    └── app3/
        └── pom.xml