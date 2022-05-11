# The Future of Java

2022-05-11 09:00
Holly Cummins
RedHat Quarkus
Java Champion

Holly is stepping in after Simon Maple got Covid. Holly then stole Mark's content that he's supposed to present on Thursday.

Mark has been delivering this talk for a very long time. Since 2012. Copying that content would have been, interesting.

## Java's Past

- 1999
    - Java in a nutshell was a small book. 10 years later it was the work's of Shakespeare. Both a strength and a weakness.
- 2005
    - Designed to run on-prem
    - On dedicated hardware
    - Long-lived processes
    - No need to care how much room it took, just how long it took.
- 2013
    - The cloud broke this model.
    - Containerisation, in particular Docker, changed how we write software.
    - This led to the ideal conditions for the microservices design pattern.
    - The container is what changed Java. No need to go in and try and reconfigure the server. Your servers are immutable. And your servers are disposable (or are turned off and on again in new configs all the time).Because of this we need Java to be:
        - Fast
        - Actually work (idempotency)
- 2017
    - Holly was nominated as a Java Champion, but she wasn't really working on Java any more.
        - Since 2015 she'd mostly been working in NodeJS
- 2022
    - Java has really changed.
        - Fast start time
        - Functional(ish)
        - Expressive
        - Greater developer experience
    - The rapid release cadence, with new versions every 6 months has really allowed for these changes.
        - var, actually useful NullPointerExceptions
    - Java has shifted to the cloud.
    - Modern Java REST app, 13 MB footprint, 0.014s startup time. (RedHat Quarkus
    - These improvements were done with the idea of increasing density in the cloud. But those requirements also made the developer experience a lot better.
    - Kubernetes Native Java
        - Performance (high density)
        - Opertions (declarative in configuration)
        - Frictionless (developer experience)
    - JVM + JDK isn't the only thing that makes Java
        - Binary distributions - OpenJDK
        - Frameworks
            - Can make things smaller, peeling out only what we need to deploy
            - Can also bloat but that's a given
    - Lately
        - cross-release compatibility
        - stability
        - performant
        - cross-vendor co-operation
        - rapid iteration
        - cloud native
        - kubernetes-native

Java has been predicted to die for a long time. But it's usage is still at the top. However, on cloud, only 6.1% of serverless applications are Java. Almost everything else is NodeJS (62.9%).

Java servers are not at all like light switches (historically). And that's a problem.

---

Simon Maple' talk was recorded and will be played at the end of the conference.