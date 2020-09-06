# Designing & Building REST APIs

[Course link](https://www.udemy.com/course/rest-api)

**Contents**

- [Designing & Building REST APIs](#designing---building-rest-apis)
  * [What the course covers](#what-the-course-covers)
  * [Can we use mLab for hosted mongo services?](#can-we-use-mlab-for-hosted-mongo-services-)
  * [Why is REST popular?](#why-is-rest-popular-)
  * [Types of API consumers & APIs](#types-of-api-consumers---apis)
  * [API management](#api-management)
    + [Security](#security)
    + [Documentation](#documentation)
    + [Access request](#access-request)
    + [SLA Management](#sla-management)
  * [REST architectural constraints](#rest-architectural-constraints)
    + [Client-Server](#client-server)
    + [Uniform interface](#uniform-interface)
    + [Statelessness](#statelessness)
    + [Caching](#caching)
      - [Caching on the server](#caching-on-the-server)
      - [Caching outside the server](#caching-outside-the-server)
      - [Cache directives as HTTP headers](#cache-directives-as-http-headers)
      - [How is an Etag header helpful?](#how-is-an-etag-header-helpful-)
      - [How does a server calculate the Etag?](#how-does-a-server-calculate-the-etag-)
      - [The W in an Etag](#the-w-in-an-etag)
      - [Last modified header & its use](#last-modified-header---its-use)
    + [Layering](#layering)
    + [Code on demand](#code-on-demand)
    + [Measuring the RESTfulness of an API](#measuring-the-restfulness-of-an-api)
  * [Designing a REST API](#designing-a-rest-api)
    + [Domain examples](#domain-examples)
    + [Product or package](#product-or-package)
    + [Version](#version)
    + [Resource & ID](#resource---id)
    + [Root URL](#root-url)
    + [Resource naming conventions](#resource-naming-conventions)
    + [Resource associations](#resource-associations)
    + [HTTP request flow for REST API](#http-request-flow-for-rest-api)
    + [HTTP response flow for REST API](#http-response-flow-for-rest-api)
    + [HTTP status codes](#http-status-codes)
    + [Using the appropriate verbs](#using-the-appropriate-verbs)
    + [Creating a new resource](#creating-a-new-resource)
    + [Retrieving a resource](#retrieving-a-resource)
    + [Updating a resource](#updating-a-resource)
    + [Deleting a resource](#deleting-a-resource)
    + [Notes](#notes)
    + [Data format](#data-format)
  * [API error handling patterns](#api-error-handling-patterns)
    + [How many HTTP status codes to use](#how-many-http-status-codes-to-use)
    + [Application error response handling patterns](#application-error-response-handling-patterns)
  * [API versioning patterns - Handling changes](#api-versioning-patterns---handling-changes)
    + [Types of changes](#types-of-changes)
    + [When to change your API?](#when-to-change-your-api-)
    + [How to version your APIs?](#how-to-version-your-apis-)
  * [REST API cache control patterns](#rest-api-cache-control-patterns)
    + [Who controls caching?](#who-controls-caching-)
    + [Why cache?](#why-cache-)
    + [What to cache and for how long?](#what-to-cache-and-for-how-long-)
    + [Cache control directives](#cache-control-directives)
      - [Directives](#directives)
  * [REST API response handling patterns](#rest-api-response-handling-patterns)
    + [Custom partial response support](#custom-partial-response-support)
    + [Adding pagination support](#adding-pagination-support)
  * [REST API security](#rest-api-security)
    + [Basic authentication](#basic-authentication)
    + [Token based authentication](#token-based-authentication)
    + [API secret & key](#api-secret---key)
    + [OAuth 2.0](#oauth-20)
    + [Last mile security](#last-mile-security)
  * [REST API specifications using Swagger 2.0](#rest-api-specifications-using-swagger-20)
    + [Swagger 2.0](#swagger-20)
  * [REST API management](#rest-api-management)

## What the course covers

1. Evolution of REST APIs
2. Designing REST APIs
3. Securing REST APIs
4. Documentation: Swagger 2.0 / Open API initiative specifications
5. API management

Overall, one would be able to:

- Setup best practices for building APIs
- Understand API management
- Build some API as Proof of Concept

## Can we use mLab for hosted mongo services?

**No!**

MLab as a company got acquired by Mongo. Therefore, they have integrated it into an offering called **MongoDB Atlas**

We can use MongoDB Atlas by signing up at [cloud.mongodb.com](https://cloud.mongodb.com). It allows us to create "MongoDB Clusters" in the public cloud. For testing and learning, free, shared clusters are available

1. Once a cluster gets created, add a user for the database (Security > Database Access > Add Database User)
2. Under clusters, you can create one ("Add my own data" option). Give it a database & a collection name
3. You may insert a document with "insert document" & add the fields, values, & their types
4. The "filter" search bar helps us type in JS objects & search for documents
5. The database name on the left has an trash icon next to it. Use this to delete a whole database

## Why is REST popular?

1. Design principles (that can be adopted by API developer to quickly launch their API services)
2. Proven best practices (A large community of developers contribute to patterns & ready-to-use libraries)
3. Simplicity & flexibility (Low network latency, less bandwidth required, not complex, etc as compared to SOAP)

REST API is however, not tied to any specific data format like JSON (it can be based entirely on the client's needs). You can also build REST APIs with other application layer protocols instead of HTTP. However, no one does this as such. Therefore, REST rests on HTTP for the most part

REST APIs are not tied to standards like WSDL in SOAP. This makes it more flexible

TODAY: **REST/JSON** has become the de-facto standard for APIs

A **resource** in the context of REST is anything. It can be an item, a book, an account, a thing, an object, or even a real-world entity

## Types of API consumers & APIs

The 3 types of consumers are:

* Private or Internal: Your own developers within the organisation
* Public or external: Anybody who builds apps that access your application on the public internet
* Partner: A trusted organisation outside of your own

Similarly, there are 3 types of APIs that map directly to each of the above consumers:

* Private or Internal: Usually fully trusted so all capabilities are made available & less security checks are required (Ex: API call from your own website)
* Public or external: Since anyone can use it, a higher level of security is established
* Partner: The API will be used within the network of a trusted partner who might use it on their public facing interfaces (ex: website)

In general, private APIs have most capabilities, public the least, and partner a moderate amount of it. ***Note: The implementation of the API itself does not change among these 3 types***

## API management

Once the API is up & running, there are some ***considerations*** that come into play & these govern the way in which you continue providing the API service to your consumers

### Security

1. Private:
	- Developers are trusted
	- *Basic authentication* & *proprietary schemes* are enough
2. Public:
	- Developers cannot be trusted
	- *OAuth & key/secret schemes* or a combination of them are employed

### Documentation

1. Private:
	- It is a controlled environment. API details can be shared freely
	- Usually transferred through PDFs, internal websites or on spaces such as confluence docs
2. Public:
	- It is an uncontrolled environment where we do not know the API consuming developers
	- Hence, we provide a specific website for documentation known as a *developer portal*

**Good practice:** As an API developer, always create a developer portal! It does not matter if it is private or public

### Access request 

Who is allowed to access the API & how?

1. Private:
	- Controlled
	- Usually via *email access (official email)* or an *internal ticketing system*
2. Public:
	- Uncontrolled
	- The API can *either be freely accessed* or there exists workflow of *registering oneself on a portal* & access is granted via an automated process or a manual one. This information & flow can be on the developer portal itself 

**Good practice:** As an API developer, always manage access request flow on a developer portal! It does not matter if it is private or public

### SLA Management

**Service Level Agreement (SLA)** is a contract between an API provider and a consumer which lists out many aspects of the service

As an example of an SLA, a company can provide:

- 99.9% uptime of the server
- API calls limited to 20 calls/sec
- Support provided via email

Note that depending on the type of consumer, we can have multiple SLAs. Whenever a consumer signs up, it is common to place him/her in an **SLA tier**

Depending on the SLA tier, different services can be provided as per contract. For example, a public consumer's API call rate can be 20/min while a partner's might be 160/min.

**Good practice:** As an API developer, define an SLA and publicise it on a developer portal! It does not matter if it is private or public

## REST architectural constraints

In order to build a service that is RESTful (& not REST-like), we have to follow **6 design principles**:

1. Client-Server
2. Uniform interface
3. Statelessness
4. Caching
5. Layering
6. Code on-demand (optional)

### Client-Server

This constraint mentions two things:

>  "REST application should have a client-server architecture"

>  "Client & server should not run in the same process"

Client & server here means the API consumer (ex: website or app) and the API web service, respectively. Client requests the resource, the server provides it. Server can serve multiple clients

Client & server not running in the same process refers to **decoupling** which has a few advantages:

1. Server can think about security (rate limiting), persistence (DB management), scaling, and so on
2. Client can think about authentication & authorisation, multi form factor, app development, and so on

Client & server should have a **"uniform interface"** by which they communicate with each other (ex: request & response). This allows for **independent evolution** of client and server. For example, a client may popup that wants CSV instead of JSON. The server can make this possible, since the interface is common. Therefore client & server can evolve separately and the server can service different types of clients. There should not be an impact of such changes

### Uniform interface

This constraint suggests the following:

> "The client & server share common technical interface"

- Interface: The contract between client & server
- Technical: indicates that there is no business logic involved

**4 guiding principles used to build a uniform interface**

1. ***Individual resources are identified in the Request (URI/URL)***
	- For example, www.google.com/api/users/23 identifies a user based on the user id i.e 23
	- All REST API resources are identified by a **Uniform Resource Identifier (URI)**. For example, prefer `/cars/:id` (iex: `/cars/23`) over `/cars?id=23`. The former is a URI and it identifies a resource (restful). But, in the latter method, the URI is same for all and the query parameter identifies the resource (not restful)
2. ***Representation of the resources***
	- Client receives representation of resource (Ex: client receives details of a car) which it uses to manipulate the resource itself
	- Note that the format of the resource data need not be same on server & client. For example, the data can be inside a DB on server and sent to client via JSON or CSV
3. ***Self-descriptive messages in metadata***
	- The client & server can share metadata about the messages. This is usually in the form of headers. As an example, the client can set the `Accepts` header to indicate to the user what type of data it can handle. The server can send back a header `content-type` in response which tells the actual format of the data. There are many more headers that act as metadata (Status code, `expiry`, `cache-control`, etc)
4. ***Hypermedia***
	- `Hypermedia = data + actions = links for discovery`
	- This principle suggests that server not only send back data but also hypermedia that lets client discover more data. For example, it can send back a URI to an API that fetches a post as part of the response to a request meant to get the list of post, say only its titles.
	- **HATEOAS** = HYPERMEDIA AS THE ENGINE OF APPLICATION STATE

### Statelessness

> "Client must manage its own state thus making the server stateless"

Whenever a server manages the application state on behalf of a client, it becomes complex and not good for scaling. Session based user management using cookies on the server is an example of stateful server and it is not a good practice in REST (That is why we use JWTs instead)

Therefore REST APIs adhere to the following:
1. Clients manage their own application state
2. Each request is treated independently on the server (Previous requests by the client or the state of the client do not matter)

Therefore, *all requests to the server are self-contained* (no dependence on server remembering data from previous requests)

**Challenges of statelessness**

1. Increased chattiness: The client & server might need to communicate more often
2. Request data size: The state will have to be shared with every request - more bandwidth consumed, longer latency
3. Performance impact: The first two points affects performance of the app - bad UX or delays, etc leading to higher bounce rate, monetary loss, and so on

### Caching

> "Use caching to achieve **higher scalability & performance**

Caching *counterbalances* the challenges that statefulness constraint creates. Therefore, it will help us reduce the chatter as well as the data size of the requests

#### Caching on the server

1. *Database caching*. Ex: Caching the Database so that we can get ready made answers to DB queries instead of actually querying it every time
2. *Server caching*. Ex: When the server receives a request, depending on the parameters, path, & the resource, server may choose to send back cached resource rather than executing a whole process to generate it. It may also indicate to client that resource has not changed

***The caching on the server is in the control of the REST API designer.*** 

#### Caching outside the server

1. *Application caching* (on the client). Ex. browser
2. *Gateway/Proxy caching*. Ex: CDN server, Nginx load balancer server

As REST API designers, we don't have much control over the caching on these platforms. We can however, define cache-control directives that a well-behaved client can adhere to and implement. These are sent via HTTP headers

#### Cache directives as HTTP headers

1. **`Cache-Control`**: It is a header containing cache control directives. There can be many directives that are comma (`,`) separated. Also, some of them have an optional argument (`<dir>=<arg>`). Ex: `cache-control: public, max-age=31536000`
	- `No-Store`: Do not cache at all
	- `No-cache`: Cache it but every time you need to use it, check with server once (Mainly used with `Etag`)
	- `private`: Only the client i.e Intended user can cache the resource
	- `public`: Anyone can cache it i.e Not just the client but a gateway or a proxy (intermediary) too
	- `max-age`: The time in seconds for resource cache expiry
2. `Expires`: Sets the exact date and time (timestamp) until which the resource should be cached
3. `Last-Modified`: When was the last time that the resource was modified
4. `Etag`: It is a ***unique identifier*** for a resource (like a hash)

**Note**: If there is a Cache-Control header with the `max-age` or `s-maxage` directive in the response, the `Expires` header is ignored.

#### How is an Etag header helpful?

Etags help us make **conditional requests**

Syntax:

```shell
ETag: W/"<etag_value>"
ETag: "<etag_value>"
```

- When you request a resource for the first time, the data as well as an Etag header is sent back
- The client remembers the Etag value and in the subsequent requests to the same resource, sends this Etag header along as "if-none-match" or "if-match"
```
// Example:
If-None-Match: "<etag_value>"
If-None-Match: "<etag_value>", "<etag_value>", …
If-None-Match: *

// `If-Match`: the server will send back the requested resource only if it matches one of the listed ETags
```
- Server checks the received Etag and compares it with the Etag it has generates for the resource. 
- If Etags are the same, server sends back a **`304` response without a body**. This means that the content was not modified, so the client can use the cached resource
- If content was modified, then the data with the new Etag is passed along 

#### How does a server calculate the Etag?

Server has two ways:
1. ***Shallow check***: It generates or fetches the resource and then creates the Etag before sending the response. Checks the new Etag with the received one. If they match then it sends back `304`
2. ***Deep check***: When the Etag is received from the request, it is checked against an existing Etag for the current resource on the server thereby excluding the need to process the request as if it were new. It can immediately send back a `304` in case of a match

Shallow check only reduces response size and hence network latency. But, it does not reduce the server execution time i.e the time to fetch or process data afresh

Deep check reduces all three i.e Server execution time, response size, and network latency

Example of etag, last-modified, cache-control, & expires headers:

```shell
curl --head https://www.hotstar.com/assets/2220b001005f901871764a12537e6407.svg
HTTP/2 200
content-type: image/svg+xml
etag: W/"97f8-1743143c0f0"
last-modified: Sun, 30 Aug 2020 02:31:43 GMT
server: Akamai Resource Optimizer
x-powered-by: Express
x-cachestatus: 3
cache-control: public, max-age=31536000
expires: Wed, 01 Sep 2021 11:40:03 GMT
date: Tue, 01 Sep 2020 11:40:03 GMT
x-cachestatus: 1
x-cachettl: 31464405
x-origin-date: 1598960403
```

Examples of Etag and a `304` response:

```shell
# First request:
curl -I https://www.thepolyglotdeveloper.com/css/custom.min.css
HTTP/2 200
accept-ranges: bytes
cache-control: public, max-age=0, must-revalidate
content-length: 6235
content-type: text/css; charset=UTF-8
date: Tue, 01 Sep 2020 11:48:15 GMT
etag: "6686025177e732020fd0563cd1271c82-ssl"
strict-transport-security: max-age=31536000
age: 0
server: Netlify
x-nf-request-id: 60784331-6a70-4288-a158-28273e878841-14739554

# Second request with `if-none-match` header:
curl -I -H "if-none-match: \"6686025177e732020fd0563cd1271c82-ssl\"" https://www.thepolyglotdeveloper.com/css/custom.min.css
HTTP/2 304
cache-control: public, max-age=0, must-revalidate
date: Tue, 01 Sep 2020 11:48:42 GMT
etag: "6686025177e732020fd0563cd1271c82-ssl"
strict-transport-security: max-age=31536000
age: 1
server: Netlify
x-nf-request-id: 60784331-6a70-4288-a158-28273e878841-14750453
```

#### The W in an Etag

'W/' (case-sensitive) indicates that a ***weak validator*** is used. 

- Weak etags are easy to generate, but are far less useful for comparisons
- Strong validators are ideal for comparisons but can be very difficult to generate efficiently. 

Weak ETag values of two representations of the same resources might be semantically equivalent, but not byte-for-byte identical. This means weak etags prevent caching when byte range requests are used, but strong etags mean range requests can still be cached.

#### Last modified header & its use

It is a header that mentions when was the last time a resource was modified. It is supposed to e less accurate than an ETag header, it is a fallback mechanism.

- The `If-Modified-Since` request HTTP header makes the request conditional: the server will send back the requested resource, with a `200` status, only if it has been last modified after the given date. 
- If the request has not been modified since, the response will be a `304` without any body; the `Last-Modified` response header of a previous request will contain the date of last modification.

`If-Unmodified-Since` request header does the opposite with a `412` status if request has changed

### Layering

A ***layered architecture*** is one in which the *dependencies are simplified*. Every entity forms a tier i.e Client is a tier, Server is a tier, Gateway is a tier, DB too!

When you have multiple tiers, it is called a layered architecture. In this architecture the dependency is *unidirectional* & *at the most on the next tier*. For example, client depends on a gateway only, the gateway depends on a load balancer only, the load balancer depends on one of the origin servers only, the server depends on the DB only

In this layered approach:

- There is space for architecture to evolve (Ex: Introducing a load balancer & multiple servers)
- At the most, the layer in question & one other layer are impacted due to changed

*REST APIs tend to work very well with layered architectures* and hence it is recommended we use them during REST API design. Layering allows for *simplicity* but does *not affect performance*

### Code on demand

> "Server can extend the functionality of the client by sending code"

This constraint is **optional**. If only this constraint is not addressed but all others are then your API is still RESTful

Consider a HTML page. Server has sent the HTML as data but it also contains anchor tags and javascript. Both of these provide some functionality and hence are known as *actions* (or code)

Similarly, in REST we can send back data but also links (*API paths*) to access other data. For example, if we are browsing the titles of a blog post, in the same response we can list the link to get the full contents of that blog post

As a second example, if we have a tourist package data come in the response, it may contain links to actions such as one to book the package, or retrieve reviews, and so on

**Benefits**

1. Server knows resource state (not client app state!) and hence is efficient. Ex: Client does not need to make more requests just to get the endpoint for another resource
2. Server may change the API URI endpoint for a resource since that data becomes available as code on demand in other API calls (client need not remember the URI)
3. Server may add new functionality i.e it may evolve. Also, client can also evolve independently

If we support code on demand then it is known as **HATEOAS** (Hypermedia As The Engine Of Application State). The links we get back in the API responses are known as *HATEOAS Links*

*In short, server extends the functionality of the client*

### Measuring the RESTfulness of an API

We use the **Richardson Maturity Model (RMM)**. It is a way to measure RESTfulness of an API. This model was built by analysing 100s of web services

[Link to RMM article by Martin Fowler](https://martinfowler.com/articles/richardsonMaturityModel.html)

RMM provides an API service a score between `0` & `3`. If you have scored `0` then it means the API is not at all RESTful. If you have scored `1` then it means the API is fully REST compliant

**LEVEL 0**

- Least RESTful. 
- Implements an `RPC with HTTP + XML (with SOAP maybe) + only 2 methods: GET & POST`

**LEVEL 1 (Resources)**

- Considers web as a collection of resources (instead of just URL endpoints). Ex: Vacation is a resource which is associated with other resources such as packages and reviews
- Therefore, it is a combo of `Resources + URI endpoints`

**LEVEL 2**

Apart from consider web API as a collection of resources, it:

- Uses all verbs such as GET, POST, DELETE, PUT ... . These verbs are used to manipulate the resources. Therefore, CRUD operations are implemented with the above 4 verbs. *You cannot carry out CRUD without using HTTP verbs*
- Therefore, it is a combo of `resources + URI endpoints + All HTTP verbs`

**LEVEL 3**

- Most RESTful. *Highest level of maturity!*
- Employs Hypermedia. It employs HATEOAS (Code on demand) which allows for further manipulation and the concept of discovery of resources on the server
- Therefore, it is a combo of `resources + URI endpoints + All HTTP verbs + HATEOAS`

Once an API has reached the third level, it is the most mature RESTful service it has ever been

## Designing a REST API

An API client needs an **Endpoint URL for the API**. The syntax:

```js
// The API URL endpoint:
https://<domain>/<product>/<version>/<resource>/{id}

// Base URL:
https://<domain>

// Grouping name (optional):
<product>

// Version of API:
<version>

// REST resource:
<resource>

// Resource ID:
{id}
```

### Domain examples

- `https://api.paypal.com` (It is common to use `api` subdomain)
- `https://app.ticketmaster.com` (It is also common to use `app` subdomain)
- `https://developer.uber.com`

**Pro-tip:** Keep a ***subdomain*** for your APIs. Do not use the same as the website i.e do ***not*** use `www`

### Product or package

This is ***optional***. You can logically group the API based on the products or the teams that will be working on them. For example, `https://api.store.com/payments/` or `https://api.store.com/discovery/` can be managed by a payments and a discovery team, respectively

Product , if used, helps multiple teams independently develop parts of the API

Without a product, we have a version of the API immediately after the domain. Ex: `https://api.store.com/v1`

### Version

- A version is useful when you expect the API to *evolve over time*
- Maintaining different versions insulates users (clients) from breaking changes in the API (Gradual adoption)

Version example: `https://api.store.com/v1`

**Alternative approaches:** Instead of a version in the endpoint, some API designers add the version in the ***headers*** or as ***query parameters*** (Ex: `?v=1`)

### Resource & ID

If you do not specify an `{id}` then the endpoint can work on a *collection of resources*. If you pass an ID, it will work on that particular resource

It is common convention to use ***plurals*** for the resource name (Ex: `books` over `book`). If you do not specify an ID then you get to work with a collection of resources and plurals help to think in terms of many resources

Example:

```shell
# Collection of resources:
https://api.store.com/v1/books/

# A particular resource (book by id 5661):
https://api.store.com/v1/books/5661
```

###  Root URL

The domain, product, & version form the root URL (up to the version). For example, `https://api.store.com/discovery/v1` is the root url and on this root url, you have resources, usually defined separately with a path like `/books` and so on

### Resource naming conventions

- Use ***"nouns"*** for naming resources (i.e Easy to remember it as name, place, animal, or a thing). Ex: `books` or `cities` or `users`
- Use ***"plurals"*** for naming resources (Easier to think of them as collections). Ex: `photos` over `photo`

The above two conventions help with actual resource. 

*What if you need endpoints for actions (calculations or derived resources) instead of a resource?* 

- Use a ***"verb"*** for your "action" endpoints
- Examples of verbs are `calculate`, `search`, etc
- It is common to combine actions (verbs) with query parameters for more refined actions
- A full example: `https://api.store.com/v1/books/search?q=harry+potter`

**Note**: 

- Difference between an action & a resource is that an action does not refer to any specific API resource 
- Actions can be a part of the "resource hierarchy". For example, if you need estimates (action) of a price (another action) the endpoint can be `/estimates/price`

### Resource associations

We can nest resources by specifying them in the endpoint in the order in which they make sense. For example, users have posts, posts have comments, and so on. Alternatively, people have followers.

Examples: 
- `/people/{id}/followers/{id}`
- `/accounts/{id}/transactions/{id}`

**Note**: Avoid deep nesting (too many associations) in the APIs. Pro-tip: Keep it at a ***max of 3***

### HTTP request flow for REST API 

As part of the *uniform interface*, client requires the following:

1. API endpoint URL (Which it uses to make GET/POST/PUT/DELETE (CRUD) requests to)
2. Query parameters that are available (if any)

In return, client specifies the following in the request:

1. Standard headers. Ex: An `Accepts` header (to indicate the type of data format it expects in response)
2. Custom headers (if required by contract. Ex: `x-auth-token`)
3. Body of the request (It is usually in a form that is defined by its `Content-Type` header)

The client will likely follow a *request schema*

### HTTP response flow for REST API

The server specifies the following in the response:

1. HTTP status code (Ex: `200`, `404`, etc) - Has to be always present!
2. Standard headers
3. Custom headers (if required by contract)

The client will likely follow a *response schema*

### HTTP status codes

1. **`1xx`: Informational** (Not used in regular API development)
2. **`2xx`: Success**
   - `201`: Created
   - `204`: No content
3. **`3xx`: Redirection**
   - `301`: Moved permanently
   - `304`: Not modified
   - `307`: Temporary redirect
4. **`4xx`: Client error**
   - `400`: Missing required field 
   - `401`: Unauthorized
   - `402`: Payment required
   - `403`: Forbidden
   - `404`: Resource not found
   - `415`: Unsupported media type (Ex: When client requests response data in XML but server supports only JSON)
   - `429`: Too many requests (Ex: User is allowed to use app from only 2 clients but 3 clients are trying to access at the same time)
5. **`5xx`: Server error**
   - `500`: Internal server error
   - `502`: Bad gateway
   - `503`: Service unavailable
   - `504`: Gateway timeout

### Using the appropriate verbs

Map the following 4 popular verbs to the intended CRUD operations:

1. GET for Read (Retrieve)
2. POST for Create
3. PUT for Update (Can also use PATCH in some cases)
4. DELETE for DELETE

### Creating a new resource

- Use `POST` and the request body contains the resource representation
- If successful, return a `2xx` (Ex: `201` created)
- If client error return `4xx` (Ex: `400` missing required field)
- If server error return `5xx` (Ex: `503` database unreachable)
- Response data:
	- May return the new resource instance (Ex: in JSON or XML format)
	- May return new link associated with the new resource instance (HATEOAS approach)

### Retrieving a resource

- Use `GET` and the request body is optional (mostly never populated)
- If successful, return a `2xx` (Example: `200 OK`)
- If client error return `4xx` (Ex: `404` resource not found)
- If server error return `5xx` (Ex: `500` internal server error)
- Response data:
	- A collection of data if no ID was specified
	- A single instance of request resource (if ID was specified)

### Updating a resource

- Use `PUT` for updating all the fields of a resource instance i.e replace resource with new data
- Use `PATCH` for updating only some fields of a resource instance i.e update only some attributes
- PATCH maybe more performant on resources that are *large data objects*
- Usually a PATCH attribute is defined as a *query parameter*
- Ex: `PATCH https://api.store.com/books/121?title=The+Mossad`
- Request body contains the data to update the resource
- If successful, return a `2xx` (Ex: `201` created or `204` no content - if there is no data sent back in response body)
- If client error return `4xx`
- If server error return `5xx`

### Deleting a resource

- Use `DELETE` and generally body in request is left empty (For example, {id} takes care of identifying resource)
- If successful, return a `2xx` (Example: `204` no content - if there is no data sent back in response body)
- If client error return `4xx`
- If server error return `5xx`

### Notes

- Custom and standard headers can be part of each request & response (For any CRUD operation)
- We can use only POST instead of PUT & PATCH (flexible). twitter uses only GET & POST but the way their endpoints are listed differ. Ex: `POST` on `/saved_searches/destroy/{id}` to delete a tweet

### Data format

A REST API format need not only be `json` (misconception). It can be defined as per the contract in the uniform interface. It can be XML or CSV or any other type agreed upon by the contract

If an API supports *multiple data formats*, it can be considered a ***feature*** of that API

- Client requests a format in the form of `Accepts` header. Ex: `Accepts: application/json`
- If successful, data is available in the response in requested type, indicated by a `200` success status code and the `Content-Type` header lists the data format which is expected to match the value of `Accepts` header that came in the request
- If data format is not supported by server, it sends back a **`415`** unsupported media type status (no body)

**Alternatively**: A suffix with extension (`/weather/{day}date.json`) or a query parameter (`/weather?day=xx/xx/xxxx`) may also be used in the request to indicate expected response data format

**Pro-tip**: As a REST API designer, ensure documentation of supported formats in the documentation portal

## API error handling patterns

The error information is contained in:

1. Response header: Start line with `status-code` & `Reason-phrase` (Ex: `404 not found`)
2. Response body: It contains an application status code as per the format for the body

This error information can be used by the client to log it and show the end user a friendly error message

Error options:

1. **Error information only in header**: Only have status code but nothing in body (or no body)
2. **Error information only in the body**: *This is the least preferred approach*. Every call will have a success status even if it is a failure - failure is only detected in the body (Facebook used it in the early day but not anymore) 
3. **Error information in both header & body**: The ***recommended*** approach. Status code will indicate type of error and the code for error inside the body will give more details regarding it. In this approach, there is:
	- **HTTP error status**
	- **Application error response (inside body)**

### How many HTTP status codes to use

As an API developer, decide on which HTTP status codes are of the most use to you and only use that subset. Recommendation: Do not use more than **10** of them

**Why limit it?**

- Hard for API management team to manage them
- Hard for clients to adapt every one of them

### Application error response handling patterns

How to design an Application error response?

1. Informative and actionable. Ex: If a field is missing then it should tell which field and how the app developer can fix it
2. Simple. Ex: It must not be hard to understand the error
3. Consistent. Ex: All responses must use one of the decided error codes & keep the format consistent

**Application status (error) code**

- It is recommended that this be a *number*. Ex: `1002` or a prefixed, namespaced number like `US_2899`
- It must be *well-documented*. The API developers should not create multiple error codes for the same error

**Application response template**

The idea is to maintain a standard format that accounts for both success & error cases. The two approaches are:

1. No wrapper
2. Response wrapper

In "no wrapper" case, the format is similar to what is below:

```shell
text: message # An overview of the error
timestamp: datetime # Maintaining time for logging purposes
method: HTTP verb # Status code indicating success/failure
endpoint: URL # Again, logging the API endpoint for debugging
errors: List # A list of "ERROR OBJECTS" (Multiple errors possible)
payload: Optional # If no error, it contains response data
# If error, we usually leave it blank
```

Each error object can contain the following:

```shell
code: number # A unique code for identifying the error
text: message # A message about the error 
hints: actions # A list of possible resolutions to the error
info: documentationURL # To refer to documentation portal
```

In the "Response wrapper" style, the format remains the same but the "payload" is separated out from the "status". In this approach, your API may also send 200 (success) always:

```js
{
  status: { text, errors, method, ... },
  payload: { ... }
}
```

**Note:**

- Primary consumer of your API is the client app developer
- As best practice, include hints & info (document links) in error response
- Use a standard format for your responses to avoid confusion

## API versioning patterns - Handling changes

An API (& even software) *changes over time*. That is the only constant and it cannot be prevented!

We cannot force internal & external developers to change to a new API all of a sudden. We don't have control over it and we do not want to cause distress to client app developers

### Types of changes

1. **Non-breaking**: No client app code change required. For example, sending additional property called `pictures`
	- New resource or operation (Ex: new `/deals` resource endpoint)
	- Adding optional parameters or properties (Ex: `/existing?limit=3` adding a limit option)
2. **Breaking**: Client app will break if this happens
	- Change HTTP verbs or methods (Ex: POST to PUT)
	- Delete an existing API operation (Ex: remove /search?q=xyz)

### When to change your API?

First, we must ask ourselves, "Can we avoid the changes?". If the change does not add value, don't make it

However, in many cases it is not possible to not have changes. We can follow some ***best practices*** in such cases:

1. Provide planning opportunity to client (external) devs i.e Sufficient time to port to new API
2. Support backward compatibility (Keep it a non-breaking change if possible)
3. Provide document support to devs i.e Keep snippets and examples on developer portal
4. Limit frequency of changes
5. ***Always version your API since day 1***

Try to make non-breaking changes if possible. These are *minor changes* (Ex: Limiting attribute length to a value more than before which does not affect client's current attribute length - no client change required). For *major changes*, it is usually a breaking change and we must follow the best practices

### How to version your APIs?

Keep multiple versions available. When you release a new API:

1. Old API must still be available and marked as deprecated
2. New API becomes available and fully supported
3. Overt time we can retire the old API i.e unsupported (Provide a roadmap & sufficient time to developers to adapt to new API) 

**Versioning techniques**:

1. In the API *(Most popular)*. Ex: `https://domain/product/version/resource/{id}`
2. In the header. Ex: `x-api-version: <version>`
3. In the query string. Ex: `/resource/id?version=<version>`

**Version formats**:

1. Prefixed with a `v` *(Most popular)*. Ex: `v2`
2. Has a `major.minor` format. Ex: `2.4`
3. Has a date i.e date of API version launch. Ex: `2012-09-11`

Example: `https://api.store.com/v2/books/9234`

## REST API cache control patterns

What are the points at which caching can occur? There are many!

- Client caching (Ex: browser)
- ISP caching
- Gateway caching (On server network gateway)
- Mid-tier caching (On the server)
- Database caching

### Who controls caching?

The ***API*** controls it (Ex: Via the cache control headers). It is part of the API design decision making

### Why cache?

- It improves performance 
- Reduces chattiness (Too much back & forth between server & client)
- It is good for scalability or throughput
	- Imagine you are getting 30 calls/second and DB supports 50 ops/sec
	- Suddenly, you're product is popular and more people have started using it
	- You start hearing complaints that certain calls are failing
	- On investigating, you find that calls are now 60/sec and DB is failing for the extra ones
	- Here, we can cache certain requests on the mid tier itself, instead of upgrading DB capabilities and so on

### What to cache and for how long?

The factors to consider are:

1. Speed of change (Frequency of change)
2. Time sensitivity
3. Security

***Static*** contents like JS/CSS/HTML/images don't change very frequently. Hence, their speed of change is less. Therefore, they can be cached for days or weeks or more

API, however, is most likely to contain very ***dynamic*** data. Some APIs like stock market trading and news might be very time sensitive. Other APIs that contain user data require security and should not be cached on the gateway or ISPs

### Cache control directives

The API can define **`cache-control`** header which places directives for caching mechanism on client and intermediaries. This header can also be sent in the request (by API consumer - client) to control caching of the request data

Syntax: `Cache-Control: <directive1>=<value>, <directive2>,...`

#### Directives

1. `public` vs `private` (Ex: `Cache-Control: private, max-age=60`)
	- Public means the data can be cached on intermediaries as well as the client
	- Private means the data can only be cached on the client (Not on intermediaries like ISPs or Gateways). Assume you have user information that is financial in nature - you do not want this on your ISPs but only on the client
2. `no-store` (Ex: `Cache-Control: no-store, max-age=60`)
	- Clients generally store cache on the filesystem. This is not very secure as it can be accessed by some other entity
	- This rule states that the data cannot be "stored" anywhere (Hence, browser cannot save cache locally on the system). In-memory caching should likely be fine (?)
	- It is used for sensitive data like bank transactions, medical records, etc
3. `no-cache` (Along with the `Etag` header) (Ex: `Cache-Control: no-cache, max-age=60`)
	- Always get data from the server
	- Subsequent request to the same URL will return different data
	- *Drawback*: Inefficient as we don't use caching 
	- *Solution*: Used to check if the data has changed on the server (returns `304 Not modified` otherwise, with no body, leading to a faster RTT at least). If data has indeed changed, send a `200 OK` response with the data
	- Useful for time sensitive data (extremely dynamic)
	- If you have *large responses*, consider using the `Etag` header
4. `max-age` (Ex: `Cache-Control: private, max-age=60`)
	- Instructs how long to cache the resource for in **seconds**
	- Be very careful in setting this value as it can lead to unpredictable behaviour and complaints if not set properly

*An example of `no-cache` & `Etag`:* You are developing a stock market trading API. Caching is not an option here. However, server can still respond immediately without data & the processing time involved in it by checking the `Etag` hash

*An example of `no-store` & `no-cache`:* Saving social security numbers SSN or passwords must not be stored nor cached. It can lead to leaks and malicious use

## REST API response handling patterns

> "Client must control response data"

What this means is that different clients have different requirements with respect to response size & attributes. The client must be able to control what it needs and how much of it. In general, it allows for *better performance*, *granularity control*, & *optimised resource usage*

**Partial response** is an example of response handling. A client may request only a subset of the data that comes normally in a response. This can be done through specifying the fields. It is useful for minimising network bandwidth and other performance benefits

***Pagination*** of data is an example. A mobile client might request smaller dataset of a resource at first than a desktop client. It may load the remaining or more on demand, when it chooses to. This is a performance and UX consideration and is very important

Advantages:

- Better performance
- Better CPU utilisation
- Battery saver

We can still have the ***same*** API endpoints

How do we do it then?

1. Custom partial response support
2. Use *GraphQL* (A different way of doing APIs and built for just this kind of a thing, by facebook)

### Custom partial response support

We can add query parameters:

1. *Single query parameters*: These list the fields you want to access
	- Ex: `/resource/{id}?fields=foo,bar,baz:(beep)`
	- The above might return only `foo`, `bar`, & `baz.beep` in the response data
2. *Multiple query parameters*: These either include or exclude fields. Ex: using terms like `only` or `omit`
	- Ex: `/resource/{id}?omit=bar,baz`
	- The above might return every field except `bar` and `baz` in the response data

### Adding pagination support

Pagination deals with collections of resources. It splits them up into "pages" which the client can ask instead of getting the whole data at once. This is good for performance (allows for on-demand loading & optimised resource usage)

How to implement pagination?

1. Cursor based
2. Offset based *(most popular)*
3. Use of HTTP link header

In *cursor* approach, you have an "envelope based response" i.e you have a response similar to what is below:

```js
{
  data: { /* endpoint data is here */
  paging: {
    cursors: {
      after: /* Some hash */
      before: /* Some other hash */
    }
  }
  next: ... /* link to api endpoint to get next page (HATEOAS) */
  prev: ... /* link to api endpoint to get prev page (HATEOAS) */
}
```

This cursor approach has many benefits as [listed here](https://jsonapi.org/profiles/ethanresnick/cursor-pagination/)

In *offset based* approach:

- Makes use of query parameters
- We need to supply an **offset** & a **limit** at the very least
- Offset specifies which page to fetch i.e which record to start from
- Limit specifies how many records to retrieve (from the offset point)
- If you need bidirectional search, a *direction* query string parameter might also be supplied

Ex: `/books?offset=0&limit=10` and then `/books?offset=1&limit=10` for the next page

This is the most common approach

In *HTTP link header* approach: We use the `Link` header that contains a URL to the next page in the API. Approach is not very commonly used

## REST API security

In NodeJS, we can make use of *PassportJS* for our authentication support.

There are many types of authentication. *We use the selected authentication mechanism for our API endpoints that require protection.* Let's take a look at some of them.

### Basic authentication

Basic Authentication is part of standard HTTP specification

- Client sends an HTTP header in the request
- The header is **`Authorization: <value>`**. The value is **Base64** encoded data
- The encoding contains data in the format: `userid: password` when decoded!
- Server decodes the header and checks the userid & password against its database 
- If the userid & password match, `200 OK` is sent back in response
- If they don't match, `401 Unauthorized` is sent back in response to indicate failure

**Note**: Basic authentication should be used only with **HTTPS/TLS** (& not HTTP). HTTP data is sent in plain text and hence, userid & password can be detected easily by *man-in-the-middle-attacks*. In HTTPS, the data is encrypted - cannot decipher even if the request is accessed in the middle

**Problems with basic authentication:**

1. Need for sending credentials in every resource call (All the more likely to get attacked)
2. Need for session management on the server (Not very RESTful!)
3. Sharing of credentials for building mobile apps (when you already have another, desktop app)

### Token based authentication

Token based auth is better than basic auth.

A *token* is an encoded string that was generated from a hashing algorithm (such as HS256 or RS256). Therefore, they are *hashed strings*

- Client provides credentials the first time the server API is hit
- The server API generates a token and sends it back to client
- The client can store this token locally (ex: local storage)
- For subsequent calls to server, the client will use this token instead of the credentials 
- If token is valid, `200 OK` with response data is sent back
- If token has expired or tampered with then `401 Unauthorized` is sent back in response
- Client must again fetch a new token once it expires or is tampered with

**Advantages of token approach**

1. Client credentials are not used all the time (safer)
2. Eliminates the need for the API server to maintain a session on the backend (More RESTful)
3. API server has control over the token i.e expiry, ability to revoke token at any time, and so on

**How do we send a token?** We have multiple options:

1. Header
2. Query string parameter
3. Cookie
4. Request body

**JSON WEB TOKENS (JWTS)** are the most commonly used type of tokens in the REST API world

### API secret & key

- These are long strings just like userid & password
- Unlike userid & password, they are NOT meant for humans (but for machines)
- They are *very long strings* so that it is harder to crack them by brute force

1. *API key* identifies an API consumer. It is also called a *Client key* or *Client ID*
2. *API secret* is used by client prove its identity i.e as a password or as a digital signature

**Process:**

- Client sends API key & signature to server
- The signature contains API secret in the header or payload built using a *hash* function 
- The server knows the secret and hence is able to decipher the client signature
- Server validates client key & client secret with the key & secret that it holds 

**Advantages:**

1. API key & secret are used for **authentication**
2. It is also used for **analytics**. Ex: How many machines accessed an S3 bucket? What type of machines were they and what was the frequency of access?
3. It is used to get **tokens** from the server
4. **Rate limiting** (Send `429 too many requests` if rate exceeds)

**API key can be sent in:**

1. HTTP Header
2. Query string
3. Request body

*Rate limiting should NOT be done at the API layer.* This is quite difficult to manage. Instead, use a cloud service where your API resides (API management platform like ones provided by AWS) to manage rate limiting and similar limitations

**API key & secret can be used for the following:**

1. Basic authentication (Use it in place of credentials)
2. Token based authentication (We can provide key & secret instead of credentials to get back a token)
3. *Anonymous access by way of digital signatures* (Ex: Accessing an S3 bucket does not require authentication with credentials) 

### OAuth 2.0

Defined in RFC6749. It is a flexible framework which provides authorisation to third party apps (clients) to use resources owned by the user & to get user details (using OpenID)

OAuth 2.0 uses tokens (other mechanisms are allowed too but they aren't very popular)

- The access token is given via **grants**
- User is in control of the **scope** (what resources to expose & how much)
- Client app requires an *API key & a secret* to begin OAuth2.0 process

Read [OAuth2.0 notes](https://github.com/pushkar100/notes-apis-services-auth-security/blob/master/api-basics.md#security-authentication--authorization) for more information on how it works

### Last mile security

- [Last Mile Security - Securing the API Backend](http://acloudfan.com/last-mile-security)
- [Microsoft Azure LMS](https://azure.microsoft.com/en-in/resources/videos/last-mile-security/)
- [Google Cloud LMS](https://cloud.google.com/apigee/docs/api-platform/security/last-mile-security)

## REST API specifications using Swagger 2.0

There are two approaches to REST API specifications:

1. Contract last: The API contract is made after the REST API has been built
2. Contract first: The API contract is made before the REST API has been built. Use this approach

### Swagger 2.0

Swagger has become the de-facto standard for creating API specifications and it is supported by the major API management tools

**Benefits:**

1. Developer documentation for the API can be generated
2. Automated proxy creation (Server code generation) (Ex: NodeJS code to handle API requests)
3. Client code generation (Ex: Java code to make API requests)
4. Service virtualisation (Mocking servers so that we can test our APIs)

**Alternatives to Swagger:**

- RAML
- WADL (by W3C)
- API Blueprint

**Swagger walkthrough**

1. Swagger was built by a private company for internal usage and later open sourced
2. The Open API foundation under the Linux Foundation adopted it
3. It is, therefore, also called the **Open API Specification (OAI)**
4. It uses a *configuration* file to specify the REST API and is written in YAML or JSON
5. YAML is a *superset of JSON* and is more human readable (*meant for human consumption*). It uses indentation instead of brackets
6. YAML uses `- <value>` on every new line to indicate an array of values
7. You can write the specification using YAML/JSON in an editor of your choice. Some YAML extensions might make your job easier
8. It also common to use [https://editor.swagger.io/](https://editor.swagger.io/) to create the specification online
	- It allows us to view the generated documentation on e right panel
	- We can also download scaffolding code for the server and client in the language of our choice
9. Another approach is to download `swagger-editor` and `swagger` NPM packages and use them on the CLI to generate server/client code & documentation after providing the YAML/JSON file

Example Swagger 2.0 file:

```yaml
swagger: "2.0"
info:
  description: "This is a sample server Petstore server.  You can find out more about     Swagger at [http://swagger.io](http://swagger.io) or on [irc.freenode.net, #swagger](http://swagger.io/irc/).      For this sample, you can use the api key `special-key` to test the authorization     filters."
  version: "1.0.0"
  title: "Swagger Petstore"
  termsOfService: "http://swagger.io/terms/"
  contact:
    email: "apiteam@swagger.io"
  license:
    name: "Apache 2.0"
    url: "http://www.apache.org/licenses/LICENSE-2.0.html"
host: "petstore.swagger.io"
basePath: "/v2"
tags:
- name: "pet"
  description: "Everything about your Pets"
  externalDocs:
    description: "Find out more"
    url: "http://swagger.io"
- name: "store"
  description: "Access to Petstore orders"
- name: "user"
  description: "Operations about user"
  externalDocs:
    description: "Find out more about our store"
    url: "http://swagger.io"
schemes:
- "https"
- "http"
paths:
  /pet:
    post:
      tags:
      - "pet"
      summary: "Add a new pet to the store"
      description: ""
      operationId: "addPet"
      consumes:
      ...
    ...
  ...
...
```

**Swagger 2.0 specification file**

The first line always begins with **`swagger: 2.0`**

Specification file has 3 parts:

1. Contains ***general information*** about the API. It has the following 4 parts:
	- ***Metadata*** (The `info` object which has 5 properties)
		- `info` contains: `version`, `title`, and `description` (self-explanatory). Note: Use `|` to create a multiline description
		- `info` also contains `contact` (Whom to contact for API details)
			- `contact` contains `name`, `url`, `email` 
		- `info` also contains `license` information
			- `license` contains `name` & `url`
	- ***Root document***. It has properties that are used for creating endpoint URLs
		- `host`: The hostname - domain plus port (Ex: `https://api.acme.com:3000`)
		- `basePath`: The base path which includes the product (optional) & the version (Ex: `/v2`)
			- It is the part of URL between the `<host:port>` and `<resource>` 
		- `schemes[string]`: `https` (For production or development) or `http` (Use only for development)
		- `consumes[string]`: Mime types array (Ex: `application/json` or `application/xml`)
		- `produces[string]`: Mime types array (Ex: `application/json` or `application/xml`)
	- ***External documentation***. Ex: Links to an API documentation portal/website
		- Defined as `externalDocs` and it contains a `description` and a `url`
		- The external doc will likely contain more information about API such as FAQs
		- It can also contain more info about the paths
	- ***Tags***. This helps assign keywords so that it is searchable & filterable
		- Contains an array of tags
		- Each tag is a combination of a `name` and `description` value
		- It helps developer portal tools to discover elements of the API
		- It is referred in paths or operations
2. ***Definitions***. Contains:
	- `definitions`: Request/response schema
		- `$ref: "#/definitions"` from operation
		- Each definition contains a `schema` and `examples`
		- The `schema` is a JSON schema defined in `http://json-schema.org`
		- The `examples` is the response/request data example used by mock servers during testing. Format: `Mime-type: { /* example as per the schema */ }`
	- `parameters`: Request/input schema
		- `$ref: "/parameters"` in operation parameters
		- Each parameter contains a schema defined by:
			- `name`
			- `description`
			- `in` (Is it sent in `body`, `query` parameter, `header`, `path`, or `formData`?)
				- If in `body`, we need to specify a schema i.e either a new one or existing definition but no name is required
				- If in `header` then it is the custom header name
				- If in `query` then it is the query string parameter name
				- If in `path` then it is a path variable name. Ex: `id` in `:id`/`{id}`
			- `required`
	- `securityDefinitions`: A security schema
		- For security from the operation
		- `type`: `basic`, `apiKey`, or `oauth2`
		- `name`: Used for associating with operation
		- `description`
		- `in`: `query` or `header`
3. ***Paths***. The actual endpoint paths
	- It makes references to the previous two parts
	- Ex: `$ref` can be a reference tag
	- `paths` contains multiple paths (an array)
	- Each path can define itself using `/{path}`
	- Each path can have multiple operations
	- Each operation is defined by an HTTP verb (Ex: `get`, `post`, `put`, `delete`)
	- Operation specification format:
		- `summary`: Short description
		- `description`: Long description
		- `tags[]`: Refer to the predefined tags or can be newly created
		- `externalDocs`: External documentation that is specific to the operation
		- `consumes[string]`: Mime types array (Ex: `application/json`)
		- `produces[string]`: Mime types array (Ex: `application/json`)
		- `parameters`: Use `$ref: "#/parameters"` to pick an existing parameter or we can define a new one right here. We can have multiple parameters (use an array)
		- `responses`: We can send back multiple responses (an array)
			- One response per HTTP status code (Ex: One response for `401`)
			- The response contains a `description` and a `schema`
			- We can use a response schema defined earlier using `$ref: "#/definitions` or create our own schema
			- Note: Use a `default` to match all other HTTP status codes we did not define
		- `security`: Adds the security to the response path (an array)
			- We can use a security definition with `- <a security definition>: []` (Note: we do not use `$ref` for linking security definitions)
			- The array `[]` is only used in case of OAuth2.0 to provide the *scopes*

***The convention used is to define the paths before the definitions!***

**Note**: 

- The definitions are referenced by using the json **`$ref`** operator
- The definitions may be placed in a *different files and linked to paths/operator* by way of `$ref` with absolute path
- You *may skip the definition section* and describe the schema under the operation but then the schema will not be re-usable. *As a good practice use definitions*
- The request schema is needed for the operation only if the value of `in` in `parameters `definition is `body`

Example:

```yaml
swagger: "2.0"
# ************* Part 1 ************* 

# 1.1 The metadata:
info:
  version:
  title:
  description: Can be |
  multiline |
  description.
  
  contact:
    name:
    url:
    email:
  
  license:
    name:
    url:
    
# 1.2 The root document:
host:
basePath:
schemes: # An array of values
  - https
  - http
producer: # An array of values
  - application/json
consumer: # An array of values
  - application/json

# 1.3 The tags:
tags:
  - name:
    description:
  - name:
    description:

# 1.4 The external documentation:
externalDocs:
  - description:
  - url:

# ************* Part 3 (By convention, appears before part 2) ************* 

# 3.1 Paths:
paths:
  /vacations/{id}:
    get:
      summary: get a vacation
      description: Returns vacation package identified by id
      tags:
        - vacations
        - packages
      externalDocs:
        description:
        url: 
      consumes:
        - application/json
      produces:
        - application/json
      parameters:
        - $ref: "#/parameters/IdInPath"
      responses:
        "200": # One response per HTTP status code
          description: Gets the specified vacation package
          schema:
            $ref: "#/definitions/Vacation"
        "404":
          description: When a package is not found
          schema:
            $ref: "#/definitions/NotFoundError"
        default":
          description: For all other http status codes, send a generic error
          schema:
            $ref: "#/definitions/GeneralError"
       security:
         - KeySecurity: []

# ************* Part 2 ************* 

# 2.1 Definitions:
definitions:
  GeneralError: # We are defining a schema for all error messages
    required:
      - message
    properties:
      message:
        type: string
  NotFoundError: # An error message schema for when a resource is not found
    required:
      - message
      - hint
    properties:
      message:
        type: string
      hint:
        type: string
  Vacations: # Schema for one our our resource responses
    required:
      - name
      - description
      - type
      - numOfNights
    properties:
      name:
        type: string
      description:
        type: string
      type:
	    type: string
	    enum: # We can create enumerations in our data
	      - resort
	      - cruise
      destinations:
        type: array
        items:
          $ref: "#/definitions/Location" # Can reference other schemas too!
        minItems: 1 # The array can have a min/max items constraint in a schema
        maxItems: 12
      numOfNights:
        type: number
        minimum: 1 # We can have a min/max constraint on a number type in a schema
        maximum: 31
  Location:
    required:
      - city
      - country
    properties:
      city:
        type: string
      country:
        type: string

# 2.2 Parameters:
parameters:
  IdInPath: # A custom parameter to be used in paths Ex: `/v1/books/{IdInPath}`
    name: id
    in: path
    type: string
    required: true

# 2.3 Security definitions:
securityDefinitions:
  KeySecurity: # Custom name (In query like so: `/v1/books?api_key=<value>`
    type: apiKey
    in: query
    name: api_key
```

## REST API management

[Learn more about it from the lectures (1hr 15min)](https://www.udemy.com/course/rest-api/learn/lecture/6091400#overview)

---

**THE END**
