# An Introduction to Redis for Developers

2022-05-12 11:20
Steve Lorello
Developer Adovacate @ Redis
@slorello
github.com/slorello89
slorello.com

## What is Redis?

- REmote DIctionary Server
- NOSQL database
- Completely in memory Key Value Store
- Single Threaded
- Blazingly Fast
- Easy to Use
- Beloved by Developers

For the last three years running it's the most loved database on Stackoverflow.

Redis is ACIDic
- Atomicity
  - Single Threaded.
  - All commands are completed atomically.
  - Transactions & Scripting for group commands.
- Consistency
  - Depends on deployment and config
  - Single instance is always consistent
  - Read-only replication is guaranteed eventual consistency
    - Using the WAIT command enforces consistency
- Isolation
  - Single Threaded
  - Isolated as there's no concurency
- Durability
  - Entire database loaded into memory
  - Two durability models exist with persistence to disk
    - AOF (Append Only File)
      - Each Redis Command writes to AOF (Redis command are in us not ms so this can be a lot)
      - Reconstruct Redis from AOF
      - AOF flush to disk is NOT necessarily synchronous. It's buffered in memory
      - FSYNC config policy determines your level of durability
        - always (sync flush, fully durable, reduced performance)
        - everysec (flushes to disk every second on a background thread)
        - no (OS decides when to write to disk)
    - RDB
      - Snapshots of the database
      - Taken in intervals or by command
      - More compact and faster to ingest than AOF
      - Less strain on OS
      - Happen less frequently so greater potential for data loss

### Redis Standalone

- One Redis Instance
- Simple
- No High Availability capabilities
- No Horizontal scaling

### Redis Sentinel 

- Replication for High Availability
- One primary with a Writable Master and then Read-only Replicas
- Sentinels handle HA and Failover for the group.

The sentinels are not the replicas, each instance has a Sentinel which keeps an eye on it to assist wit the failing over.

### Redis Cluster

- Horizontal scaling in hard mode.
- Keyspace split across multiple shards
- Deterministic hash function on the key determines the slot
- Shards are responsible for groups of slots
- This allows you to horizontally scale the reads and writes across the cluster
- Multi-key operations become slot limited, which is the big trade off.

A primary shard exists with read only replicas on other instances. You always want to have an odd number of instances so that you always have a quarum to elect a new leader.

### Redis Cloud

Have Redis manage your cluster. Scales endlessly.

Other cloud features

- Geo-Distribution with Active-Active (two active primary shards in different regions) than become eventually consistent
- Tremendous throughput
- Five-nines availability
- Multi-cloud product
- Access to Redis Modules
- Enterprise support

From a developer advocate perspective

- They run a Discord server - discord.gg/redis
- Field lots of complex questions about cluster/sentinel from the community
- Write up lots of complex answers with the typical refrain:
    - "Or you could just use Redis Cloud"

## Major Redis Use Cases

As a Cache

- #1 Most Popular Usage
- Low Latency Data Access

As a Session State Store

- Session state stored for particular users
- Low Latency Access
- Distributed Session State

As a Message Bus

- Stream Messages Through Redis with Redis Streams
- Consume with consumer groups

As a Database

- No extra layers of caching, just use the cache as a DB!
- Document Data Structures allow for CRUD operations
- Indexing Capabilities to easily find things

## Redis Data Structures

### Strings

- Simplest Redis Data Structure
- Single Key -> Value
- Primarily driven by sets/gets

`SET foo bar EX 50`

Set foo to bar with expiry of 50

`GET foo`

Get foo value

### Sets

- Mathematical set
- Collection of Redis Strings
- Unordered
- No Duplication
- Set Combination (Union, Difference etc.)

`SADD myset thing-1`

Inserts into myset value thing-1

`SISMEMBER myset thing-1`

Does thing-1 exist in myset

`SMEMBERS myset`

Get all the members of myset

`SUNION myset myotherset`

Get all the members of both sets

### Sorted Sets

- Same membership rules as sets
- Set sorted by member score
- Can be read in order or in reverse order
    - By rank
    - By score
    - By lex

`ZADD users:last_visited 1651269753 Steve`

Add to user:last_visited Steve with the score 165129753

`ZRANGE users:last_visited 0 -1`

Get the sorted set from the beginning to the end by index (-1 means the end)

### Hashes

- Field value stores
- Appropriate for flat objects
- Field Names and Values are Redis Strings

HSET dog:1 name Honey breed Greyhound

Produces a hashmap structure that stores a flat object

HGET dog:1 name

Pulls out the name of dog:1 name from the hash

### JSON

Stores JSON data as objects in Redis
Access fields of objects via the JSON path
This is available in a Redis Module

Redis Modules sit on top of an API within Redis. You can use them for free under a source licence as long as you don't sell it as a database product.

`JSON.SET dog:1 $ '{"name": "Honey", "breed": "Greyhound"}'`

`JSON.GET dog:1 $.breed`

### Streams

- Implement Log Data Structures
- Send messages through streams like a message queue
- Messages consist of ID and a list of field-value pairs
- Consumer Groups for coordinated reads

Stream IDs can either be:

`1519878791273-0`

A timestamp in milliseconds and an incrementer. The incrementer distinguishes between things passing through within the same millisecond.

or special IDs:

* - XADD - Auto-Generate ID
$ - XREAD - Only retrieve new messages
> - XREADGROUP - Next Messages for Group
- - XRANGE - Lowest
+ - XRANGE - Highest

`XADD sensor:1 * temp 27`

Send a message on the sensor:1 stream, generate the ID automatically and send key pair temp-27

`XREAD STREAMS sensor:1 $`

Read new messages off the stream

`XREADGROUP GROUP avg con1 sensor:1`

## Redis Design Patterns

### Round Trip Management

The Root of Most Redis Bottlenecks are roundtrips

- Op-times in Redis are measured in us
- RTTs in Redis are measured in ms
- Minimize the RTT
- Minimise the number of round trips

Variadic commands (commands with multiple variables)

- Many commands allow you to pass in multiple different fields, so you can pass in more than one to avoid calling the same commands over and over again.

Always be Pipelining

- Send multiple commands to Redis simultaneously
- Appropriate when intermediate results are unimportant
- Can dramatically increase throughput

### Object Storage

There are three models

- Hashes
- Raw JSON 
- JSON Data Structure

Hashes are native flat-object data structures. You can use the HGET/HMGET/HGETALL to get the fields in the hash. It's Native and Performant but for more complicated objects or collection storage we've got issues.

JSON Blobs are a native, simple data structure. But Updates and Reads are expensive (O(N)). Read out the whole string, unserialize, reserialize etc.

JSON Data Structure. All operations are fast. Organized retrieval/update of data within the object and works with rich objects. However, this needs a module.

### Finding Stuff

#### Indexing

Finding by value requires the use of a pattern.
Construct Sorted Sets to Index

- `Person:firstName:Steve{(0, Person:1)}`
- `Person:age{(33, Person:1)}`

Query with ZRANG commands
Complex queries with Set Combination Commands.

This Native in any Redis and Performant. However it's really tricky to maintain with any consistency.
Cross-shard updates are very difficult for atomicity

#### RediSearch

This lets you predefine the index. Use JSON/Hashes to create your objects. Query with RediSearch. It's fast, easy to use, index across shards but you need to use a separate redis module.

Transactions
Apply multiple commands to server atomically.
Start with MULTI
Issue Commands
End with EXEC

If all commands are valid it will execute. However, it doesn't have any rollback logic.

Lua Scripts
These allow scripted atomic execution.
You can use intermediate results atomically.

## Using Redis in your app

RESP wire protocol

Your app talks to Redis over RESP

Lower level clients handle connections + commands

- Jedis
- node-redis
- etc.

## Redis Gotchas and Anti-Patterns

- KEYS command can lock up REDIS. Use cursor-based SCAN instead.
- Reuse connections. Opening up TCP connections is expensive so pool where possible.
- Single keys with heavy access patterns. Use redis-cli --hotkeys. Mitigate by scaling redis clusters or duplicating or use client side caching.

## Demo

```java
// Dependency: jedis.clients:jedis:version
JedisPool pool = new JedisPool("redis://localhost:6379");
try(Jedis jedis = pool.getResource()) {
    jedis.set("foo");
    String foo = jedis.get("foo");
    System.out.println(foo);
}
try(Pipeline jedis = pool.getResource().pipelined()) {
    jedis.set("foo", "bar");
    jedis.set("bar", "baz");
    jedis.sync();
}
try(Jedis jedis = pool.getResource()){
    jedis.sadd("myset", "foo", "bar", "baz")
    Set<String> strings = jedis.smembers("myset")
    System.out.println(strings);
}
```

## Questions

AOF/RDB - What are your volume limits? When do you get degredated performance?
