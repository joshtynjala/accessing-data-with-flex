# Deploying applications that access data services

There are many factors to consider when moving your application from a
development environment to a deployment environment. The process of deploying an
application is dependent on your application, your application requirements, and
your deployment environment.

For example, the process of deploying an application on an internal website that
is only accessible by company employees is different from the process for
deploying the same application on a public website.

[Deploying applications](https://web.archive.org/web/20150924172518mp_/http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf69084-7ec8.html)
provides an overview of things to consider and includes a
[Deployment checklist](https://web.archive.org/web/20150924172518mp_/http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf69084-7f1a.html).
The checklist discusses some common system configuration issues that customers
have found when deploying applications for production. The documentation also
contains troubleshooting tips to diagnose common deployment problems.

## Best practices for coding access to services

Using Flash Builder tools, you can generate client code to access data in a
database. This feature is available for both PHP and ColdFusion. However, this
code is for prototyping only. Do not use this code as a template for writing
secure applications.

By default, this code allows anyone with network access to your server to
insert, select, update, or delete from the database table. Here are some best
practices to consider when modifying the generated code or writing any code that
accesses services. See
[Securing Data Services](https://web.archive.org/web/20150924172518mp_/http://www.adobe.com/go/flex_security)
for additional information.

### Remove functions that are not used

Delete or comment out any functions that you do not plan to use within your
application.

### Add authentication

Add user authentication to ensure that only trusted users can access your
database information.

### Add authorization checks

If authentication is necessary, then add authorization checks. Even though you
authenticated users to your application, you want to ensure that they are
authorized to make specific queries.

For example, you can allow everyone to select but restrict which users have the
authority to delete.

Another example is authorizing user A to retrieve their own information using a
select query. But prevent user A from using a select query to access user B's
information.

### Data validation

Be sure to add data validation. For example, validate the data provided to any
insert statement to ensure that bad or malicious data does not get accepted by
the database.

Client side validation is not able to protect you from someone sending manual
requests to your web server. Data validation protects against hackers and
ensures the quality of the information that is stored.

### Restrict the amount of data that is retrieved

Select methods can select everything from a table. In some cases, this practice
leads to too much information going over the network. Only retrieve the data
that you need.

For example, `SELECT *` from a user table can return the user name and password
over the network.

### Consider SSL for sensitive data

Using a secure protocol ensures the privacy of the information you are sending.

### Exporting source files with release version of an application

When you export a release build of an application, Flash Builder provides the
option Enable View Source. This option allows users to view the source files
that implement the application. For server projects, the source files include
the `services` folder, which contains the files providing access to your service
implementation.

Important: Use caution when including service files with the View Source option.
The service files expose details on accessing your database, and possibly
include sensitive information such as user names and password. If services are
included in the View Source option, anyone who has access to the launched
application has access to sensitive data.

## Writing secure services

The examples in Adobe documentation, including the tutorials and applications
created using Flash Builder code generation, are instructive in nature. They
illustrate how to access data services from a client application. However,
because these examples are designed to ensure clarity, they do not illustrate
best practices for secure access to data.

The Flash Builder documentation contains examples, including applications
created from generated code. These examples are to be deployed in a trusted
development environment. A trusted development environment can be a local
machine or location inside a firewall. Without additional security measures,
anyone with network access also has access to your database.

Some best practices when writing services include:

- Authenticate the user before calling any method on a service

- Use service authentication to allow only certain users to perform certain
  actions.

  For example, suppose you have an application that allows employee data to be
  modified through a RemoteObject call. In this case, use RemoteObject
  authentication to make sure that only managers can change the employee data.

- Use programmatic security to limit access to services.

- Apply declarative security constraints to entire services.

- When accessing a web service (`<mx:WebService>`) or HTTP service
  (`<mx:HTTPService>`) one of the following must be true:

  - The service implementation is in the same domain as the application that
    calls it.

  - The host system for the service has a `crossdomain.xml` file that explicitly
    allows access from the application's domain.

## Writing secure applications

Adobe® Flash® Player runs applications built with Flash. Content is delivered as
a series of instructions in binary format to Flash Player over web protocols in
a precisely described SWF file format. The SWF files themselves are typically
hosted on a server and then downloaded to, and displayed on, the client computer
when requested. Most of the content consists of binary ActionScript
instructions. ActionScript is the ECMA standards-based scripting language that
Flash uses. ActionScript features APIs designed to allow the creation and
manipulation of client-side user interface elements and for working with data.

The security model for Flex protects both client and the server. Consider the
following two general aspects to security:

- Authorization and authentication of users accessing a server's resources

- Flash Player operating in a sandbox on the client

Flex supports working with the web application security of any J2EE application
server. In addition, precompiled applications in Flex can integrate with the
authentication and authorization scheme of any underlying server technology to
prevent users from accessing your applications. The Flex framework also includes
several built-in security mechanisms that let you control access to web
services, HTTP services, and server-based resources such as EJBs.

Flash Player runs inside a security sandbox that prevents hijacking of the
client by malicious application code.

Note: SWF content running in Adobe AIR follows different security rules than
content running in the browser. For details, see the Air Security topic in the
AIR documentation.

For links to various security topics, see the
[Security Topic Center](https://web.archive.org/web/20150924172518mp_/http://www.adobe.com/devnet/security/)
at the Adobe Developer Connection.

More Help topics

[Flex Security](https://web.archive.org/web/20150924172518mp_/http://www.adobe.com/go/flex_security/)

![](../img/flashbuilderLinkIndicator.png) 
[Export a release version of an application](https://web.archive.org/web/20150924172518mp_/http://help.adobe.com/en_US/flashbuilder/using/WSe4e4b720da9dedb5-13a250c812e8e9b5533-7ff9.html)

[Securing Data Services](https://web.archive.org/web/20150924172518mp_/http://www.adobe.com/go/flex_security)

![](../img/flexLinkIndicator.png) 
[Introduction to security](https://web.archive.org/web/20150924172518mp_/http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf6167e-7fff.html)

![](../img/flexLinkIndicator.png) 
[Using RPC services](https://web.archive.org/web/20150924172518mp_/http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf6167e-7fe1.html)

![](../img/flexLinkIndicator.png) 
[Writing secure applications](https://web.archive.org/web/20150924172518mp_/http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf6167e-7fd9.html)

![](../img/flexLinkIndicator.png) 
[Accessing data services from a deployed application](https://web.archive.org/web/20150924172518mp_/http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf69084-7bbe.html)

![](../img/flexLinkIndicator.png) 
[Using cross-domain policy files](https://web.archive.org/web/20150924172518mp_/http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf69084-7f2b.html)

![](../img/flexLinkIndicator.png) 
[Other resources](https://web.archive.org/web/20150924172518mp_/http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf6167e-7fc8.html)
