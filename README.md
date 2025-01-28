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

### Get Started

At first, let's download all five projects via spring initializer.

#### - root module

Fist of all, we will set up the root project, and it must be like this:

    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <parent>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>3.4.2</version>
            <relativePath/> <!-- lookup parent from repository -->
        </parent>
        <packaging>pom</packaging>
        <groupId>br.com.root</groupId>
        <artifactId>root</artifactId>
        <version>0.0.1</version>
        <name>root</name>
        <description>Root</description>
            <modules>
                <module>shared</module>
                <module>app1</module>
                <module>app2</module>
                <module>app3</module>
            </modules>
            <properties>
                <java.version>21</java.version>
                <spring.boot.version>3.4.2</spring.boot.version>
            </properties>
    </project>

The packing must use the POM type because it will not generate a .jar

    <packaging>pom</packaging>

We need to set up all modules we will use on the project

    <modules>
        <module>shared</module>
        <module>app1</module>
        <module>app2</module>
        <module>app3</module>
    </modules>

#### - shared module

The shared module must follow the pom below:

    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <parent>
            <groupId>br.com.root</groupId>
            <artifactId>root</artifactId>
            <version>0.0.1</version>
            <relativePath>../pom.xml</relativePath>
        </parent>
        <groupId>br.com.api</groupId>
        <artifactId>shared</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <name>shared</name>
        <description>Shared App</description>
        <properties>
            <java.version>21</java.version>
        </properties>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-autoconfigure</artifactId>
            </dependency>
        </dependencies>
    </project>

# important

Do not forget to remove the `spring-boot-maven-plugin`, otherwise this plugin will take the JAR existing JAR and
repacking it. The result puts your classes in a location where Spring Boot can find them, but not the regular class
loading mechanism.

#### - app1, app2, app3 module

All of them, must follow the same structure, importing the shared dependency and pointing the parent tag to the root 
project.

    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
        <parent>
            <groupId>br.com.root</groupId>
            <artifactId>root</artifactId>
            <version>0.0.1</version>
            <relativePath>../pom.xml</relativePath>
        </parent>
        <groupId>br.com.api</groupId>
        <artifactId>app1</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <name>app1</name>
        <description>App 1</description>
        <properties>
            <java.version>21</java.version>
        </properties>
        <dependencies>
            <dependency>
                <groupId>br.com.api</groupId>
                <artifactId>shared</artifactId>
                <version>0.0.1-SNAPSHOT</version>
            </dependency>
            ... your dependencies
        </dependencies>
        
        <build>
            <plugins>
                <plugin>
                    ... your plugins
                </plugin>
            </plugins>
        </build>
    </project>

And that's all! We can test only one module with `mvn clean install -pl shared,app1 -DskipTests` or you can run all 
projects running `mvn clean install -DskipTests`.

The result must be like this:

    [INFO] Reactor Summary:
    [INFO]
    [INFO] root 0.0.1 ......................................... SUCCESS [  0.222 s]
    [INFO] shared 0.0.1-SNAPSHOT .............................. SUCCESS [  1.512 s]
    [INFO] app1 0.0.1-SNAPSHOT ................................ SUCCESS [  2.369 s]
    [INFO] app2 0.0.1-SNAPSHOT ................................ SUCCESS [  1.644 s]
    [INFO] app3 0.0.1-SNAPSHOT ................................ SUCCESS [  1.514 s]
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  7.637 s
    [INFO] Finished at: 2025-01-27T21:27:38-03:00
    [INFO] ------------------------------------------------------------------------

🖥️💻 ️Your code is mine!