# API & Web Service Introduction

 [Course link](https://www.udemy.com/course/api-and-web-service-introduction)

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

## API Details

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

You can have **as many lines (Headers)** as you want in the headers section.

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
