---
layout: page
title: Project 1
---

**<font color='red'>The project webpage is not ready yet. More details will be added later.</font>**

# Overview

In this project, you will learn the basic of HTTP protocol and socket programming through developing a simple Web server and crawler.

All implementations should be written in C++ using [BSD sockets](http://en.wikipedia.org/wiki/Berkeley_sockets).
No high-level network-layer abstractions (like Boost.Asio or similar) are allowed in this project.
You are allowed to use some high-level abstractions for parts that are not directly related to networking, such as string parsing, multi-threading.
We will also accept implementations written in C, however use of C++ is preferred.



# Task Description

The project contains two parts: a Web server and a Web crawler.
The Web server accepts an HTTP request, parses the request, looks up the requested file in local file system.
If the file exists, the server creates an HTTP response with the requested file as the content,
otherwise the server creates an HTTP response with the corresponding error code.
The response will be sent back to the requester through the same channel where the request is received from.

The Web crawler, after retrieving the response from the Web server, save the retrieved file in local file system.
Moreover, the crawler will also inspect the retrieved file to see if there is any other file to crawl (you can assume all the responses will be [HTML](http://www.w3schools.com/html/default.asp) files).
If there is a file that the crawler has not fetched before, the crawler will continue the fetching until there is no new file to fetch. 

The basic part of this project only requires you to implement HTTP 1.0.
That is, the crawler and server talk to each other through **non-persistent** connections (however, you can get bonus points if your crawler and server can support HTTP 1.1, see details in [Grading](#http1.1)).

You may want to approach the project in three steps: **HTTP message abstraction**, **Web crawler**, **Web server**.

## HTTP message abstraction

As the first step, you need to implement several helper classes that can help you to construct/parse an HTTP message (request/response).
For example, you may want to implement an `HTTPRequest` class that can help you to customize the HTTP request header and encode the HTTP request into a string of bytes of the wire format. Some high level pseudo code would look like:

    HTTPRequest request;
    request.setUrl(...);
    request.setMethod(...);
    vector<uint8_t> wire = request.endcode();

Note that, you only needs to implement HTTP `GET` request in this project.

For parsing, you may also want to implement an `HTTPRequest` constructor method that creates an `HTTPRequest` object from the wire encoded request.

    HTTPRequest request(wire);

Similarly, you may also want to implement an `HTTPResponse` class that can facilitate processing HTTP responses.

## Web server

After finishing HTTP abstraction, you can start building your Web server.
The eventual output is a binary `web-server`, which accepts three arguments: the hostname of the Web site, the port number, and the local file directory.

    $ web-server [hostname] [port] [file-dir]

For example, the command below will starts the Web server with host name `localhost` listening port `1234` and serving files under the directory `/tmp`.

    $ web-server localhost 1234 /tmp

The default arguments for `web-server` is `localhost`, `4000`, and `.` (current working directory).

The Web server needs to convert the hostname to an IP address and opens a listening socket on the IP address and the specified port number.
Through the listening socket, the Web server accepts the connection requests from clients and establishes connections to the clients.
Through the established connection, the Web server should receive the HTTP request sent by the client, and return the requested file in terms of HTTP response.
The Web server **must** handle concurrent connections.
In other words, the web server can talk to multiple clients at the same time.


After implementing the Web server, you can test it by visting it through some widely used Web clients (e.g., Firefox, wget) on your local system. 

## Web crawler

After finishing the Web server, you can start building your Web crawler.
The eventual output is also a binary `web-crawler`, which accepts single argument: the initial URL.

    $ web-crawler [URL]

For example, the command below will start the Web crawler that crawls all the files linked on the an webpage `http://localhost:4000/index.html`.

    $ web-crawler http://localhost:4000/index.html

The Web crawler first tries to connect to the Web server as specified in the URL.
Once the connection is established, the crawler can construct the HTTP request and send it to the Web server, expecting the HTML file in response.
After receiving the response, the crawler needs to parse it and find more URLs to retrieve.
All the subsequent retrievals are done in the same way as the initial one.

You can also test your implementation by crawling some real but simple Website or the Web server you just implemented. 

# Environment Setup

You will do this project on a virtual machine powered by VirtualBox.

Here are the instructions to set up your environment.

1. Creating your virtual machine using [vagrant](https://www.vagrantup.com/).

2. (NOT finished yet...)

# Submission


To submit your project, you need to prepare:

1. all your source code and Makefile (no binaries);
2. a PDF project report that describes
   * the high level design of your server and crawler;
   * the problems your ran into and how you solved the problems;
   * additional instructions to build your project (if your project uses some other libraries);
   * how you tested your code and why.
   * the contribution of each team member (up to 3 members in one team) and their UID

Put all these above into a package and submit to CCLE.
   
Please make sure:

1. your code can compile
2. no unnecessary files in the package.

Otherwise, your will not get any credit.

   
# Grading

Your code will be first checked by a software plagiarism detecting tool. If we find any plagiarism, you will not get any credit.

Your code will then be automatically tested in some testing scenarios. If your code can pass all the test cases, you will get full credit.


##  Bonus points

You can have:

* 5% extra points: if your crawler can handle HTTP request timeout;
* 10% extra points: if you can implement concurrent Web server using both synchronous and asynchronous programming techniques;
* <a name="http1.1"></a>10% extra points: if both of crawler and server can support HTTP 1.1 instead of HTTP 1.0. More specifically, the crawler should be able to talk to the server over a persistent connection, and use pipeline if necessary.



