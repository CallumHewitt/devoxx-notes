# Spring Cloud Gateway

2022-05-12 10:00
Thomas Vitale
Software Architect @ Systematic
@vitalethomas
Author of Cloud Native Spring in Action
thomasvitale.com

## API Gateway

Scenarios

- Different clients need different APIs
    - From the client we want to decouple the backend from the client to ensure compatibility and avoid breaking existing functionality
    - It shields the clients from backend changes
- Strangling the Monolith
    - We identify parts we want to extract to microservices without disrupting the current business.
- Unified interface for microservices
    - With distributed systems clients would need to keep track of all of the services. So we provide a gateway
- Cross-cutting concerns in distributed systems
    - Security, Resilience, Observability

## Example System

Users for a public library. They call the edge service, handling all of the cross-cutting concerns. The edge service connects to the underlying microservices for accounts, books and loans information.

## Reactive Spring

We're talking about introducing a new service which is:

- Adding something new that we have to maintain between the clients and the backend services
- We don't want to make this a single point of failure
- We need to consider capacity and we want to avoid making it a bottleneck

The reactive programming paradigm is used to avoid this. We don't allocate a thread per request. We want to decouple from the thread pool.

So we switch instead to an event loop model. We schedule events and register callbacks. The event loop is just a few threads processing multiple requests.

This is reactive spring and is what Spring Cloud Gateway is based on.

## Routing

The first function of a gateway is routing.

The Spring Cloud gateway can be created from spring initializr and we just add the Spring Cloud gateway starter.

You can either do this with properties or with the Java DSL. With properties, this looks like:

```yml
spring:
  application:
    name: edge-service
  cloud:
    gateway: 
      routes: 
      - id: book-route
        uri: ${BOOK_SERVICE_URI:http://localhost:9001}
        predicate:
        - Path=/books/**
          filters: 
            - AddRequestHeader=X-Tenant,acme
            - AddResponseHeader=X-Genre,fantasy
```

The predicate is used to determine if this route is the correct route for this request to the gateway.

The filters can be used to modify the request or the response returned.

## Observability

We need to be able to observe how the requests are routing through our systems, both in dev and production.

By default the log print to stdout which is what we want on a cloud native system.

We can also add:

- Spring Boot Actuator - Health, Metrics, Flyway, Thread Dumps, Heap Dumps
- Spring Cloud Sleuth (Micrometer Tracing) - Distributed tracing, Instrumentation, OpenZipkin and OpenTelemetry

Using Grafana, an open-source observability stack, we can view this telemetry.

Grafana -> Tempo, Prometheus/Cortex, Grafana Loki.

## Resilience

Via filters we can implement resilience and security patterns

We want our system to be fault tolerant.

### Retry

This can be a dangerous pattern. We need to think about what we are retrying. Get requests are idempotent.

The retries wait for longer delays between each retry to give the target service time to recover.

If all the attempts fail, we need to consider:

- Returning a friendly message to the user
- Returning cached data instead

```yaml
filters:
  - name: Retry
    args:
      retries: 3
      method: GET
      backoff:
        firstBackoff: 50ms
        maxBackoff: 500ms
```

This filter only allows retries for GET requests. We wait more and more between each attempt. The delays increase between 50ms and 500ms between each retry.

### Request Rate Limiter

We can configure this per user if we wanted to. This means we can do it for resilience or even to implement certain business requirements.

The model is based on one from [Stripe](https://stripe.com/blog/rate-limiters). It's a token based rate limiter. Each request costs a token and once out of tokens the user can only send requests after the bucket of tokens refills.

This means storing state, and we want stateless services so we can use Redis.

```build.gradle
// This might not be right. It might be redis-reactive-rate-limiter or something
implementation 'org.springframework.boot:spring-boot-start-redis-reactive'
```

```yaml
default-filters:
  - name: RequestRateLimiter
    args:
      redis-rate-limiter.replenishRate: 10
      redis-rate-limiter.burstCapacity: 20
      redis-rate-limiter.requestedTokens: 1
```

1 tokens per request. At most 20 requests. Every second 10 more tokens.

Doing this will send back extra headers, showing how many requests you have left etc. This can be dangerous so these headers can also be disabled.

### Circuit Breaker

If our target service, eg. book service, becomes unavailable we need to stop sending requests to it in order to avoid propagating the issue. Shielding the system from error.

There are three states. 

Closed - All is well. All requests are forwarded to the target service.

If the failure rate goes above a certain threshold we go to:

Open - No requests are allowed to pass. It returns a response directly to the user.

After a wait duration we attempt a reset. We check to see if the service is up and running.

Half-Open - We allow a few requests to go to the service. 

If the failure rate is above a threshold we go back to Open. Otherwise, we go back to Closed.

```build.gradle
// Add the Resilience4J library starters
```

```yaml
filters:
  - name: CircuitBreaker
    args:
      name: bookService
      fallbackUri: forward:/books-fallback
resilience4j:
  - see photos for spec
```

### Time Limiter

We can do this to let us know where a request is completing in good time. This is also in Resilience4J

## Security

### User Authentication

We can do this directly in the edge service using the Spring Security libraries, or a dedicated authentication server. If we delegate the authentication to an external service we need to decide which strategy, which protocol and the data format to use.

One common pattern is OAuth2 + OIDC.

How can we propagate the authenticated user security content through the system?

We can use a Token Relay with OAuth2. The Session Cookie stays between the user's browser and the edge service. Which maintains a relationship between the session and an access token. Then access token is used for the other services.

In the Edge service we add the OAuth2 Client starter. And the OAuth2 Resource Server is added on the target services.

On the target service:

```yaml
security:
  oauth2:
    resourceservice:
       jwt:
         issuer-uri: ${KEYCLOAK_URI}
```

On the edge service:

See pictures

This specific example is using a Keycloak authentication server.

With this implementation we're asked to login and auth with whatever strategy we like before we get our data.

## Questions

ZK discovery for cloud gateway?
Why have a half-open state? Can we specify certain logic to do while in that state? What does the service do differently that when in an open state?

