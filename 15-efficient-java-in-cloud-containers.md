# The Diabolical Developer's Guide to running Java *efficiently* in Containers on Cloud Infrastructure

2022-05-11 12:20
Martijn Verburg
Principal SWE Group Manager for Java @ Microsoft
@karianna

## Java at Microsoft

2m JVMs in production at Microsoft.

- LinkedIn - 2000+
- Azure
- Yammer
- Minecraft - Thousands of server, millions of Players
- Android
- Bing and MSN

Because Microsoft are a cloud vendor, much of it runs within containers.

## Cloud Compute and Climate Change

#4/5 as consumers of energy when compared to countries. So we need to make the way Java runs **far** more efficient to reduce energy usage and reach *carbon negative* targets.

## Hardware Resources

Each host *has limited resources* still.

A semi-typical cloud stack has JVMs in Containers, running on a Container Engine, run on a VM. VMs run on Hypervisors which are serving up the VMs. This is then on the Host OS and then on Bare Metal.

This is 6 levels of abstraction on top of your bare metal reducing that bare metal resource that's available. Metal as a Service (MaaS) isn't widely adopted.

A more increasingly common get's rid of the Hypervisor and VMs and then just runs the container engine on top of the host OS.

You need to know which of these two stacks you're getting. You can find this in the docs for your cloud provider.

We need to calculate what you need with headroom. 64GB of RAM **will not** give you 16x8GB VMs that work. Modern hypervisors are clever and shift crap around to let you get away with it but... it's not always right.

## JVM Ergonomics

"Worked fine in Dev, Ops problem now."

Your laptop isn't the container you're deploying too. Your containers might be *far* more constrained that the laptop you're running on. Especially these days.

1. How many GCs are available on a vanilla OpenJDK 11+ distribution
   - More than 4
2. Do you expect the JVM to choose the best GC based on ergonomics?
   - CPU and Memory available
3. How may GCs does the JVM consider when evaluating ergonomics?
   - Only two
4. Do you trust the JVM to pick the best GC?
   - 43.7% trust the JVM when really you shouldn't.
5. Would you like better JVM ergonomics?
   - Yes!

Most devs are deploying JVM workloads in containers with up to 4 CPUs, up to 4GB RAM that are I/O intensive.

On containers it's usually 1 CPU, with 1GB or less RAM and without GC configured. Typical fixes end up being increasing the heap size, having more replicas or migrating to another stack.

### Default settings when no GC is specified

HotSpot JVM / OpenJDK
    - Java 11 or later: SerialGC or G1GC
    - Java 8: SerialGC or ParallelGC
    - Default GC: SerialGC if 1791MB or less memory available.
    - Otherwise, G1GC.

Microsoft have business reasons for why to move from Java 8 to Java 11 that have been published.

## JVM Garbage Collectors

There are 5 GCs that are recommended.

See the photos

Setting Heap size is not enough. You really need to know what your workload is. Have enough CPUs to run the appropriate GC.

## Heap Size Configuration

Recommended starting point

- Servers - set to whatever the application needs
- Containers - Set to whatever the application needs but 75% of the container memory limit.
    - You can go higher, the larger your heap.
    - The larger your containers, the tigher 80% for 8GB for example, as the overhead does not scale.

See photos

Parallel GC often beats out others but is never chosen by default.

## Java on Kubernetes

Kubernetes gives you a timeslice of a CPU, not a single CPU.

1000m does not mean a single vCPU, you get a timeslice of a CPU. The JVM does not see that, it sees it as 1. 2000m means 2 CPUs. This will probably evolve with newer Java versions but not for right now.

If you start using more than you're allocated Kubernetes will start throtling. Java does a lot in the background like GC for example.

So we need to think about the underlying work as well as the business work to avoid that throtling.

Using the flag -XX:ActiveProcessorCount let's you lie to the JVM and say that you have more than there are. Which is useful for enabling certain GCs. Because you have timeslices of CPUs it's actually okay to lie here.

See photo for better Kubernetes starting points.

### Deploying Kubernetes

Do you go short-but-wide or tall-but-narrow. 

See slides from photos.

## Summary

See slides from photos.
