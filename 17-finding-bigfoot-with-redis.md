# Finding Bigfoot with Redis and RedisSearch

2022-05-12 16:30
Guy Royse
Developer Advocate @ Redis
@guyroyse
github.com/guyroyse
guy.dev

## Bigfoot

Bigfoot is Guy's father. He went out for milk and cigarettes and never came back.

He's been looking for Bigfoot for decades now.

He researched trying to find his Dad. He found newspaper articles of his past.

He spoke to the people at the discovery channel and they weren't able to help either. But the guy on the right is founder of the Bigfoot Field Researchers Organisation (brfo.net).

They have a comprehensive sightings database. What they meant was actually a bunch of webpages with accounts on it, not something to query.

However, Tim Renner (@timothyrenner) and has screen scraped all of the information and created a CSV file with all of the data. He's also done Haunted Places and UFO data.

Tim Renner did some clustering analysis on the Bigfoot data and found that he's often found... in the woods.

## Data

The data has the following fields:

- ID
- Date
- Title
- Observed
    - What was seen
- Classification
    - A - I saw Bigfoot
    - B - I saw evidence of Bigfoot
    - C - Someone told me they saw Bigfoot
- County
- State
- Latitude
- Longitude
- Location Details

Tim Renner put some Dark Sky weather data on top of that. Temperatures, Humidity, Cloud Cover, Moon Phase etc.

This is the data that Guy got and he put it all into Redis.

## Representing Data in Redis

- Strings: `SET bigfoot:sighting:8086:classification "Class B"`
- Lists: `LPUSH bigfoot:new:sightings "I saw Bigfoot in the woods"`
- Sets: `SADD bigfoot:sightings:by:state:Ohio 8086 7220 7085`
- Hashes: `HSET bigfoot:sighting:8086 classification "Class B"`, `HGET bigfoot:sighting:8086 classification` -> "Class B" 

Our data is taken from CSV and then represented as Hashes.

The easiest way to get out the sightings is:

`HGETALL bigfoot:sighting:8086` - Get sighting 8086 from the `bigfoot:sighting` namespace.

We want to see sightings in particular places, so lets build indicies using Sets.

`SAD "bigfoot:sightings:by:state:Ohio" 38980 46578 ...`
`SAD "bigfoot:sightings:by:class:B" 94145 10514 ...`

`SMEMBERS bigfoot:sightings:by:state:Ohio` returns all of the members.
`SINTER bigfoot:sightings:by:state:Ohio bigfoot:sightings:by:class:B` returns all class B Ohio sightings.

This leads to a problem.

## 99 Problems

### Indicies is One of Them

We have to remove things when they change if the sighting moves states. Same thing with the sighting classification.

This is a non-atomic operation and could run into some big problems down the road.

### Finding Bigfoot is One of Them

```redis
SUNION
    bigfoot:sightings:by:state:Ohio
    bigfoot:sightings:by:state:Kentucky

    SINTER
        bigfoot:sightings:by:class:B
```

Redis doesn't support this. So you'd have to create a temp set for the first union and then apply the second one.
What if you want all the sightings that were at temperature 75 degrees or higher? That's not supported here either.
What about Bigfoot sightings with the word Creek in them?
What about sightings within 50 miles of Cincinatti?

## RedisSearch

This is a module for Redis that does all of this Indexing for you. Provides full-text search. It also does aggregation.

```redis
-- Create an index on all with prefix bigfoot:sighting:

FT.CREATE bigfoot:sighting:index
ON hash
PREFIX 1 bigfoot:sighting:
SCHEMA
    observed    TEXT    SORTABLE
    state       TAG     SORTABLE
    humidity    NUMERIC SORTABLE
    geo         GEO
```

We can then do queries

```redis
FT.SEARCH
    bigfoot:sighting:index "*"

LIMIT 0 3 (starting index, number of results)

RETURN 2 id state (fields to return)
```

There are four types of fields

## Text fields

    - Searching text is easy. You can use `FT.SEARCH` and then just search for the words you need.
    - "creek river" will search for creek or river
    - "creek ~river"
    - Lots of options
    - "@title:creek @observed:river" - Only searches certain fields
    - In your schema you can also use WEIGHT to give certain fields more weighting.
    - Stopwords like "a, is, the, an" etc are ignored as it's not useful for search. You can turn this off.
    - Stemming. give is like "give, gave, given, giving" so Redis gives that full search out of the box.

Let's search for Creek

`FT.SEARCH bigfoot:sighting:hash:index creek`

## Tags

`FT.SEARCH bigfoot:sighting:index "@state:{Ohio|Kentucky} @class:{B}`

Classification B, State Ohio or Kentucky

## Number

`FT.SEARCH bigfoot:sighting:index "@temperature_high:[60 75]"`

Temperatures between 60 and 75.

+inf is positive infinity
-inf is negative infinity
(60 (75 means exclusive. > than not >= 60 and < not <= 75

## Geolocation

`FT.CREATE bigfoot:sighting:index ON hash PREFIX 1 bigfoot:sighting: SCHEMA location GEO`

Redis does it with longitude then latitude, rather then latitude then longitude like most other places.

`FT.SEARCH bigfoot:sighting:index @location:[ -84.5120 39.1031 50 mi]`

Search 50 miles of that location.

## Aggregation

`FT.AGGREGATE bigfoot:sighting:index "*"`

These allow you to filter values out, transform your values to create new fields, group fields and the sort them.

You can do these in any order over and over again if you'd like.

### Filtering

`FILTER "@temperature_high > 75"`
`FILTER "@temperature_high < 80 || @humidity > 50"`
`FILTER "exists(@temperature_high)"`
`FILTER "floor(@temperature_low) > 32"`
`FILTER "dayofweek(@timestamp) = 1"`

### Transforms

`APPLY "@temperature_high" as high_f`
`APPLY "@temperature_high * 5 / 9" as high_c`

These are the same functions that are available in the filtering

### Grouping

`GROUPBY 2 @state @classification REDUCE COUNT 0 as count`

The reducer is the aggregator that brings your items together.

`REDUCE AVG...`
`REDUCE MAX...`
`REDUCE MIN...`
`REDUCE QUANTITLE`

`GROUP BY 0` is the entire dataset.

### Sorting

`SORTBY 2 @state @classification`