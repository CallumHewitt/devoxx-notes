# REST next level: crafting business oriented web APIs

2022-05-11 11:50
Julien Topcu
Tech Coach @ Shadow

We want all of our business logic in the backend. We don't want it duplicated on all of our consumers.

Eg.

```js
performASearch()
selectTrainWithFare()
isSelectionComplete()
bookTrip()
```

The frontend uses those back end features. In between these layers we use REST. Which only has four words mapping to CRUD. These don't map neatly between our English language method names that are far more expressive.

"I want to search for trains going to my destination" != "CREATE a search resource"

In REST those are equivalent, but not in English.

We have an impedance mismatch.

Before 2000, if you wanted to interact with the backend you would have to use the HTTP protocol. This was backend specific. REST came along and standardised this.

In 2000, Roy Fielding warned that since information is transferred in a standardised form, we will degrade the performance of the system.

## An example service - Columbiad Express

A space port travel company.

JFK -> MOON.

Search shows all of the outbound space trains. We select the fare type. Then the selected train appears on the right hand side.
Then we select the inbound return space train. Only compatible inbound trains are selected.

Search for the space train:
/searches/$uuid/spacetrains?bound=OUTBOUND&onlySelectable=false

Imagine the PO comes in and asks for multi-destination trains. Now OUTBOUND/INBOUND doesn't mean anything. We have to change the backend and the API.

Now we have

/searches/$uuid/spacetrains?journeyIndex=0&onlySelectable=false

This change will mean that the API is broken for the consumers. However, miraculously the UI has detected the change.

This comes from a technique that brings business oriented web APIs in as a lovely side effect.

## False beliefs regarding REST

```java
interface BookSpaceTrains {
    Booking fromTheSelectionOf(Search search)
}
```

Since we're creating a resource we have to use POST.

```java
interface RebookSpaceTrains {
    Booking atANewDate(UUID bookingId, LocalDate newDate)
}
```

Because we're recreating the same resource we still have to use POST.
We have two different use cases on the same endpoint. The only difference is the content of the payload. One includes a new date, the other, only the ID.

We could try POST /rebookings but rebooking is an action and not an entity. We're caught out by this limitation of REST.

REST **must not lead** the design of the business domain.

Rebooking is pseudo-functional accidental complexity. You have to figure out why we have rebooking and bookings.

POST bookings/{id}/rebook { newDate: "" }

This fixes the problem but we're not supposed to use verbs in REST right? If you look at Roy Fielding's work, he never actually says about how you construct your URL. The main thing is not to use words in your URL that are at odds with the GET, POST, PUT, DELETE words.

We say the frontend shouldn't know how to craft the URL, but it needs to speak with the backend regardless.

## Confusion between the model and the business

Let's say that we need to select the fare that we want.

The model:

Search
|- availableSpaceTrains
|- selection
    |- selectedSpaceTrains

There are several ways to implement the business world in a model. Models don't last long, they solve a specific problem at a specific time.

Models exposes the business model implementation. Breaks in the API break the models in the code.

Changes on the user side for the search as it updates use

PATCH /searches/{id}/selection

I'm updating the selection that the user has picked on the sub-resource.

In the worst case, the user experience get's built like the API. In the best case, beautiful frontend, ugly backend. The user has to translate the business logic into the language of the backend. A fare selection to a subresource PATCH.

Exposing the model to the consumer causes accidental complexity of adaptation.

We instead need to encapsulate all of this in the backend. 

## The problem with encapsulation

```java
Search {
    UUID id
    Criteria criteria
    SpaceTrains spaceTrains
    Selection selection

    get/sets
}
```

In domain design we drop the setters and have functions that make sure all the state transitions happen correctly.

```java
Search(Selection selection, ...) {}
Search selectSpaceTrainWithFare((UUID spaceTrainId, UUID fareId)
Boolean isSelectionComplete() {}
```

When you want to expose these on the network you have to expose it and reveal all of the state. In the backend the controller and the database are CRUD oriented and we don't want to be like that. So the mid-layer business logic is really easy just to make it CRUD.

Completely
Ridiculous & 
Useless
Domain Driven Design

Back at the post bookings section, we have to de-encapsulate by looking at the post body in order to do anything.

```
PATCH /searches/{id}/selection
[
    {
        "op":"add",
        "path": "/selectedSpaceTrains/-"
        "value": {"trainNumber":"MOON421", "fare":"FIRST"}
    }
]
```

Problems: Front end has to translate business intent into the payload. The only way to see what's happening is to read the payload.

Lets do this instead:

POST /searches/{id}/spaceTrains/{number}/fares/{code}/select

The backend model is still the same, the intent is business specific. We're asking for an update to train {number} by taking the fare {code} and selecting it.

Now, what about the front-end knowing how to build these URLs? We didn't want it to know how to do that.

## Problem 4: Anemic APIs

How do we know that we need to proceed in the specific order of steps to make a booking. We have about 6 API calls. POST, GET, POST, GET, POST, POST...

The backend only processes steps of the workflow. The backend only shows steps of the workflow.

Nothing stops the front end from calling the endpoints out of order. The orchestration is only happening on the front-end. And that's business logic stuck in the front-end.

We need to encapsulate the workflow in the API.

Level 0 - The Swamp of POX
Level 1 - Resources
Level 2 - HTTP Verbs
Level 3 - Hypermedia Controls

## Hypermedia Controls

HATEOAS - Hypermedia as the Engine of the Application State

A linking system (URLs) allowing you to:
- Expose the relationship between domain objects
- Encapsulate the business workflow

The front-end only needs to know the entry point to the API.

The first POST request creates the Search.
In the response you get _links. Which provides URLs for doing different actions on the search object.
    - Go back to the search
    - self
    - Getting the selections
    - Getting all the outbound trains

Getting the selections provides links for each selection to send it to the backend and note that a selection has been made.

As the links go deeper, the backend can detect that a selection has been made on the fare. And offer a create booking link in the response in addition to the options that are available on the UI.

This gives a much better decoupling between the API and the implementation. You can change the implementation pretty easily.
