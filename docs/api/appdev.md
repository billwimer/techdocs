|  | All curated documentation has moved to the connections github pages wiki |
|------|-----------|
|**Source** | https://github.ibm.com/connections/connections-wiki/ |
|**Wiki view** |https://pages.github.ibm.com/connections/connections-wiki/wiki |

# AppDev: APIs in a Pink Universe

|            |                     |                        |                   |
|------------|---------------------|------------------------|-------------------|
| **Title**  | *APIs in a Pink Universe*              | **Status**             | *In Review*   |
| **Owner**  | *Maureen Leland*                       | **Additional Editors** | *none*      |
| **Due**    | *22-June 2017*                         | **Customer review ?**  | *no*        |

### Summary

Our APIs are our public face to our customers.  It is important that they be consistent and usable.  Connections has always had a large collection of APIs, but with Connections Pink, we will ship a reimagined, comprehensive set of APIs that allow our partners to build applications that will enhance our platform and make our partners our loyal advocates.

### Scope

This document describes the general goals and requirements for the API layer.  It can be considered a "survey course" for the API work to be done in Pink, and in many cases, there will be separate position papers for each related area.  For example, Joseph Lu has written a nice design document for the api gateway and the apis that this document will not try to recreate.  Refer to https://apps.na.collabserv.com/files/app/file/18e0ccc5-ae26-475d-b3b9-825f0de76ec9 as a sequel to this document. (The location of this document may change).

### Actions from this paper

Any team defining an API should ensure that the API is consistent with the standards described here, and in Joseph's document, and that the API is reviewed by the API Gateway team.  An example API will be published as an additional paper in the near future.  People writing an API should look at the example and model their API after the standard rather than being creative in naming and structure.

### Timeline for these actions

Now.

### What teams need to do

Teams building services that provide public APIs need to work with the API Gateway team to publish their APIs through the gateway.  In designing the APIs, they should adhere to the guidelines described (and referenced) in this paper.  All public APIs will undergo a review.  It is not the business of the API Gateway team to tell microservices what their APIs need to be, but the API team does need to ensure usability and consistency across all the APIs we are publishing.  It will not be an onerous process, we're not mean, and the more closely the guidelines are followed, the faster and more efficient the review process will be.

Loopback models for components that have public APIs that go through the gateway likely need to be packaged as loopback components, so the models can be shared between the gateway and the microservice.  (Discussion ongoing).

## Structure and Requirements

### It's a Contract

First and foremost, an API is a contract between us and our customers.  We do not break it.  Ever.  We can always add (optional) parameters to an API, or add new APIs, but once we ship an API, it is an inviolate commitment to support that API indefinitely (until the end of Pink).

This position is not always easy to abide by, but results in a stable platform for our partners and customers to work within.  That stable platform will attract and, importantly, retain developers and that will keep Pink alive.

If we ever encounter a situation that can't be solved by adding a parameter, the existing API remains inviolate, and a new API endpoint will be generated so our primary goal of not breaking customer (and internal) applications remains intact.

### Versioning

Just say no.  When you see an API that has /myfancyapi/endpoint/v1/..., what do you think?  The first thought I have is to wonder if I'm using the right/latest version, and if I am, for how long will this version be the right version and when is it going to break on me, making me change my code in a hurry?  In short, it does not instill confidence, rather it generates uncertainty.  Our APIs will not have versions because we are not going to make breaking changes to them (see It's a Contract).

### Consistent JSON Payloads

It's 2017, time for all of our APIs to speak JSON rather than Atom.

Connections has a lot of components in blue/green, and it is going to continue to have lots of services going forward.  A consistent API, with predictable patterns for structured responses and payloads, will make it easier for our partners to write code, as the APIs will be easier to memorize.  With multiple components and a global development team, it is easy to see how the APIs across the components could easily become inconsistent.

Joseph's document has a list of guidelines for API patterns.  When an API (or a change to it) is added to the gateway, it will be reviewed against this list.  In order to make it available at the survey course level, it is duplicated here.

#### Naming

Shamelessly adapted from Joseph's document.

Nouns for resources, except for algorithms
Avoid Hyphens, use Underscore instead (it's subjective, but underscores are prettier)
Don't expose the backend model, do not use 'package'
CamelCase for JSON variables

In responses:
Response should include a direct URL link to the returned resource
Use JSON Objects rather than arrays for the top level object. (entries[] is fine as long as it is inside an object).
The json payload should be symmetric.  The payload from a GET needs to be able to be used in a PUT, PATCH, or POST without any restructuring.

#### URLs

Understandable URLs - descriptive and clear


Hackable up the tree - remove a leaf and get a response back.  
    E.g., ../customers/123/orders/7 - removing 7 gives a list of orders

Use a collection, followed by a member
    E.g., ../customers/123/orders/7/items/897  - items belong to orders, which belong to customers
Versioning:  NO

Use query parameters when there isn't a simple hasMany relationship between the resources

- start, not first or startIndex (brevity is the soul of wit)
- count (not numReturned, num, etc)
- total (not sum, etc)
- entries[] for an array of elements returned

Clear, properly spelled, simple and obvious parameter names.  Every API should use the same name for the same concept.  For example, "lastModifiedByID" should be the name of the parameter that returns the ID of the person who last modified the object regardless of whether it is used in Files, People, LiveGrid, etc.  Refer to the example API(s) for the names of the common parameters.

#### Headers

Authentication (authorization) Header
Content-Type - Application/JSON in general
Media Type - default to JSON, Accept Header, which can be overridden by Request parameter of 'type'

### Doc/Developer "Experience"

Andre's developer experience paper will cover this in depth, but all of our APIs will be documented and available through an API Explorer for developers to experiment with.  All of us should share what we know in writing blog articles so that if someone googles "how do I use Connections APIs" or "how do I write an app for connections," they receive an overwhelming number of results.  All of us internally should also monitor stackoverflow and its ilk to help where we can. This will not only help our customers and the ecosystem, but selfishly it will also help us, because we learn so much from our customers and their questions and requests.

### Customer Preview

Whenever possible we should get user feedback on the APIs we have settled on. We need to engage our partners at the earliest possible moment to ensure usability, and incorporate their feedback.

## Authentication & Security

API calls must be authenticated, and we will support OAuth.  Refer to the Pink IAM document for supported services.  Depending on the API, authentication may not be required when anonymous access is allowed.

The most popular part of the Social Business Toolkit was the set of helper functions that encapsulated the authentication process for users.  While we should not need to resurrect the entire SBT (because our APIs will be comprehensible!), we should include such authentication helpers into the core API package. We will need JS helpers client side.

## Admin & Impersonation

Admin access to the APIs allows partners to maintain their environment with a minimum of intervention from us.  Certain APIs will only be available to users with admin access.  With token based auth, the token can contain any indication that the user is impersonated, and it
## CRUD

Our rest APIs should support all appropriate HTTP methods.  PATCH should be used to update individual fields in an object, PUT to replace the entire object.  This will be consistent with Loopback 3 handling of the REST verbs.

Like Loopback APIs (so this should likely be easy), our APIs should be symmetric.  For example:


- GET (many)   http://clumbersareus.com/api/community
- POST (one)   http://clumbersareus.com/api/community
- GET (one)    http://clumbersareus.com/api/community/id
- PUT (one)    http://clumbersareus.com/api/community/id
- PATCH (one)  http://clumbersareus.com/api/community/id
- DELETE (one)  http://clumbersareus.com/api/community/id
- OPTIONS       (multiple)

That allows callers to keep a minimum of roots around for their code.  Note that PATCH updates only the fields contained in the payload, while a PUT updates the entire record.  Loopback 3 updates are typically PATCH operations, and being consistent with that is good.  Also, if all you want to change in a profile is a phone number, for example, it is much easier for the caller to provide a teeny payload with just the phone number (and property name) than have to send back the entire profile payload.  This should also help to reduce network traffic and help keep us snappy.

### Batch mode

Every component should examine their APIs to ensure they can be used efficiently.  A recent customer request was to be able to find all the files liked by a person - and that request could only be done by iterating through all their files.  In social contacts, we were trying to get the profile information for multiple contacts at once.  Those scenarios are just an example, but there are clear use cases for returning results for either a list of items (profile ids in the social contacts use case) or to be thinking about the different ways that the data may need to be retried (liked files).  This is an application problem, but is important to the ultimate usability of the API, and components should keep this in mind.

## Gateway/Magic Middleware

The API Gateway in the middleware layer is the place from which all public APIs are exposed.  Individual microservices should not expose public APIs.  While there is natural worry that the Gateway can become a bottleneck, centralizing the APIs in such a way helps us ensure that we expose a consistent set of APIs, and will ensure that we avoid the API chaos that grew over time in blue & green.  It also offers opportunity for consistent metrics, caching, and throttling with a global perspective.

When the API Gateway receives an API call, it will ensure authentication, throttling, monitoring, etc. all happen.  (Each of those topics has or needs to have a separate position paper).  The Gateway will first check the cache before contacting the relevant microservice (assuming it is a get and that the microservice has no work to do).  In many cases, the response may already be available in Redis.  If not, the gateway will have a pub/sub conversation with the microservice, and the response will then be in the cache.  While in many cases the response may be exactly what the API gateway needs to send back, the gateway has the ability to adjust the response as needed to match the schema for the API model.

For updates, the gateway always needs to prod the microservice into action, but again the conversation is pub/sub, with the gateway returning the requisite response to the caller.

Some APIs like file upload present complications to the pub/sub mechanism.  These are currently under discussion.  It is believed, however, that the majority of conversations between the API gateway and the microservices should be fairly simple, with the microservice providing almost exactly the needed API response through the pub/sub mechanism.

This centralization allows us to do holistic predictive caching.  While each microservice has a simple view of what it is being asked, the gateway can see the entire conversation across the macrocosm, and can make predictions that cross microservices (e.g., you just asked for a file, you may want a profile or a community next).

A vital service of the API gateway is to provide the interoperability between the legacy ATOM APIs and the new JSON APIs.  Because we are reasonable people, we are not going to break our customer's applications.  The API Gateway contains a translation layer to translate atom to JSON and JSON to atom, allowing our partners' existing applications to continue to work with pink.  It will also provide new JSON apis for the services that are not being rewritten (wikis, blogs, etc), so that all of our services present a consistent, JSON API to our customers.

Please see Joseph's paper (linked above) for some nice architecture diagrams and further discussion of the API gateway and the translations between JSON and ATOM.

## GraphQL

The APIs for Pink will include GraphQL capabilities.  This does not give us an excuse to short shrift the REST APIs - the REST apis must be complete unto themselves.  But GraphQL provides a layer that allows consumers to easily collect specific data from varied apis in a single call, which is particularly important to the mobile people, and for some UI applications.

Prototyping has begun with the express-graphql package, and it looks promising.  GraphQL schemas need to be as inviolate as API contracts.  

GraphQL should also have its own paper!

## @Functions - The Lazyman's APIs

While the REST APIs we provide will give developers a full set of capabilities, we will also provide a JavaScript library of functions, referred to as @functions in order to make those old Domino developers smile (and feel welcome in Pink). And it's honestly not just for Domino people - they are shortcuts for any developer who likes to get things done the fastest way possible.  These functions will be very visible in the LiveGrid component, where they will assist adventurous non-developers, but they are also useful tools in any application that our partners and customers may write, so rather than making them available only in LiveGrid, they will be shipped as part of the API layer (but LiveGrid will definitely consume the microservice(s)).  It would also be possible for any end user to use formula language in any runtime context where we choose to expose them (imagine putting 'hi @username' as a status).  Having the ability to do this doesn't mean we have to expose it everywhere initially,

While it may be easy to think of these functions as being intended for inexperienced developers (and they certainly are good in that regard), they are also useful to more experienced developers as shortcuts.  Why go through typing multiple lines to do a rest call to the people service to get the user's name when all you have to type is @UserName()?

So what does it mean to have @functions?? Classic @functions operate at both server and client.  We need separate services for client and server @formulae, and potentially a third for data services.  For server side functions, we should also provide an agnostic client side library for users to include to invoke them directly from client side code (and server side code can call the parallel function server side).  Ideally just a matter of which library to include in the developer's app.

What @functions do we need?  The union of the Excel functions and Domino @functions is a good starting approximation.  Jason found this library of Excel functions that might give us a good head start - we should begin by evaluating it.  https://github.com/handsontable/formula-parser

The Domino @functions are displayed here:  http://www-12.lotus.com/ldd/doc/uafiles.nsf/docs/designer65poster/$File/FormulaPoster.pdf
While not all of those @functions apply to Connections (we don't need @WebDBName), a good many of them do. And there are Connections specific @functions we can easily imagine, too - like @MyNetwork(), @MyCommunities(), ...  In essence, in reviewing our APIs, we should ensure that the most common operations have an @function shortcut.  In looking at that poster, it is easy to imagine each yellow box being a separate microservice (though perhaps some can be combined, too).  Easy divisions are client/server, and again by document vs. Excel type functions (if we use the Excel package, that would likely form a single microservice).

It is important to note that @functions can be strung together to form code blocks that must be parsed and processed.  They are not simply discreet functions but often are used in a more complex expression.  @if, @do, @for, etc. all need to be supported to meet our customers' needs.  While JavaScript developers who are using the functions as a shortcut may not need @if, functions like @if allow less skilled developers/end users to use macros in Connections in similar ways as they would in a spreadsheet.  Thus, the power of these functions and then the platform becomes available to many more users.

These @functions should also be extensible so that our partners can extend our set.  So the @functions should all be in a registry that is also fed by extensions in the app registry.  This is looking like it needs its own position paper :-)

## Extensibility

In the same way that our UI will have extension points, our APIs should also give partners the opportunity to customize the response.  Partners should also be able to run extensions in the server, providing an additional layer of extensibility.  There have been ongoing experiments in this space and it is worthy of its own paper.
