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

### Points to note

1. A Web service needs a **format** to transfer data over the N/W (ex: XML, JSON)
2. It also needs a **protocol** to communicate the information over the network (ex: REST, SOAP, XML/RPC)

