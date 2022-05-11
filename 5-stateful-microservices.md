# Exploring Stateful Microservices in the Cloud Native World

2022-05-11 10:50
Grace Jansen
Developer Advocate @ IBM

## Cloud Native Application Development

We want to design our applications to take best advantage of the cloud.
Flexibility, scalability etc.

Definitions often say

Cloud-Native == Stateless Microservices

## The 12 Factor Application methodology

Drafted by developers at Heroku. (12factor.net). There's actually an updated 15 factor one.

Survive and thrive advice. One of these is "VI Processes - Execute the app as one or more stateless processes"

## Stateful vs Stateless

Marlin vs Dory

Marlin has an excellent memory and remembers everything. He's slow to action things, thinking about the consequences.
Dory forgets literally everything. She acts as and when she wants, without thinking about consequences.

## Stateless Computing

A communication protocol that does not retain any session information. State is not recorded between transactions. 
Scaling is easier, recovery for system failure is easier.

However, realistically we live in a stateful world.

## Stateful Computing

State of the data gets recorded at every step across all transactions.
Scaling the system is difficult. Recovery from system failure involves a lot of efforts.

## Statefulness in the cloud-less days

Before cloud, "computing" used to be a lot simpler.

We had:

- Stateful database systems on the server side. Database-styled transactions. ACID style. 2-Phase commit (2PC). These type of commits are locking and tend to be quite slow.
- Java - Enterprise Java Beans - Stateful EJB (Session vs Entity beans). Servlet - HTTPSession.
- Client-side caching of server responses - Cookie-based auth, Token-based auth (JWT).

## Stateful Microservices in Cloud Native Environments

Cloud Native is an extension to Cloud Computing (defined by Netflix). It was created to address the 'true needs' of enterprise-level distributed business application systems. The true needs are:

- Highly available
- Scalable
- Performant

How do we do this with state?

## Techniques/Mechanisms

Original ones.

- Caches
- Database-style transactions
- Cookies
    - Sticky cookies are available kubernetes for example.
- Sessions
- Tokens

Plus cloud native infrastructure:

- Leader Election (etcd)
    - Apache Zookeeper
- StatefulSets
    - Kubernetes. Helps manage the state of the data. 
    - Kubernetes manages the workers and manages the state across multiple of these worker nodes.
- PersistentVolume
    - More of a storage level.
    - Kubernetes can do logical assignment of the disk for different components.
- Cookie/Session affinity (sticky session)
    - Built into Kubernetes.
    - Making it so that worker nodes pick up jobs from the same clients each time. Simplifying the auth process for next time.

## Design patterns

- Saga
    - Failure management pattern.
    - Transactions spanning multiple services.
    - BASE
        - Basic
        - Available
        - Soft State
        - Eventual Consistency
    - You have compensating transactions that compensate for failures in the chain.
        - Different compensating strategies.
        - Choreography
            - No central co-ordinator. Each service in the SAGA performs the transaction then publishes events. The next microservice handles the event
        - Orchestration
            - A SAGA co-ordinator manages the state of the complete transaction overall.
- Long-Running Action / Saga Interaction Pattern
    - Uses the compensator model. It's an implementation of SAGA
    - Relaxes ACID to BASE.

## MicroProfile

A community led specification for Enterprise Java Microservices be ready for the cloud. Introduces API and specifications that help.

Tons of vendors are introducing implementations.

LRA is a example of a Long-Running Action definition. Open Liberty is an implementation of that.

https://openliberty.io/blog/2021/01/27/microprofile-long-running-actions-beta.html

@LRA - join/create method that handles any required business logic
@Complete - Used by complete methods after the LRA completes successfully
@Compensate - Used if the LDR has an issue

Interactive lab version:

https://openliberty.io/guides/sessions.html

## What about Reactive?

Reactive Systems and the Reactive Manifesto

https://ibm.biz/reactivereport

Much of this maps back to the Netflix cloud native definition. Stability, Performant, High Availability.

We have to enable these key resilient characteristics, and that doesn't always mean stateless.

## IBM Developer

developer.ibm.com covers all kinds of topics.

## Questions

What about compensation after the service crashes? Especially before eventual consistency.

- This is one of the advantages of the co-ordinator. It will remember that the microservice went down before it could process the payment (for example). This is inherently slower, but it's a trade off. There are times when you need state.

How does the SAGA co-ordinator interact? HTTP, MQ etc.?

- Will check and get back.
