# REST in Peace. Long live gRPC!

2022-05-12 14:10
Mario-Leander Reimer
@LeanderReimer
https:://lreimer.github.io
Principal Software Architect @ QAWare

*Uses Tilt to run a bunch of microservices locally*
github.com/lreimer/from-rest-to-grpc

## Introduction

"One cannot **not** communicate"

Even if you don't speak, you're communicating something. It applies to computing. We always have some form of communication going on with the systems that we develop. With distributed systems, we need these things to talk to each other.

## Inter Process Communication

1976 - RPC proposed
1983 - RPC implementation working
1991 - CORBA 1.0 - Interface definition language generating skeletons, stubs, clients in various languages
1996 - HTTP/1.0
1996 - CORBA 2.0
1996 - DCOM
1997 - Java RMI
1998 - XML-RPC
1999 - CORBA 2.3
1999 - HTTP 1.1
2000 - REST
2002 - CORBA 3.0
2003 - SOAP 1.2
2014 - RESTful Applications
2015 - HTTP 2.0
2016 - gRPC 1.0

## REST APIs

REST APIs - "Pretty URLs with JSON payloads" - @hhariri

We have verbs on the protocol and nouns in the URL. The request comes in. Then we provide our response.

The reality is that we often want more than resources.

Despite the Richardson REST Maturity Model (see [other talk](6-rest-next-level-hateoas.md)) very few people are out there using Level 3 APIs. They're complex to build so people tend to do level 1/2. So REST gets abused for use cases that should be RPC.

When developers ask why we want REST it's usually for "readability". But looking at it, it's not. ISO 25010 never mentions the words "readability". We want efficiency, correctness etc. but not readability.

### The 8 Fallacies of Distributed Computing

2. Latency is zero
3. Bandwidth is infinite
7. Transportation cost is zero

With REST to get all the resources to implement our use cases, the calls often stack up. This means that we end up having quite inefficient use cases of our APIs.

## Protocol Buffers

- Like XML or JSON - but smaller, faster and easier.
- Language and platform neutral
- Extensible
- Efficient binary format
- Data structures are defined in an interface definition language and code is generated from those IDLs into objects.

See photo of code

What's on the wire is no longer readable but there are tools that help.

## JSON vs Protobuf Performance

Protobuf on a non-compressed environment had requests taking 78% less time than JSON requests. The binary format performed almost 5 times faster than the text format.

Protobuf on a compressed enviroment performed 6 times faster, taking only 25ms to handle requests that took 150ms on a JSON format.

## gRPC in a Nutshell

A modern high performance open source universal RPC framework.

Uses HTTP 2 as a transport protocol. It includes multiplexing (multiple calls on same connection), TLS and compression inbuilt. 

Supports

- Classic request/response
- Streaming (uni and bi directional)

Uses Protobuf IDL to define services and payload messages

Client and Server stubs are generated from IDL easily for several languages.

Supports various load balancing options: proxy, client-side etc.

See photos for example proto file

There is a tool called `grpcurl` which lets you interact with gRPC APIs. It will also output for you in a nice JSON output.

## What about the REST fans?

The world might not be ready for my cool gRPC API. But I don't want to implement it.

So let's generate a matching API for our clients.

The gRPC ecosystem comes into play.

- gRPC Gateway (Currently only supporting Go)
- gRPC Web

Supports load balancing options with Nginx and Envoy.

In the gRPC proto you can specify how your methods map to a HTTP 1 REST API. This is the gRPC Gateway.

See photo

gRPC Web uses the Web Envoy filter. This filter can forward requests to your gRPC server with a proxy component.

Then in our JS code we can use a grpc-web plugin to generate your client. This is still kind of experimental but it is possible to do staight up gRPC with web clients.

## Questions

How do you do security? Are there libraries like Spring security?

- You would configure Auth etc. after the fact. 

Compatibility

- Each and every proto definition has fields with an index.
- Those indexes can't change. If you extend your API you need to use higher numbers.
- If you stick to the three proto rules, the old client will still be able to unmarshall the new requests.

API design wise do you follow classic design 

- This example was pretty CRUD but based on the business use case we can specify those contracts to match

Whats the state of contract based testing for gRPC?

- Pact has added support for it. There are other tools that let you lock and version proto definitions that make that safe and easy to use
