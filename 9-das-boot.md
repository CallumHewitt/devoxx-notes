# Das Boot: Diving Into Debugging Spring Boot Applications

2022-05-11 16:00
Mark Heckler
Principal Cloud Advocate, Java/JVM Languages
Ex-Spring Pivotal
markheckler@microsoft.com
mark@thehecklers.com
@mkheck

## Introduction

We want to deal with verifiable truths as opposed to assumptions when we're debugging.

Java Champion
Author: Spring Boot Up & Running

"To achieve great things, two things are needed: a plan, and not quite enough time" - Leonard Bernstein

## Spring Boot is too much magic

Common complaint. People coming from much more imperative backgrounds it can be a bit JARring.

`@SpringBootApplication`. Drilling in using IntelliJ we can see:

- @SpringBootConfiguration -> an alias for the Configuration class, which is an alias for @Component.
    - This makes the class a Bean so it will be injected into the spring context.
- @EnableAutoConfiguration -> this is the jarring part. In the java EE world you need to set up you connections, with a lot of boiler plate, in order to get your application to work. You do it so many times that it feels normal. Auto configuration will look for things on your classpath and assume that you want to use it. When paired with other information it can auto-configure beans for us.
- @ComponentScan -> Define classes and annotate them as @Component or one of it's aliases to let Spring to know that this should be a bean in the application context.

## Dependency Versions in Starters

Spring Boot starters have been tested extensively against all of it's dependencies. It then makes a decision as to whether it should bring in the dependencies on the starter based on defined criteria.

## Creating beans

We can create our beans inside the class annotated with @SpringBootApplication using the @Bean annotation. Or any of the @Component annotations on a class in the same file.

## Defining Properties

By default application.properties will fill in any property names.

If you create an application.yml file alongside an application.properties file, Spring will 

## Spring Actuator

Actuator exposes endpoints for your application. It has health and info endpoints by default. It can be very dangerous in production but as a developer they're invaluable. You can secure them but for development...

```properties
# DO NOT DO THIS AT HOME. BAD BAD BAD BAD BAD BAD BAD!
management.endpoints.web.exposure.include=*
```

Going to the API /actuator we can see all of the available endpoints.

/actuator/env shows the properties we have in the context. It also shows from what files the properties are coming from.

## More properties

```console
export BEANNAME=F
export SPRING_APPLICATION_JSON='{"beanname":"W"}'
```

Again actuator here is invaluable. It shows where our properties are coming from and being set.

The docs.spring.io docs will actually explain how these properties get resolved but actuator is useful in showing what's actually happening.

## Beans spring makes

We might have only defined one bean but Spring will create many many more in the background. You can see in the debugger when looking at the application context object.

## Remote Debugging

On many times you won't be able to debug what's on your machine. You'll need to debug what's on your machine or container.

In IntelliJ -> new Run Config -> Remote Debug

Follow the instructions in the IntelliJ docs...

Requires adding the "-agentlib" arguments to your application runscript.