---
layout: post 
title: "#1 - Welcome; URL Shortener"
categories: misc
---

## Goals

- Everybody is well acquainted
- The course is introduced and understood
- Requirements and Instructions to set up the machine are clear
- The semester project is introduced

## What is Software engineering?

Software engineering is an engineering approach on a software development of systematics application.

A software engineer is a person who applies the principles of software engineering to design, develop, maintain, test,
and evaluate computer software. The term programmer is sometimes used as a synonym, but may also lack connotations of
engineering education or skills.

## Basic Outline of software engineering

1. Software requirements
2. Software design
3. Software construction
4. Software testing
5. Software maintenance

Systems design is the process of defining the architecture, product design, modules, interfaces, and data for a system
to satisfy specified requirements. Systems design could be seen as the application of systems theory to product
development.

## URL Shortener - Requirements

### What is a web/application server?

A web server is computer software and underlying hardware that accepts requests via HTTP (the network protocol created
to distribute web content) or its secure variant HTTPS. A user agent, commonly a web browser or web crawler, initiates
communication by making a request for a web page or other resource using HTTP, and the server responds with the content
of that resource or an error message. A web server can also accept and store resources sent from the user agent if
configured to do so.

The hardware used to run a web server can vary according to the volume of requests that it needs to handle. At the low
end of the range are embedded systems, such as a router that runs a small web server as its configuration interface. A
high-traffic Internet website might handle requests with hundreds of servers that run on racks of high-speed computers.

### What is a URL?

A Uniform Resource Locator (URL), colloquially termed a web address, is a reference to a web resource that specifies its
location on a computer network and a mechanism for retrieving it. A URL is a specific type of Uniform Resource
Identifier (URI), although many people use the two terms interchangeably. URLs occur most commonly to reference web
pages (http) but are also used for file transfer (ftp), email (mailto), database access (JDBC), and many other
applications.

Most web browsers display the URL of a web page above the page in an address bar. A typical URL could have the
form http://www.example.com/index.html, which indicates a protocol (http), a hostname (www.example.com), and a file
name (index.html).

![URL fragments](/advanced-java/assets/URI_syntax_diagram.svg.png)

[Read more about the syntax](https://en.wikipedia.org/wiki/URL#Syntax)

```
          userinfo       host      port
          ┌──┴───┐ ┌──────┴──────┐ ┌┴┐
  https://john.doe@www.example.com:123/forum/questions/?tag=networking&order=newest#top
  └─┬─┘   └───────────┬──────────────┘└───────┬───────┘ └───────────┬─────────────┘ └┬┘
  scheme          authority                  path                 query           fragment

  ldap://[2001:db8::7]/c=GB?objectClass?one
  └┬─┘   └─────┬─────┘└─┬─┘ └──────┬──────┘
  scheme   authority   path      query

  mailto:John.Doe@example.com
  └─┬──┘ └────┬─────────────┘
  scheme     path

  news:comp.infosystems.www.servers.unix
  └┬─┘ └─────────────┬─────────────────┘
  scheme            path

  tel:+1-816-555-1212
  └┬┘ └──────┬──────┘
  scheme    path

  telnet://192.0.2.16:80/
  └─┬──┘   └─────┬─────┘│
  scheme     authority  path

  urn:oasis:names:specification:docbook:dtd:xml:4.1.2
  └┬┘ └──────────────────────┬──────────────────────┘
  scheme                    path
```

### What is a URL Shortener service?

There are several reasons to use URL shortening. Often regular unshortened links may be aesthetically unpleasing. Many
web developers pass descriptive attributes in the URL to represent data hierarchies, command structures, transaction
paths or session information. This can result in URLs that are hundreds of characters long and that contain complex
character patterns. Such URLs are difficult to memorize, type out or distribute. As a result, long URLs must be copied
and pasted for reliability. Thus, short URLs may be more convenient for websites or hard copy publications (e.g. a
printed magazine or a book), the latter often requiring that very long strings be broken into multiple lines (as is the
case with some e-mail software or internet forums) or truncated.

On Twitter and some instant messaging services, there is a limit to the number of characters a message can carry –
however, Twitter now shortens links automatically using its own URL shortening service, t.co, so there is no need to use
a separate URL shortening service just to shorten URLs in a tweet. On other such services, using a URL shortener can
allow linking to web pages which would otherwise violate this constraint. Some shortening services, such as goo.gl,
tinyurl.com, and bit.ly can generate URLs that are human-readable, although the resulting strings are longer than those
generated by a length-optimized service. Finally, URL shortening sites provide detailed information on the clicks a link
receives, which can be simpler than setting up an equally powerful server-side analytics engine, and unlike the latter,
does not require any access to the server.

URLs encoded in two dimensional barcodes such as QR code are often shortened by a URL shortener in order to reduce the
printed area of the code, or allow printing at lower density in order to improve scanning reliability.

### How does it work?

URL redirection, also called URL forwarding, is a World Wide Web technique for making a web page available under more
than one URL address. When a web browser attempts to open a URL that has been redirected, a page with a different URL is
opened. Similarly, domain redirection or domain forwarding is when all pages in a URL domain are redirected to a
different domain, as when wikipedia.com and wikipedia.net are automatically redirected to wikipedia.org.

URL redirection is done for various reasons:

- for URL shortening;
- to prevent broken links when web pages are moved;
- to allow multiple domain names belonging to the same owner to refer to a single web site;
- to guide navigation into and out of a website;
- for privacy protection; and
- for hostile purposes such as phishing attacks or malware distribution.

### What next? Let's gather requirements!

We want to build an HTTP web application that offers URL shortening service to its users, what are the features and
requirements that need to be satisfied by this application?

[Read more](https://enkonix.com/blog/functional-requirements-vs-non-functional/)

We go into break out session and discuss what are the requirements for our URL Shortener service. At the end of the
class, we should have this documented, and we will focus on building a system that satisfies those requirements over the
course fo this semester

The requirements aggregated from the breakout rooms;

- A user can request to shorten a long url
- A user can request to access the long url of a shortened url
- An admin who manages the application
- A shortened URL can expire after a delay or can live forever
- A dashboard with statistics of the URLs, who is accessing it, how many times, and from where, etc.
- A shortneed url can have description perhaps to facilitate search
- We can limit the kind of characters that are used in the shortened url
- We can limit the number of times a shortened URL is accessed
- We can limit the location from the shortened URL is accessed
- We can use random identifiers for our shortened URLs
- A user can specify the path/slug/identifier they want for their shortened URL e.g. short.url/my_special_slug
- We can validate the long URLs to ensure that they are reachable
- We can maintain a blocklist of domain names that we do not allow in our system for shortening.
- A user can use free text to search for a previously shortened URL
- We can monetize the app, have paid/premium features  

## What do you need for this course?

- [Java](https://www.oracle.com/java/technologies/downloads/)
- [Maven](https://maven.apache.org/install.html)
- [Intellij community edition](https://www.jetbrains.com/help/idea/installation-guide.html)
- [Google Java Format](https://plugins.jetbrains.com/plugin/8527-google-java-format)
- [Complete this assignment](https://classroom.github.com/a/wPm_PcDw)
