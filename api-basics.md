# API & Web Service Introduction

 [Course link](https://www.udemy.com/course/api-and-web-service-introduction)

**Contents**

1. [What is an API?](# What is an API?)
2. [API details](# API details)
3. [What is a Web service?](# What is a Web service?)
4. [HTTP](# HTTP)
5. [Data representation formats](# Data representation formats)
6. [Protocols for Web services](# Protocols for Web services)
7. [Types of apps](# Types of apps)
8. [Security, authentication, & authorization](# Security, authentication, & authorization)
9. [Types of authentication & Postman Introduction](# Types of authentication & Postman Introduction)
10. [AWS](# AWS)
11. [Webhooks](# Webhooks)
12. [Microservices](# Microservices)

## What is an API?

API stands for "Application Programming Interface". What does it mean? 

- You have a ***software that can run a task (application)*** and 
- A ***program that actually runs the task (programming/code)***
- Finally, the user has access to ***a place from where you tell the program to run the application task (interface)*** 

In terms of sequence of actions, it helps to think of an API as ***IPA***. You interface first which runs the program which then completes the application task

The application is the entity that maintains the program (Ex: Google application maintains its search program)

### Examples of APIs

- Google translate app on the phone is an interface
- Typing a word and hitting enter will call the program (on the server)
- This program runs the application's task (i.e translation) and returns the result

We can think of all mobile and desktop apps as interfaces where our actions run the programs that perform a task that the application intends to complete

Most of the time, this **program** resides on the **server**

### What do people mean when they refer to an API?

Usually, even though the interface might be an icon on the phone or a browser, when people say "This or that API" they are usually referring to ***the program that runs on the server***

The program is accessible via a path on the server and hence, we can ***access an API by hitting the URL for it*** (known as the **endpoint**)

### Advantages of APIs

1. It can be *used* (You don't have to write the program yourself)
2. It is *platform independent* (Can hit it from any device, it does not matter. There are some exceptions)
3. It is *upgrade safe* (You don't have to worry about the program changing at the interface level)

### Why are APIs on the server?

It allows us to hit the program from anywhere, using any script (Python, curl, etc) and it just works. Therefore, for an application to maintain the API in the same place where it resides (i.e server and *not the client*), provides it a lot of **flexibility**!

### Simplified definition of an API

***Tell a program that you do not own, to run!***

## API details

 Every API has at least 3 parts:
 
 1. Request
 2. Program
 3. Response

**Example:** You make a request with https://www.google.com/search?q=beard (interface) which runs the search task (program) and returns the response (Ex: google page update)

For GUIs, the interface can be the client side objects such as buttons, inputs, etc. For more complex APIs that developers work with, it is usually a folder on computer (i.e a N/W server and a path with params that responds to the request)

Therefore, the interface can reside on the server (application) itself. For example, you can hit the google search from the search bar and the interface becomes the search bar (on the client). But, you can also hit a URL for the same (i.e a path on the server), maybe even from your CLI. The interface in this case is the server itself (i.e the endpoint that triggers program inside the application on being hit)

## What is a Web service?

Web means the internet and service is another term for an API. Therefore, a ***Web service is an API that uses the internet***

Most popular public APIs such as Github's, Ebay's, etc are web services. All Web services are APIs. However, not all APIs are Web services. At times, an application may be interacting with another application internally, without hitting the network (locally). Such interactions are most definitely not Web services

In Web services, the program is on a server i.e a computer not on the client.

### Points to note

1. A Web service needs a **format** to transfer data over the N/W (ex: XML, JSON)
2. It also needs a **protocol** (ex: REST, SOAP, XML/RPC)

## HTTP

 A Web service is an API and since it runs over the internet, it uses **HTTP** for the request/response communication

HTTP stands for **HyperText Transfer Protocol**. HyperTest means text that can take you somewhere which regular text cannot. That is, when you hit www.google.om or click an anchor tag, the HTTP protocol automatically hits that URL with a request and comes back with a response (which is why all URLs starting with `www` are auto-prefixed with `http` or `https`). This is the mechanism that is used to transfer data

### HTTP request & response

The content structure of the HTTP request or response is as follows:

1. Start line (Tells program to start) **Mandatory**
2. Headers
3. Blank line
4. Body

Only the start line is mandatory.

#### Start line (Also called Request line or Status line)

The ***HTTP version*** is contained in both the request and response start lines (Ex: `HTTP/1.1` or `HTTP/2`)

In the request, it also contains the ***method*** (Ex: GET , POST, PUT, DELETE. etc). We even have the request path (folder) and the params (query string). For example, `/search?q=beard`

In the response, it contains the ***status code*** which indicates if the request was a success or not (Ex: `200` for OK, `400` for client error, and `500` for server error)

##### Request line format:

`<method> [<path+queryparams>] <httpversion>`

Examples: `GET /search?q=beard HTTP/1.1`, `POST HTTP/1.1`, etc

##### Status line format:

`<httpversion> <statuscode>`

Example: `HTTP/1.1 200 OK`

#### Headers 

Each line in the headers section is one individual header (key & value)

The common request headers are **host** (domain) such as `www.google.com` and **token** (if request requires authentication) and so on

The common response headers are **cookie** (holds all the cookies)m **content-type** (is it an HTML page or a JSON, etc?)

On both sides, we can have custom headers (usually prefixed with an `x-`). For example, your CDN provider might allows some custom headers to pass through and they carry a special meaning to your application

You can have **as many lines (Headers)** as you want in the headers section. This means that we can have **custom headers** too (such as the `x-` prefixed ones used as per convention)

#### Blank line

This exists just to differentiate header section from the body section

#### Body

The body can be empty or contain information (i.e contains the ***content***)

In the request, if it is a GET method then the body is empty. For other methods where it needs to send (Ex: POST) the server some information (ex: username & password), it will contain such data in the body

In the response, the data can be an HTML page or a JSON response or XML, depending on what was requested and how the server handled the request

##### Body structure

The structure of the body depends on the **content-type** header. The content type is present on both the request and response HTTP packets in order to inform the server and client, respectively, on how to read the data. 

There are two parts to the `content-type` header: `<type>/<subtype>` (For example, `application/json`, `image/png`, or `text/css`, etc)

#####  Content-types used for sending or receiving data

The `application/json` & `application/xml` are two common data formats for sharing data in an API

#### Demo:

![HTTP packet example image](https://www.ntu.edu.sg/home/ehchua/programming/webprogramming/images/HTTP_ResponseMessageExample.png)

**Note:**

- There are 4 main methods used (They can be thought of as **CRUD** operations):
	- `GET`: To query data
	- `POST`: To create data
	- `PUT`: To change data
	- `DELETE`: To delete (remove) data
- **Idempotence of methods**: Does repeating it cause result to stay the same? Is it safe to repeat? Only ***POST*** is not idempotent!
	- GET: Yes (Querying the same data again and again is fine)
	- POST: ***No*** (Adding more value does affects resulting data)
	- PUT: Yes (Applying the same change over and over again is fine)
	- DELETE: Yes (Once you delete and repeat deletion, nothing is left to delete)
- Standard HTTP status codes:
	- `2xx`: Success codes
	- `3xx`: Redirections
	- `4xx`: Client errors
	- `5xx`: Server errors

#### Standard headers (Non-exhaustive)

##### Common

- `Date`: of the header generation
- `Cache-Control`: Contains caching directives in both request and response
- `Connection`: Specifies if connection should stay open i.e `keep-alive` after current transaction finishes

##### Request

- `Accept-Language`: The language the client accepts ex. `en-US` for US English
- `Cookie`: The cookies shared with server
- `User-Agent`: The user agent string
- `Referer`: Address of previous web page from which to current page was requested

##### Response

- `Age`: Time in seconds the object has been in a proxy cache (`0` means just fetched from the server)
- `Location`: The URL to redirect a page to (Usually in a `3XX` or `201` status response)
- `Server`: Describes the software used by origin server that handled request (Ex: nginx or apache information)

#### Entity (Common)

- `Content-Type`: Media type of request or response body (content)
- `Content-length`: Size of the body in bytes
- `Content-Encoding`: Used to compress data as per the content-type

## Data representation formats

### XML

XML stands for **eXtensible Markup Language**. It was created in 1997 by the **W3C**, which has created other standards for the web as well, such as HTML, SOAP, XPath, XMLSchema, and so on

It is a data representation format (used in APIs)

It is constructed similar to HTML with tags (Ex: `<pizza>...</pizza>`) but unlike HTML the tags describe the data point rather than a DOM object. Also, HTML is not extensible while XML is. This means that XML tags can be customized whereas in HTML we have to use pre-defined tags (that the browsers understand)

The **`content-type`** header when you send XML data in request and response is set to **`application/xml`**. In this case, the HTTP **body** will contain data in XML format.

An XML file example (Note: The first line is for documentation or information only):
```xml
<?xml version="1.0" encoding="UTF-8"?>
<note>
  <to>Tove</to>
  <from>Jani</from>
  <heading>Reminder</heading>
  <body>Don't forget me this weekend!</body>
</note>
```

#### Related XML standards

W3C has come up with other XML-related standards:

1. **XPath**: It is a query to uniquely identify an XML element (can be used with HTML too)
2. **XSLT**: A standard to convert XML from one XML format to another XML format
3. **XMLSchema**: It is a schema language for specifying the type of data that an XML document must adhere to. It uses something known as an **XSD** to define the schema (Earlier, they'd use DTD)

There are other standards too!

Example of an XML Schema that uses XSD:
```xml
<?xml version="1.0"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">

<xs:element name="note">
  <xs:complexType>
    <xs:sequence>
      <xs:element name="to" type="xs:string"/>
      <xs:element name="from" type="xs:string"/>
      <xs:element name="heading" type="xs:string"/>
      <xs:element name="body" type="xs:string"/>
    </xs:sequence>
  </xs:complexType>
</xs:element>

</xs:schema>
```

### JSON

JSON stands for **JavaScript Object Notation**. It was created in 2001 by a proponent of JS, ***Douglas Crockford***

The data is represented similar to an object in the javascript language. Instead of tags like in XML, the JSON contains `key: value` pairs where the keys are specified within double quotes (`"`) and the values can be the common values like in a JS object (strings, numbers, arrays, and so on). We cannot, however, have more complex JS values such as functions, sets, etc

The **`content-type`** header for an HTTP body that contains JSON in a request or response is **`application/json`**

JSON is simple and the information is available in an easy to read format [here](https://www.json.org/json-en.html)

Example JSON:
```json
{
  "glossary": {
    "title": "example glossary",
    "GlossDiv": {
      "title": "S",
      "GlossList": {
        "GlossEntry": {
          "ID": "SGML",
          "SortAs": "SGML",
          "GlossTerm": "Standard Generalized Markup Language",
          "Acronym": "SGML",
          "Abbrev": "ISO 8879:1986",
          "GlossDef": {
            "para": "A meta-markup language, used to create markup languages such as DocBook.",
            "GlossSeeAlso": ["GML", "XML"]
          },
          "GlossSee": "markup"
        }
      }
    }
  }
}
```

#### JSON vs XML: Which is better?

XML has been falling out of favour and is almost never picked over JSON nowadays. There are a few reasons for this:

**JSON is lightweight and more popular:**

1. JSON notation is simpler. It is less verbose and there are no tags. Terser
2. Because JSON is terser, it makes sense to use it over the network. The XML payload for the same data is likely to consume more bytes than JSON and hence, will increase the time required to transport it over the network

**XML is more powerful:**

1. We can convert XML from one format to another using XSLT
2. We can provide security with XML Schema (Do not respect incoming data if schema is not followed). Such checks are not so prevalent with JSONs

#### JSON Schema

JSON too has a schema to represent the object structures and value types but it is not very commonly used. It is more popular to use plain JSON without a schema

## Protocols for Web services

For APIs that work over the internet (Web services), we have protocols that define the API working. Two of the most popular Web service protocols are:

1. SOAP
2. REST

Since Web services work over HTTP, *SOAP and REST are just **rules to form HTTP requests and responses***

### REST vs SOAP

REST is more popular and also growing in popularity as compared to SOAP. You may never use SOAP if you start dealing with APIs now

### SOAP

SOAP stands for **Simple Object Access Notation**. Object access refers to accessing an API. Therefore, SOAP is a notation by which we access an API (i.e rules)

SOAP uses a language called **WSDL** (Web Services Description Language) for its purposes (pronounced "wizdel")

#### WSDL structure & example

```xml
<definitions>
<types>
  data type definitions........
</types>
<message>
  definition of the data being communicated....
</message>
<portType>
  set of operations......
</portType>
<binding>
  protocol and data format specification....
</binding>
</definitions>
```

Example:
```xml
<message name="getTermRequest">
  <part name="term" type="xs:string"/>
</message>

<message name="getTermResponse">
  <part name="value" type="xs:string"/>
</message>

<portType name="glossaryTerms">
  <operation name="getTerm">
    <input message="getTermRequest"/>
    <output message="getTermResponse"/>
  </operation>
</portType>
```

In this example the `<portType>` element defines "glossaryTerms" as the name of a port, and "getTerm" as the name of an operation. The "getTerm" operation has an input message called "getTermRequest" and an output message called "getTermResponse". The `<message>` elements define the parts of each message and the associated data types

#### The HTTP request/response rules in SOAP:

1. **Start line**: `POST <WSDL> <HTTPVersion>`
	- There are no method names in SOAP so it just uses `POST` as a placeholder for it (even though it does not create content on the API server). Hence, **every SOAP request uses POST**
	- The WSDL location is used in place of the path & query params
2. **Headers**: The `content-type` must be set to **`text/xml`**. No constraint on the other headers
3. **Blank line**
4. **Body**: Contains XML data (i.e an XML envelope formed using WSDL)

The SOAP rules were defined by the **W3C** (Same organization that created XML, HTML, & WSDL)

An example SOAP HTTP Body (XML envelope created using the WSDL):
```xml
<?xml version="1.0"?>

<soap:Envelope
xmlns:soap="http://www.w3.org/2003/05/soap-envelope/"
soap:encodingStyle="http://www.w3.org/2003/05/soap-encoding">

<soap:Header>
...
</soap:Header>

<soap:Body>
...
  <soap:Fault>
  ...
  </soap:Fault>
</soap:Body>

</soap:Envelope>
```

**Note:** 

1. `ns` in `xmlns` refers to a namespace. We pass the URL of the WSDL as value to a namespace
2. `<soap:Envelope>` attributes basically registers the WSDL which acts as the envelope that is used to define the XML body

Example SOAP HTTP request image:

![SOAP HTTP request example image](http://www.bizcoder.com/Media/Bizcoder/Windows-Live-Writer/REST-Paint-On-A-SOAP-Stack_A717/image_thumb_1.png)

An example SOAP HTTP request:
```
POST /webservicesserver/NumberConversion.wso HTTP/1.1
Host: www.dataaccess.com
Content-Type: text/xml
cache-control: no-cache
Postman-Token: 48c93c51-62e3-42e8-ba66-6e299d5b7b89

<?xml version="1.0" encoding="utf-8"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <NumberToWords xmlns="http://www.dataaccess.com/webservicesserver/">
      <ubiNum>500</ubiNum>
    </NumberToWords>
  </soap:Body>
</soap:Envelope>
```

### REST

REST stands for **REpresentational State Transfer**. What it means is that the data communicated back is a ***representation*** *(a copy)* of the data that was queried. For example, the request might have been to fetch user data from a database or a file. The response is a model of the actual data in the format that the API wants to provide it to you

REST was created by **Roy Fielding**

#### SOAP vs REST

1. Methods:
	- All SOAP request have the POST method name (since method names are not used)
	- REST relies on method names. Hence, it uses them. For example, to request an HTML page, we will use the GET method since we want to read the HTML data that exists on server. User creation on the other hand, will use a POST method since we are creating a user on the server. There are 4 main methods: GET, POST, PUT, DELETE
2. `Content-type`:
	- In SOAP, the request and response must be of an XML format (based on the WSDL)
	- In REST, the content type can be anything you define it to be.
3. State:
	- SOAP is stateful. That is, it depends on the state of the server. If server is down, it cannot call a procedure on the server and if the state changes during a procedure call, the request can be affected by it
	- REST is **stateless** (just like HTTP). If the server is down, it just waits for it to come back up and serve a resource (as opposed to calling a procedure). So, whenever the server is ready to server state (i.e a resource) it serves it. Therefore, the control is with the server and not the client (i.e we request from a server without telling it how to get the data unlike in SOAP). Therefore, ***time** of the request does not affect the response* (and it does not associate new requests with old and so on, every request is independent)
4. Rules
	- SOAP has rules on content type, its creation, the procedures that can be called, and so on
	- REST does not have have any rules like SOAP does (like using wsdl, following a syntax) and it is flexible. 

Therefore, ***REST is today the modern and most preferred way to do APIs***

#### Methods:

1. GET: Used to **read** (query) data
2. POST: Used to **create** data
3. PUT: Used to **update** (change) data
4. DELETE: Used to **remove** data

Therefore, we can see that POST-GET-PUT-DELETE maps directly to the **CRUD** operations on a database. Hence, these are the most important methods and most commonly used as well

There are other methods too such as TRACE, HEAD, OPTIONS, etc.

**NOTE:**  
- The **GET** method also helps us **CACHE** resources based on the headers such as `cache-control`/`expiry`. 
- Since JSON is simpler and better than XML, a lot of the REST APIs request and response data are in JSON

#### Structure of HTTP request using REST

1. **Start line**: `<method> <location&params> <httpversion>`
	- Example: `GET /search?q=tuna HTTP/1.1`
2. **Header lines**: Any of the regular headers can be used (No constraint on `content-type` header. It need not be `text/xml`)
3. **Blank line**
4. **Body**: Any content type (JSON, images, HTML, XML, etc)

#### Example of an HTTP request with REST protocol

![Image of a REST based HTTP request](https://static1.smartbear.co/soapui/media/images/stories/rest/rest_query_parameter_with_post_in_raw_os_1.png)

#### History of API protocols

- 1980s - Sun RPC (Remote Procedure Calls)
- 1989 - XML RPC
- 1999 - SOAP
- 2000 - REST

## Types of apps

There are 3 main types of apps:

1. Native apps
2. Web based
3. Hybrid apps

The operating system itself provides APIs to interact with the hardware. For example, camera, location, sound, etc are all used via APIs. These are low-level APIs. We can also have high-level APIs that use low-level APIs. These include things like the browser, email client, etc (which can be apps too).

Native apps need to use the OS APIs. Therefore, a native app has to be build for a particular OS. For example, an android app for android os, windows app for windows os, and so on

Web based apps work inside the browser. For example, your websites. So, the apps work inside another API like the browser so it does not have full access to the OS except via the browser. It has HTML5 however, and that provides a lot of capabilities that allow OS API access. We make most of our work done via requests to APIs on servers running on other computers over the internet. Therefore, these web apps are platform independent (OS it is used on does not matter)

Hybrid apps have both native features. Web apps can be embedded inside the native app. We can use middlewares between OS and web app to interact with the system

### Comparison

- Quality, performance, & speed: Native apps are best
- Cost: Web apps are the easiest to create
- Accessibility: Web apps are very accessible (apps require download)

## Security, authentication & authorization

### HTTPS

When you transfer data over the internet with HTTP, you are transferring plain data that anyone can intercept and see. Hackers can take advantage and exploit vulnerabilities. Therefore, HTTP is not secure

HTTPS was introduced later and it **encrypts** your transmitted data (i.e requests and responses). It is more secure in the sense that if someone intercepts the network and gets the HTTP payload, it is not easy to decrypt and decipher the contents of it

HTTP urls start with `http://` prefix while HTTPS urls start with `https://`

### Authentication vs Authorization

Authentication and authorization do not mean the same thing. 

- **Authentication**: *Proving your identity*. For example, logging in to a site which proves that I am so-and-so
- **Authorization**: *Limited access*. For example, depending on the authorization level, you can allow someone to see free videos, or premium ones, if you are a video hosting website

Authorization has nothing to do with proving your identity.

#### Examples of authentication and authorization

| Name | Authentication | Authorization | Examples |
|------|----------------|---------------|----------|
| No Auth | N | N | Google search page |
| Basic Auth | Y | N | Gmail |
| Bearer Token | N | Y | Not many examples |
| OAuth | Y | Y | Many (ex: Waze, Google OAuth) |
| Two-factor Auth | Y | N | High security |

- **No Auth**: Does not require any security. Anyone can access everything
- **Basic Auth**: API needs to know who you are. Ex: Once you login to an email account, you can access all features. There is no auth required
- **Bearer Token**: Don't need to know who is using the API but we need to provide access based on a token that authorizes the request. This approach is ***not very secure*** and hence, not used much
- **OAuth**: We give another entity (like an app) access to resources. You need to know who is using the entity (app) and depending on that there is authorization on what resource can be accessed. For example, a Google Maps app user authenticated and allowed to use only the location API on the phone but not the others. ***Very commonly used***
- **Two-factor Auth**: We authenticate ourselves first normally (using primary credentials like a username and a password) but then we have to also put in a token (a second credential) that we might physically have or accessible elsewhere (like another app or sms) to be able to actually use the app and APIs. Therefore, ***we authenticate twice*** in this approach. There is no authorization needed. It is used for **high security** applications where there is no access restriction once you login but we need to be sure who's using the app (Ex: Bank apps)

Depending on the need, there is authorization or authentication or both!

### OAuth2.0

> Built from multiple tutorials including the main inspiration for this article (Topmost link)
> - [Original Link to RFC 6749](https://tools.ietf.org/html/rfc6749)
> - [Okta article](https://developer.okta.com/blog/2019/10/21/illustrated-guide-to-oauth-and-oidc)
> - [OAuth2 Simplified](https://aaronparecki.com/oauth-2-simplified/#user-experience-and-alternative-authorization-flows)

***OAuth2.0 is a way to give access (authorization) to a 3rd party entity (i.e application) to access your resources on another entity.*** It was started in 2006

As an example, imagine you have a Google Photos account and want share access to a folder in it for a photo editing app (or to another friend). Your photo editing app then needs to send you to google photos which will make you login (if not done so already), take your consent to provide access to the other app, and redirect back to the photo editor app with a special token. This token is then used by the photo editor app to work on photos from the folder you've allowed it to access

**Note**: 
- ***OAuth1.0 is obsolete*** and no one uses it. However, it's not bad. In fact, it's good! But, it's too complicated. You do not have to learn it and OAuth2.0 is not built on top of it either
- RFC stands for *Request For Comments* that was put out by **IETF** (Internet Engineering Task Force) for many standards

#### Why OAuth2.0?

OAuth stands for ***Open Authentication***. It allows sharing of your credentials on one app with another app and expecting privacy and security to be guaranteed is a big risk. The need is to provide authorization to resources and not the user credentials

Note that there is authentication as well as authorization in OAuth:

1. Authentication of the user (resource owner) on authorization server
2. Authorization of the client app on the authorization server

#### Working of OAuth2.0

1. OAuth allows 3rd party access
2. Limited access (authorization)
3. It provides access to a ***Web service*** (An API over the internet)
4. Therefore, OAuth requires that ***HTTP protocol*** be used
5. Instead of giving away credentials (insecure), OAuth introduces an ***authorization layer***

#### Roles in OAuth2.0

1. ***Resource owner***: You (the user) are the owner of your identity on the accounts 
2. ***Client***: The 3rd party app that wants to access your other account on your behalf
3. ***Authorization server***: It is the application that knows the resource owner, can authenticate him/her, and provide access keys
4. ***Resource server***: It is the application that contains the resources (of the resource owner) that the client is trying to access

The authorization and resource servers can be on the same computer, under the same organization, or they can be separate in both ways. OAuth does not put any ***constraint*** on it

#### Basic OAuth2.0 flow (high level)

1. User (Resource owner) is using a 3rd party app (Client)
2. User clicks on trigger to start OAuth (say, a button)
3. User is redirected to Authorization server page where he is asked to login if not already done
4. User's consent is asked on the Authorization server page for the resources he is willing to share with the Client (He can deny it too!)
5. On getting consent, the Authorization server redirects back to the Client page and a lot of further API calls between Authorization server, Resource server and Client happen that the user is unaware of (i.e does not require his/her interaction) until finally the resource is received by the client

#### Terminologies

1. **Redirect URI**: Page to redirect to once auth server grants client
2. **Grant (Response) type**: The type of grant provided
	- *Authorization code*: Most common type of grant type (popular)
	- Other types: *Client credentials*, *Implicit*, *Resource owner*
3. **Scope**: Includes the list of resources that can be granted access to
4. **Consent**: Whether user wants to allow access to requested resources
5. **Client ID**: The client must have registered a unique name with authorization server much before any of the OAuth flow can take place
6. **Client secret**: Along with ID, even a secret key is made available to client to be used during communication with the auth server
7. **Authorization code**: A short lived, temporary code that, along with client secret, is used to fetch the access token from authorization server
8. **Access token**: The token used to get the protected resource from the Resource server
9. **Refresh token**: The access token might expire. So, you can use an optional refresh token to hit the Authorization server to fetch a new access token instead of going through the whole OAuth flow from the beginning (The refresh token has a longer expiry than access token)

#### Detailed OAuth2 workflow using Authorization Code grant type

| Step | Client                                                                                                              | Authorization Server                                                         | Resource Server                                            | User Action                                 |
|------|---------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|------------------------------------------------------------|---------------------------------------------|
| 1    | Registers itself with Auth server using Client ID                                                                    | Provides a Client secret to the client                                  |                                                            |                                             |
| 2    | Redirects to Auth server with \(a\) Client ID \(b\) Redirect URI \(c\) Grant type \(d\) scope                       |                                                                              |                                                            | Clicks on button that enables authorization |
| 3    |                                                                                                                     | Authenticates user \(Is session active?\)                                    |                                                            | Must login to Auth server if needed         |
| 4    |                                                                                                                     | Takes consent of user for access to resources in scope                       |                                                            | Must click to allow access \(or deny\)      |
| 5    |                                                                                                                     | Redirects back to client with grant \(Authorization code\)                   |                                                            |                                             |
| 6    | Makes an API call \(No redirect\) to Auth server using \(a\) Client ID \(b\) Authorization code \(c\) Client secret |                                                                              |                                                            |                                             |
| 7    |                                                                                                                     | Responds to Client with \(a\) access token \(b\) Optionally, a refresh token |                                                            |                                             |
| 8    | Makes an API call to Resource server with access token                                                              |                                                                              |                                                            |                                             |
| 9    |                                                                                                                     |                                                                              | Responds to Client with data it has requested & can access |                                             |
| 10   | Makes use of the received data                                                                                      |                                                                              |                                                            |                                             |


#### Client registration with Authorization server 

This is a step that happens *way before* the user can interact with an app and provide access to his/her accounts using OAuth. 

The client app, when it recognizes the importance of accessing the resources of its user on another service, must reach out to the service and register itself

1. **From Client to Auth server**
	1. Client type (If native app, sends "public" since it can't store secrets on OS as a file where anyone can access it, else "confidential" for a web app since the browser will store it securely & not the app)
	2. Redirect URL
	3. Client secret (only if client type is "confidential")
	4. Additional information (optional)
2. **From Auth server back to Client**
	1. Client ID
	2. Client secret (if client type is "confidential")

#### Contents of the authorization request (Client to Auth server)

Refer to **step (2)** in the above table:

1. Client ID 
2. Redirect URI
3. Response Type (ex: Authorization code)
4. Scope
5. Client secret

#### Contents of the access token request (Client to Auth server)

Refer to **step (6)** in the above table:

1. Grant type (ex: Authorization code)
2. The grant (ex: Authorization Code)
3. Client ID
4. Client secret

#### Contents of the access token response (Auth server to Client)

Refer to **step (7)** in the above table:

1. Access token
2. Token type
3. Expires in (seconds) (*Note*: we can have a token that never expires)
4. Refresh token (optional)
5. Scope (optional)

### OpenID Connect

**OIDC** is a *thin layer* that sits on top of OAuth2.0. It is a standard that allows us to fetch profile & information of the user on an app and use it in another

As an example, whenever we login using facebook or google on other apps such as other websites, we are doing it via OAuth... but, not just OAuth. Instead, with OAuth + OpenID since it provides us with the user's facebook profile information (not all but some basic ones for the purpose of security)

OpenID is ***not*** part of the OAuth2.0 standard (`RCF 6749`). It is defined by a different organisation called the *OpenID Foundation*

#### OAuth vs OpenID

OAuth grants accesses to resources belonging to the user on a server but OpenID provides user information like profile, DOB, email, last login, likes/dislikes, phone number, and so on

#### OpenID advantages

It has become a popular way of realizing **Single Sign On (SSO)** mechanism where the user chooses one platform to login to and use that info to login to other applications. This makes it less tedious to the user by removing multiple username/password login steps and he also need not maintain so many usernames and passwords. No new login on 3rd party app required. Basically, *leverage a login a user already has and is comfortable using* 

A drawback can be that it exposes some of your data on one app to other 3rd party apps. It is usually used by the client app for *marketing* purposes and can be a slight security concern

#### How OpenID works

The flow is the regular to OAuth2.0 flow with a few differences:

1. In addition to getting an access token from Auth server, the Client also gets an **ID token** (Step **(7)** in table above)
2. In addition to sending an access token to Resource server, the Client also sends an **ID token** (Step **(8)** in table above)

An openID endpoint (URL) makes it possible for the Client to access user details using the ID token. 

The ID token is in the form of a **JSON Web Tokens (JWT)** which is a string encryption (Base64 format) and has 3 parts (`.` separated):

1. Header
2. Resource owner information
3. Signature

When decoded, it contains a JSON (hence, the name) format data (key, value pairs). The keys refer to one property of the RO (user)

Example JWT: 

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

Example decoded JWT parts: 

```
HEADER:
{
  "alg": "HS256",
  "typ": "JWT"
}

RO (USER) INFORMATION:
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022
}

SIGNATURE (GENERATION LOGIC):
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  <your 256 bit secret>
)
```

## Types of authentication & Postman Introduction

Postman is a tool that helps you make API requests mainly for the purposes of testing, prototyping, designing APIs, and so on

Developed by an Indian developer and later built a company around it

What is Postman? It is a ***browser***

Difference between Postman & other browsers:

1. You can make direct API calls without needing a (GUI) interface
2. We can look underneath the hood when making API calls (i.e see details)

*Note:* [https://www.programmableweb.com/](https://www.programmableweb.com/) provides a searchable list of public APIs under the "API DIRECTORY" link. We can search based on name, API type, Authentication type, and so on. This is a good resource to try out different APIs (via Postman) and analyse them

To be able to try out different postman features, check out this [documentation](https://docs.postman-echo.com/?version=latest)

### 1. Basic authentication

It is a simple (basic) way of proving your identity (authentication). The method is uses is that of a *username* and a *password*. It has been in existence since 1999. It is **NOT VERY SECURE!**

Anybody can see the username/password (or its base64 form and decode it) easily. So, it is better to pass it via ***HTTPS (encrypted)*** and not HTTP

1. To make an API call with basic authentication, select the request's ***Authorization*** tab, click the dropdown and select *Basic Auth*. It will require us to fill in a *username* & *password*
2. Postman creates a (hidden) header in the request called `Authorization` with a value like so: `Basic <Base64Key>`. Basic means basic authentication and the key is generated from the username and password (Ex: `Basic cG9zdG1hbjpwYXNzd29yZA==`)

Try out a demo in postman: Make a GET request to `https://postman-echo.com/basic-auth` and select 'Basic Auth' and type `postman` / `password` in username / password field 

(*Note:* if there is no authorization, you can select "No Auth" from the same dropdown)

### 2. Digest authentication

Instead of sending a username and a password as an encoded base64 string that can be easily decoded by anyone on an HTTP network which forces us to use HTTPS networks for basic authentication, we can use a slightly different approach

A ***digest*** means something that helps us *convert a value into a consumable*. In the real world, your stomach acid is a digest since it converts food taken into the stomach into nutrients that enrich our bodies. In computers, a password for a zip file is a digest is something that helps us unzip and access the actual files

A digest is different from a password in the following ways:

1. A secret is shared between the client and server only
2. When client makes a request, it generates a hash (MD5/SHA) of the data using the secret key which becomes the digest
3. When server receives the data, it decodes the digest using the secret key 
4. Even if someone else intercepts the network, they cannot decipher the digest without the secret key. Hence, digest can be used over an HTTP network

Digest approach is not very popular, not even as popular as basic authentication!

What is **Nonce**? When creating a digest, we need to use a unique number. The digest plus the unique number will help the server. If the same nonce is used again in the authentication request, the server will not act on it

The `Authorization` header in the request will be of the form `Digest <value>` 

For example:
```
Digest username="postman", realm="Users", nonce="ni1LiL0O37PRRhofWdCLmwFsnEtH1lew", uri="/digest-auth", response="254679099562cf07df9b6f5d8d15db44", opaque=""
```

Try `GET` request with `https://postman-echo.com/digest-auth` on Postman. Select "Digest Auth" and you will have to enter your username & password (`postman/password`) as before but postman takes care of generating a digest for you using those values (& other defaults, if you don't customize them)

### 3. Bearer token

Bearer token means that anyone who holds a token (i.e bears it) can get successfully authenticated. It is part of `RFC 6750` (and is the same kind of token used as access token in Oauth2.0)

It is used but there are some security concerns:

1. Since anyone having the bearer token can be authenticated, it is important for the client and server to guard this token (i.e the onus is on the apps to provide security)
2. Because of (1), it does not matter what the username/password is and who is using the app as long as he bears a token. It's similar to having a tattoo that proves that you are part of a criminal gang! No name, address, etc required...
3. Since anyone who gets the token can be authenticated, it is important to ***use HTTPS*** so that even if request is intercepted, no one can get its actual value

Then why is access token similar to a bearer token? With access tokens, a lot more mandatory steps take place before providing the token, the scope is defined, and so on

The `Authorization` header will be of the format: `Bearer <token>`. For example, `Bearer sa.24.sdfMo` (The token can literally be any string)

### 4. OAuth2.0

As understood in its own section, we do not access an API directly in OAuth2.0. Instead, user interaction is necessary and the auth server needs to be accessed by both user and the client app and then after getting the access token can we hit the Resource server.

## AWS

## Webhooks

## Microservices

