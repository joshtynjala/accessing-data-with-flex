# Accessing data services overview

## Data access in Flex compared to other technologies

The way that Flex works with data sources and data is different from
applications that use HTML for their user interface.

### Client-side processing and server-side processing

Unlike a set of HTML templates created using JSPs and servlets, ASP, PHP, or
CFML, Flex separates client code from server code. The application user
interface is compiled into a binary SWF file that is sent to the client.

When the application makes a request to a data service, the SWF file is not
recompiled and no page refresh is required. The remote service returns only
data. Flex binds the returned data to user interface components in the client
application.

For example, in Flex, when a user clicks a Button control in an application,
client-side code calls a web service. The result data from the web service is
returned into the binary SWF file without a page refresh. Thus, the result data
is available to use as dynamic content in the application.

    <?xm l version="1.0" encoding="utf-8"?>
    <s:Application xmlns:fx="http://ns.adobe.com/mxml/2009"
        xmlns:s="library://ns.adobe.com/flex/spark"
        xmlns:mx="library://ns.adobe.com/flex/halo" minWidth="1024" minHeight="768" xmlns:employeesservice="services.employeesservice.*" xmlns:valueObjects="valueObjects.*">

        <fx:Declarations>
            <s:WebService
                id="RestaurantSvc"
            wsdl="http://examples.adobe.com/flex3app/restaurant_ws/RestaurantWS.xml?wsdl" />
            <s:CallResponder id="getRestaurantsResult"
                result="restaurants = getRestaurantsResult.lastResult as Restaurant"/>
        </fx:Declarations>

        <fx:Script>
            <![CDATA[
                import mx.controls.Alert;

                protected function b1_clickHandler(event:MouseEvent):void {
                    getRestaurantsResult.token = RestaurantWS.getRestaurantss();
                }
            ]]>
        </fx:Script>
    . . .
        <s:Button id="b1" label="GetRestaurants" click="button_clickHandler(event)"/>

Compare this Flex example to the following example, which shows JSP code for
calling a web service using a JSP custom tag. When a user requests the JSP, the
web service request is made on the server instead of on the client. The result
is used to generate content in the HTML page. The application server regenerates
the entire HTML page before sending it back to the user's web browser.

    <%@ taglib prefix="web" uri="webservicetag" %>

    <% String str1="BRL";
    String str2="USD";%>

    <!-- Call the web service. -->
    <web:invoke
        url="http://www.itfinity.net:8008/soap/exrates/default.asp"
        namespace="http://www.itfinity.net/soap/exrates/exrates.xsd"
        operation="GetRate"
        resulttype="double"
        result="myresult">
        <web:param name="fromCurr" value="<%=str1%>"/>
        <web:param name="ToCurr" value="<%=str2%>"/>
    </web:invoke>

    <!-- Display the web service result. -->
    <%= pageContext.getAttribute("myresult") %>

### Data source access

Another difference between Flex and other web application technologies is that
you never communicate directly with a data source in Flex. You use a data access
component to connect to a remote service and to interact with the server-side
data source.

The following example shows a ColdFusion page that accesses a data source
directly:

    ...
    <CFQUERY DATASOURCE="Dsn"
        NAME="myQuery">
        SELECT * FROM table
    </CFQUERY>
    ...

To get similar functionality in Flex, use an HTTPService, a web service, or a
RemoteObject component to call a server-side object that returns results from a
data source.

### Events, service calls, and data binding

Flex is an event driven technology. A user action or a program event can trigger
access to a service. For example, a user clicking a button is a user action
event that can be used to trigger a service call. An example of a program event
is when the application completes the creation of a user interface component
such as a DataGrid. The creationComplete event for the DataGrid can be used to
call a remote service to populate the DataGrid.

Service calls in Flex are asynchronous. The client application does not have to
wait for returned data. Asynchronous service calls are useful when retrieving or
updating large sets of data. The client application is not blocked waiting for
the data to be retrieved or updated.

Data returned from a service call is stored in a
[CallResponder](https://help.adobe.com/en_US/FlashPlatform/reference/actionscript/3/mx/rpc/CallResponder.html)
that you associate with the service call. User interface components then use
data binding to retrieve the returned data from the CallResponder.

Data binding in Flex allows you to dynamically update a user interface component
with a data source. For example, a Flex component can associate its text
attribute with the lastResult attribute of a CallResponder. When the data in the
CallResponder changes, the Flex component automatically updates.

Flex also implements two-way data binding. With two-way data binding, when data
changes in either the Flex component or the data source, the corresponding data
source or Flex component automatically updates. Two-way data binding is useful
when updating remote data from user inputs to a Form component or a Flex data
component.

## Using Flash Builder to access data services

In Flex Builder 3, you implement remote procedure calls to data services using
Flex data access components. However, Flash Builder simplifies this process.

Flash Builder provides wizards and other tools that:

- Provide access to data services

- Configure data returned by the data service

- Assist in paging of data returned from the service

- Assist in data management functionality that synchronizes multiple updates to
  server data

- Generates client code for accessing data services

- Bind data returned from the service to user interface components

<!-- -->

### Flash Builder workflow for accessing services

Use the following workflow when using Flash Builder to create an application
that accesses data services.

1.  Depending on your circumstances, you start by connecting to a data service
    or by building the user interface.

    **Connect to remote service.** If you start by connecting to the remote
    service, you then build the user interface.

    **Build user interface.** If you start by building the user interface, you
    then connect to the remote service.

    > Note: Where you start is a matter of personal preference. For example, if
    > you already have a user interface design planned, you can build the user
    > interface first. Conversely, you can connect to the data first and let
    > Flash Builder assist you in generating application components.

2.  Bind data operations to application components.

3.  (Optional) Manage the retrieval and update of data.

    Flash Builder tools allow you to implement the paging of returned data and
    coordinate the update of sets of data.

    When returning large amounts data records, you typically implement paging to
    retrieve a set of records on an "as needed" basis.

    For applications that updates several records, you can implement data
    management features. Data Management features include:

    - Commit functionality to update changed records simultaneously

    - An undo mechanism to revert changes before they are written to the server

    - Code generation that automatically updates user interface components as
      records are added, deleted, or changed

4.  Run the application and monitor the data flow.

    When the application is complete, run the application to view it in
    operation. Use the Flash Builder Network Monitor to view data passed between
    the application and the service. The Network Monitor is useful for
    diagnosing errors and analyzing performance.

    Flash Builder also provides robust debugging and profiling environments. The
    Network Monitor and Flash Profiler are available with Flash Builder Premium.

### Extending services supported by Flash Builder

Flash Builder wizards and tools support access to the following type of service
implementations:

- PHP services

- ColdFusion services

- BlazeDS

- LiveCycle Data Services

- HTTP (REST-style) services

- Web services (SOAP)

- Static XML files

If you need tooling support for additional types of services, such as Ruby on
Rails, you can extend the Flash Builder implementation. See <a
href="https://web.archive.org/web/20150921172759mp_/http://www.adobe.com/go/learn_flex4_extensibility/">Flash
Builder Extensibility Reference</a>.

## Data access components

Data access components let a client application call operations and services
across a network. Data access components use remote procedure calls to interact
with server environments. The three data access components are the RemoteObject,
HTTPService, and WebService components.

Data access components are designed for client applications in which a call and
response model is a good choice for accessing external data. These components
let the client make asynchronous requests to remote services that process the
requests, and then return data to your application.

A data access component calls a remote service. It then stores response data
from the service in an ActionScript object or any other format the service
returns. Use data access components in the client application to work with three
types of services:

- remote object services (RemoteObject)

- SOAP-based web services (WebService)

- HTTP services, including REST-based web services (HTTPService)

Adobe® Flash® Builder™ provides wizards and tools to wrap the implementation of
a data access component into a service wrapper. The service wrapper encapsulates
the functionality of the data access component, shielding you from much of the
lower-level implementation. This allows you to concentrate on implementing
services and building client applications to access the services. For more
information on using Flash Builder to access data services, see
[Building data-centric applications with Flash Builder](./building-data-centric-applications-with-flash-builder/index.md).

### Providing access to services

By default, Adobe Flash Player blocks access to any host that is not exactly
equal to the one used to load an application. If you do not use a server side
application, such as LiveCycle Data Services or BlazeDS, to proxy requests, an
HTTP service or web service must either be on the server hosting your
application, or the remote server that hosts the HTTP or web service must define
a `crossdomain.xml` file. A `crossdomain.xml` file provides a way for a server
to indicate that its data and documents are available to SWF files served from
certain domains, or from all domains. The `crossdomain.xml` file must be in the
web root of the server that the application is contacting.

### HTTPService components

Use HTTPService components to send HTTP GET or POST requests and include the
data from HTTP responses in a client application. If you are using Flex to build
desktop applications (runs in Adobe AIR®), HTTP PUT and DELETE are supported.

If you use LiveCycle Data Services or BlazeDS, you can use an HTTPProxyService,
which allows you to to use additional HTTP methods. With an HTTPProxyService,
you can send GET, POST, HEAD, OPTIONS, PUT, TRACE, or DELETE requests.

An HTTP service can be any HTTP URI that accepts HTTP requests and sends
responses. Another common name for this type of service is a REST-style web
service. REST stands for Representational State Transfer and is an architectural
style for distributed hypermedia systems.

HTTPService components are a good option when you cannot expose the same
functionality as a SOAP web service or a remote object service. For example, you
can use HTTPService components to interact with JavaServer Pages (JSPs),
servlets, and ASP pages that are not available as web services or Remoting
Service destinations.

When you call the HTTPService object's `send()` method, it makes an HTTP request
to the specified URI, and an HTTP response is returned. Optionally, you can pass
arguments to the specified URI.

Flash Builder provides workflows that allow you to interactively connect to HTTP
services. For more information, see
[Accessing HTTP services](./building-data-centric-applications-with-flash-builder/connecting-to-data-services.md#accessing-http-services).

### WebService components

WebService components let you access SOAP web services, which are software
modules with methods. Web service methods are commonly called _operations_. Web
service interfaces are defined using Web Services Description Language (WSDL).
Web services provide a standards-compliant way for software modules that are
running on various platforms to interact with each other. For more information
about web services, see the web services section of the World Wide Web
Consortium website at <a
href="https://web.archive.org/web/20150921172759mp_/http://www.w3.org/2002/ws/">www.w3.org/2002/ws/</a>.

Client applications can interact with web services that define their interfaces
in a Web Services Description Language (WSDL) document, which is available as a
URL. WSDL is a standard format for describing the messages that a web service
understands, the format of its responses to those messages, the protocols that
the web service supports, and where to send messages.

Flex supports WSDL 1.1, which is described at <a
href="https://web.archive.org/web/20150921172759mp_/http://www.w3.org/TR/wsdl">www.w3.org/TR/wsdl</a>.
Flex supports both RPC-encoded and document-literal web services.

Flex supports web service requests and results that are formatted as SOAP
messages and are transported over HTTP. SOAP provides the definition of the
XML-based format that you can use for exchanging structured and typed
information between a web service client, such as an application built with
Flex, and a web service.

You can use a WebService component to connect to a SOAP-compliant web service
when web services are an established standard in your environment. WebService
components are also useful for objects that are within an enterprise
environment, but not necessarily available on the source path of the web
application.

Flash Builder provides workflows that allow you to interactively connect to web
services. For more information, see
[Accessing web services](./building-data-centric-applications-with-flash-builder/connecting-to-data-services.md#accessing-web-services).

### RemoteObject components

Remote object services let you access business logic directly in its native
format rather than formatting it as XML, as you do with REST-style services or
web services. This saves you the time required to expose existing logic as XML.
Another benefit of remote object services is the speed of communication across
the wire. Data exchanges still happen over HTTP or https, but the data itself is
serialized into a binary representation. Using RemoteObject components results
in less data going across the wire, reduced client-side memory usage, and
reduced processing time.

ColdFusion, PHP, BlazeDS, and LiveCycle Data Services can use server-side typing
when accessing data on the server. The client application accesses a Java
object, ColdFusion component (which is a Java object internally), or PHP class
directly by remote invocation of a method on a designated object. The object on
the server uses its own native data types as arguments, queries a database with
those arguments, and returns values in its native data types.

When server-side typing is not available, Flash Builder has tools to implement
client-side typing. Use the Flash Builder to configure and define types for data
returned from the service. Client-side typing allows the client application to
query a database and retrieve properly typed data. Client-side typing is
required for any service that does not define the type of data returned by the
service.

Flash Builder provides workflows that allow you to interactively connect to
remote object services. For more information, see
[Connecting to data services](./building-data-centric-applications-with-flash-builder/connecting-to-data-services.md).

More Help topics

[Building data-centric applications with Flash Builder](./building-data-centric-applications-with-flash-builder/index.md)

[Accessing HTTP services](./building-data-centric-applications-with-flash-builder/connecting-to-data-services.md#accessing-http-services)

![](./img/flexLinkIndicator.png)  <a
href="https://web.archive.org/web/20150921172759mp_/http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf64a29-7fff.html"
title="http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf64a29-7fff.html">About
events</a>

![](./img/flexLinkIndicator.png)  <a
href="https://web.archive.org/web/20150921172759mp_/http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf64c3d-7fff.html"
title="http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf64c3d-7fff.html">About
data binding</a>

![](./img/flexLinkIndicator.png)  <a
href="https://web.archive.org/web/20150921172759mp_/http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf69084-7f2b.html"
title="http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf69084-7f2b.html">Using
cross-domain policy files</a>
