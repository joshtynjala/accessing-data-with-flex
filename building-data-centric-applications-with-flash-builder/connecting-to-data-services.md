# Connecting to data services

Use the Flash Builder Service wizard to connect to data services.

For remote object services, you typically create a Flex project with the
corresponding application server type. Flash Builder introspects the service and
can configure return types for data returned by the service.

Remote object services include data services implemented in ColdFusion, PHP,
BlazeDS, and LiveCycle Data Services.

For information on using the LiveCycle Service Discovery wizard, see
[Using LiveCycle Discovery](https://web.archive.org/web/20170121212148mp_/http://www.adobe.com/go/learn_flex4_lcservicediscovery_en/).

## Accessing ColdFusion services

Use the Flash Builder Service wizard to access a ColdFusion data service that
has been implemented as a ColdFusion component (CFC). Flex accesses these
services as remote objects.

Use a Flex project that specifies ColdFusion as the application server type.
When creating the Flex project, specify Use Remote Object Access Service and use
ColdFusion Flash Remoting.

### Connecting to ColdFusion data services

This procedure assumes that you have implemented a ColdFusion service and have
created a Flex project for accessing ColdFusion services.

1.  From the Flash Builder Data menu, select Connect to ColdFusion to open the
    Service window.

2.  In the Configure ColdFusion Service dialog, browse to the location of the
    CFC implementing the service.

    > Note: If you have not implemented a ColdFusion service, Flash Builder can
    > generate a sample service from a single database table. Use the generated
    > sample as an example of how to access data services. See
    > [Generating a sample ColdFusion service from a database table](#generating-a-sample-coldfusion-service-from-a-database-table).

3.  (Optional) Modify the service details.

    <table>
    <colgroup>
    <col style="width: 50%" />
    <col style="width: 50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td><p><strong>Service Name</strong></p></td>
    <td><p>Specify a name for the service.</p>
    <p>Flash Builder generates a name for the service, based on the filename
    for the service.</p>
    <p>There are restrictions to names you can use for a service. See <a
    href="WSbde04e3d3e6474c42469a02f1237727e033-8000.html">Naming data
    services</a>.</p></td>
    </tr>
    <tr class="even">
    <td><p><strong>Service Package</strong></p></td>
    <td><p>Specify a name for the package that contains generated
    ActionScript files that access the service.</p>
    <p>Flash Builder generates a package based on the service name and
    places it in a <samp>services</samp> package.</p></td>
    </tr>
    <tr class="odd">
    <td><p><strong>Data Types Package</strong></p></td>
    <td><p>Specify a name for the package that contains generated
    ActionScript class files that define data types retrieved from the
    service.</p>
    <p>By default, Flash Builder creates the <samp>valueObjects</samp>
    package.</p></td>
    </tr>
    </tbody>
    </table>

4.  (Optional) Click Next to view the service operations.

5.  Click Finish to generate ActionScript files that access the service.

Note: After connecting to a service, you can modify the service properties.
Select the service in the Data/Services view. From the context menu, select
Properties.

Next step:
[Configuring data types for data service operations](./configuring-data-types-for-data-service-operations.md).

### Generating a sample ColdFusion service from a database table

Flash Builder can generate a sample ColdFusion service that you can use as a
prototype for your own services. The sample service accesses a single database
table and has methods for create, read, update, and delete.

Flash Builder configures return data types for the generated services and
enables data access functionality such as paging and data management.

Important: Use the generated service only in a trusted development environment.
The generated code allows anyone with network access to your server to be able
to access, modify, or delete data in the database table. Before deploying this
service, be sure to increase security and restrict access appropriately. For
information on writing secure services, see
[Securing Data Services](https://web.archive.org/web/20170121212148mp_/http://www.adobe.com/go/flex_security).

The following procedure assumes that you have created a Flex project for
accessing ColdFusion services and have ColdFusion data sources available.

1.  From the Flash Builder Data menu, select Connect to ColdFusionto open the
    Service wizard.

2.  In the Configure ColdFusion Service dialog, click the link to generate a
    sample service.

3.  Select Generate from RDS Datasource and specify a ColdFusion Data Source and
    Table.

    If the table does not define a primary key, select a Primary Key for the
    table.

    > Note: If you do not have ColdFusion data source available, select Generate
    > from Template. Flash Builder writes a sample ColdFusion component (CFC)
    > with typical service operations. Uncomment specific functions in the CFC
    > and modify the operations to create a sample service that you can use as a
    > prototype.

4.  Use the default location or specify a new location. Click OK.

    Flash Builder generates the sample service. Modify the Service Name and
    package locations to override the default values.

5.  (Optional) Click Next to view operations in the service.

6.  Click Finish.

    Flash Builder generates ActionScript files that access the sample service.
    Flash Builder also opens the sample service in an editor on your system that
    is registered to edit ColdFusion CFC files.

## Accessing PHP services

Use the Flash Builder Service wizard to connect to data services implemented in
PHP. Flex uses Action Message Format (AMF) to serialize data between the client
application and the data service. Flash Builder installs the Zend AMF framework
to provide access to services implemented in PHP. See
[Installing Zend Framework](./installing-zend-framework.md).

Access PHP data services from a Flex project that specifies PHP as the
application server type. The data service must be available under the web root
you specified when configuring the project for PHP. Place the service in a
services directory, as listed below:

    <webroot>/MyServiceFolder/services

### Connecting to PHP data services

This procedure assumes that you have implemented a PHP service and have created
a Flex project for accessing PHP services.

1.  From the Flash Builder Data menu, select Connect to PHPto open the Service
    wizard.

2.  In the Configure PHP Service dialog, browse to the PHP file implementing the
    service:

    > Note: If you have not implemented a PHP service, Flash Builder can
    > generate a sample service from a single database table. Use the generated
    > sample as an example of how to access data services. See
    > [Generating a sample PHP service from a database table](#generating-a-sample-php-service-from-a-database-table).

3.  (Optional) Modify the service details.

    <table>
    <colgroup>
    <col style="width: 50%" />
    <col style="width: 50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td><p><strong>Service Name</strong></p></td>
    <td><p>Specify a name for the service.</p>
    <p>Flash Builder generates a name for the service, based on the filename
    for the service.</p>
    <p>There are restrictions to names you can use for a service. See <a
    href="WSbde04e3d3e6474c42469a02f1237727e033-8000.html">Naming data
    services</a>.</p></td>
    </tr>
    <tr class="even">
    <td><p><strong>Service Package</strong></p></td>
    <td><p>Specify a name for the package that contains generated
    ActionScript files that access the service.</p>
    <p>Flash Builder generates a package based on the service name and
    places it in a <samp>services</samp> package.</p></td>
    </tr>
    <tr class="odd">
    <td><p><strong>Data Types Package</strong></p></td>
    <td><p>Specify a name for the package that contains generated
    ActionScript class files that define data types retrieved from the
    service.</p>
    <p>By default, Flash Builder creates the <samp>valueObjects</samp>
    package.</p></td>
    </tr>
    </tbody>
    </table>

4.  Click Next to view the service operations.

    If you do not have the supported version of the Zend Framework for accessing
    PHP services, Flash Builder prompts you to install the minimal version of
    the Zend Framework. See
    [Installing Zend Framework](./installing-zend-framework.md).

5.  Click Finish.

    Flash Builder generates ActionScript files that access the service.

Note: After connecting to a service, you can modify the service properties.
Select the service in the Data/Services view. From the context menu, select
Properties.

Next step:
[Configuring data types for data service operations](./configuring-data-types-for-data-service-operations.md).

### Generating a sample PHP service from a database table

Flash Builder can generate a sample PHP service that you can use as a prototype
for your own services. The sample service accesses a single MySQL database table
and has methods for create, read, update, and delete.

Flash Builder configures return data types for the generated services and
enables data access functionality such as paging and data management.

Important: Use the generated service only in a trusted development environment.
The generated code allows anyone with network access to your server to be able
to access, modify, or delete data in the database table. Before deploying this
service, be sure to increase security and restrict access appropriately. For
information on writing secure services, see
[Securing Data Services](https://web.archive.org/web/20170121212148mp_/http://www.adobe.com/go/flex_security).

The following procedure assumes that you have created a Flex project for
accessing PHP services and have a MySQL data source available.

1.  From the Flash Builder Data menu, select Connect to PHP to open the Service
    wizard.

2.  In the Configure PHP Service dialog, click the link to generate a sample
    service.

3.  Select Generate from Database and specify the information to connect to a
    database. Click Connect to Database.

    > Note: If you do not have PHP data source available, select Generate from
    > Template. Flash Builder writes a sample project with typical service
    > operations. Uncomment specific areas of the project and modify the
    > operations to create a sample service that you can use as a prototype.

4.  Select a table in the database and specify the primary key.

5.  Use the default location or specify a new location. Click OK.

    If you do not have the supported version of the Zend Framework for accessing
    PHP services, Flash Builder prompts you to install the minimal version of
    the Zend Framework. See
    [Installing Zend Framework](./installing-zend-framework.md).

    Flash Builder generates the sample service. Modify the Service Name and
    package locations to override the default values.

6.  (Optional) Click Next to view operations in the service.

7.  Click Finish.

    Flash Builder generates ActionScript files that access the sample service.
    Flash Builder also opens the sample service in an editor on your system that
    is registered to edit PHP files.

## Accessing HTTP services

Use the Flash Builder Service wizard to connect to REST-based HTTP services. You
can connect to HTTP services from any Flex project. You do not have to specify a
server technology for the project.

A cross-domain policy file is necessary when accessing services that are on a
different domain from the SWF file for the client application. See
[Using cross-domain policy files](https://web.archive.org/web/20170121212148mp_/http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf69084-7f2b.html).

<div xmlns:adobe="http://www.adobe.com/saxon">

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 25%" />
<col style="width: 25%" />
<col style="width: 25%" />
</colgroup>
<tbody>
<tr class="odd">
<td colspan="2"><h2 id="adobe-recommends">Adobe recommends</h2></td>
<td colspan="2"><h3 id="have-a-tutorial-you-would-like-to-share"><img
src="../img/TinyBlueTutIcon.png" /><a
href="https://web.archive.org/web/20170121212148mp_/http://www.adobe.com/community/publishing/download.html">Have
a tutorial you would like to share?</a></h3></td>
</tr>
<tr class="even">
<td colspan="4" height="10"></td>
</tr>
<tr class="odd">
<td width="5%"><span><img
src="https://web.archive.org/web/20170121212148im_/http://help.adobe.com/en_US/flex/accessingdata/images/calevans.png" /></span></td>
<td width="45%"><h3 id="flash-builder-data-wizard-for-http-services"><a
href="https://web.archive.org/web/20170121212148mp_/http://goo.gl/7V0A8">Flash
Builder Data Wizard for HTTP Services</a></h3>
<span><a
href="https://web.archive.org/web/20170121212148mp_/http://blog.calevans.com/">Cal
Evans</a></span><br />
<span>Blogger and author Cal Evans shows how to use the HTTP Data
Connection wizard to connect to a Rest-based service.</span></td>
<td width="5%"><span><img
src="https://web.archive.org/web/20170121212148im_/http://help.adobe.com/en_US/flex/accessingdata/images/leebrimelow.png" /></span></td>
<td width="45%"><h3 id="flash-builder-data-integration"><a
href="https://web.archive.org/web/20170121212148mp_/http://goo.gl/nqK9i">Flash
Builder Data Integration</a></h3>
<span><a
href="https://web.archive.org/web/20170121212148mp_/http://goo.gl/fToDa">Lee
Brimelow</a></span><br />
<span>Lee Brimelow shows how to access data through Twitter's REST-based
search API. Although this video tutorial is written for Flash Builder 4,
the concepts still apply.</span></td>
</tr>
</tbody>
</table>

### Configuring HTTP services

When accessing REST-based HTTP services, there are various ways to configure
access to the service. The Configure HTTP Service wizard supports the following:

- Base URL as prefix

  Base URL as prefix is convenient if you are accessing multiple operations from
  a single service. If you specify a base URL to the service, then for each
  operation you specify only the relative path to the HTTP operations.

  If you want access to multiple services, then you cannot use Base URL.

- URLs with query parameters

  When specifying a URL to an operation, you can include the query parameters
  for the service operations. The Configure HTTP Service wizard fills the
  Parameter table with each parameter included in the operation URL.

- RESTful services with delimited parameters

  Flash Builder supports access to RESTful services that use delimited
  parameters instead of GET query parameter. For example, suppose you use the
  following URL to access a RESTful service:

      http://restfulService/items/itemID

  Use curly brackets (`{}`) to specify the parameters in the operation URL. For
  example:

      http://restfulService/{items}/{itemID}

  Then the Configure HTTP Service wizard populates the Parameter Table:

  | Name   | Data Type | Parameter Type |
  | ------ | --------- | -------------- |
  | items  | String    | URL            |
  | itemID | String    | URL            |

  When specifying RESTful service parameters, the Data Type and Parameter Type
  are always configured as String and URL respectively.

  > Note: You can mix RESTful service parameters with query parameters when
  > specifying the URL to an operation.

- Path to a local file for an operation URL

  For an operation URL, you can specify a path to a local file that implements
  HTTP services. For example, specify the following for an operation URL:

      c:/MyHttpServices/MyHttpService.xml

- Adding GET and POST operations

  You can add additional operations when configuring an HTTP service. Click Add
  in the Operations table to add operations.

  Specify GET or POST for the operation method.

- Adding parameters to an operation

  You can add parameters to selected operations in the Operations table. Select
  the operation in Operations table and click Add in the Parameters table.

  Specify a name and Data Type for the added parameter. The Parameter Type, GET
  or POST, corresponds to the operation method.

- Content type for POST operations

  For POST operations, you can specify the content type. The content type can be
  either `application/x-www-form-urlencoded` or `application/xml`.

  If you choose `applicaton/xml` for the content type, Flash Builder generates a
  query parameter that cannot be edited. strXML is the default name. You specify
  the actual parameter at runtime.

  | Name   | Data Type | Parameter Type |
  | ------ | --------- | -------------- |
  | strXML | String    | POST           |

  You cannot add additional parameters for `application/xml` content type.

### Connecting to HTTP services

1.  From the Flash Builder Data menu, select Connect to HTTP to open the Service
    wizard.

2.  (Optional) Specify a base URL to use as a prefix to all operations.

3.  Under Operations, specify the following for each operation you want to
    access:

    - Specify the operation method (GET or POST)

    - URL to the service operation

      Include any parameters to the operation in the URL. Use curly brackets
      (`{}`) to specify REST-style service parameters.

      Flash Builder supports access to the following protocols:

      `http://`

      `https://`

      Standard absolute paths, such as `C:/` or `/Applications/`

    - A name for the operation

4.  For each operation parameter in a selected URL, specify the Name and Data
    Type of the parameter

5.  (Optional) Click Add or Delete to add or remove parameters to the selected
    operation.

6.  (Optional) Modify the service details.

    <table>
    <colgroup>
    <col style="width: 50%" />
    <col style="width: 50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td><p><strong>Service Name</strong></p></td>
    <td><p>Specify a name for the service.</p>
    <p>Flash Builder generates a name for the service, based on the filename
    for the service.</p>
    <p>There are restrictions to names you can use for a service. See <a
    href="WSbde04e3d3e6474c42469a02f1237727e033-8000.html">Naming data
    services</a></p></td>
    </tr>
    <tr class="even">
    <td><p><strong>Service Package</strong></p></td>
    <td><p>Specify a name for the package that contains generated
    ActionScript files that access the service.</p>
    <p>Flash Builder generates a package based on the service name and
    places it in a <samp>services</samp> package.</p></td>
    </tr>
    <tr class="odd">
    <td><p><strong>Data Types Package</strong></p></td>
    <td><p>Specify a name for the package that contains generated
    ActionScript class files that define data types retrieved from the
    service.</p>
    <p>By default, Flash Builder creates the <samp>valueObjects</samp>
    package.</p></td>
    </tr>
    </tbody>
    </table>

7.  (Optional) Modify the generated package name for the service.

8.  Click Finish.

    Flash Builder generates ActionScript files that access the service.

After connecting to the HTTP service, configure the return types for service
operations. When configuring the return type, you also configure the type for
parameters to the operation. See
[Configuring data types for data service operations](./configuring-data-types-for-data-service-operations.md).

Note: After connecting to a service, you can modify the service properties.
Select the service in the Data/Services view. From the context menu, select
Properties.

Next step:
[Configuring data types for data service operations](./configuring-data-types-for-data-service-operations.md).

## Accessing an XML file implementing HTTP services

You can access a static XML file that implements an HTTP service. The static XML
file can be a local file or available as a URL.

The service uses a GET method that returns an XML response. This feature is
useful for learning about HTTP services in Flex and for prototyping mock data in
client applications.

When accessing the service, you specify the node returning the XML response.
Flash Builder uses this node to automatically configure a return type for the
data. After connecting to the service, you can bind operations to the service to
user interface components.

### Connecting to an XML service file

1.  From the Flash Builder Data menu, select HTTP to open the Service wizard.

2.  Specify Local File or URL and browse to the file.

3.  Select a node in the file that contains the response you want.

    Indicate if the response is an Array.

    Flash Builder configures a return type for the selected node.

4.  Modify the service details.

    <table>
    <colgroup>
    <col style="width: 50%" />
    <col style="width: 50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td><p><strong>Service Name</strong></p></td>
    <td><p>Specify a name for the service.</p>
    <p>Flash Builder generates a name for the service, based on the filename
    for the service.</p>
    <p>There are restrictions to names you can use for a service. See <a
    href="WSbde04e3d3e6474c42469a02f1237727e033-8000.html">Naming data
    services</a></p></td>
    </tr>
    <tr class="even">
    <td><p><strong>Service Package</strong></p></td>
    <td><p>Specify a name for the package that contains generated
    ActionScript files that access the service.</p>
    <p>Flash Builder generates a package based on the service name and
    places it in a <samp>services</samp> package.</p></td>
    </tr>
    <tr class="odd">
    <td><p><strong>Data Types Package</strong></p></td>
    <td><p>Specify a name for the package that contains generated
    ActionScript class files that define data types retrieved from the
    service.</p>
    <p>By default, Flash Builder creates the <samp>valueObjects</samp>
    package.</p></td>
    </tr>
    </tbody>
    </table>

5.  (Optional) Modify the generated package name for the service.

6.  Click Finish.

    Flash Builder generates ActionScript files that access the service.

Note: After connecting to a service, you can modify the service properties.
Select the service in the Data/Services view. From the context menu, select
Properties.

## Accessing web services

Use the Flash Builder Service wizard to connect to web services (SOAP). You can
connect to web services from any Flex project. You do not have to specify a
server technology for the project.

A cross-domain policy file is necessary when accessing services that are on a
different domain from the SWF file for the client application.

### Connecting to web services

1.  From the Flash Builder Data menu, select Web Service to open the Service
    wizard.

2.  (BlazeDS/Data Services) If you have installed LiveCycle Data Services or
    BlazeDS, you can access the web service through a proxy.

    Select Through a BlazeDS/Data Services proxy destination.

    Specify a destination. Click Next and proceed to step 5.

    > Note: Accessing web services through a proxy is only enabled if your Flex
    > project specifies J2EE as the application server type.

3.  Enter a URI to the SOAP service.

4.  (Optional) Modify the service details.

    <table>
    <colgroup>
    <col style="width: 50%" />
    <col style="width: 50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td><p><strong>Service Name</strong></p></td>
    <td><p>Specify a name for the service.</p>
    <p>Flash Builder generates a name for the service, based on the WSDL
    URI.</p>
    <p>There are restrictions to names you can use for a service. See <a
    href="WSbde04e3d3e6474c42469a02f1237727e033-8000.html">Naming data
    services</a></p></td>
    </tr>
    <tr class="even">
    <td><p><strong>Service Package</strong></p></td>
    <td><p>Specify a name for the package that contains generated
    ActionScript files that access the service.</p>
    <p>Flash Builder generates a package based on the service name and
    places it in a <samp>services</samp> package.</p></td>
    </tr>
    <tr class="odd">
    <td><p><strong>Data Types Package</strong></p></td>
    <td><p>Specify a name for the package that contains generated
    ActionScript class files that define data types retrieved from the
    service.</p>
    <p>By default, Flash Builder creates the <samp>dataValues</samp>
    package.</p></td>
    </tr>
    </tbody>
    </table>

5.  (Optional) Configure the code generation for the service:

    <table>
    <colgroup>
    <col style="width: 50%" />
    <col style="width: 50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td><p><strong>Service</strong></p>
    <p><strong>Port</strong></p></td>
    <td><p>Select a service from the services available.</p>
    <p>Flash Builder generates a name for the service, based on the WSDL
    URI.</p></td>
    </tr>
    <tr class="even">
    <td><p><strong>Operation List</strong></p></td>
    <td><p>Select the operations from the service that you want to access in
    your client application.</p></td>
    </tr>
    </tbody>
    </table>

6.  Click Finish.

    Flash Builder generates ActionScript files that access the service.

Note: After connecting to a service, you can modify the service properties.
Select the service in the Data/Services view. From the context menu, select
Properties.

'After connecting to the web service, configure the return types for service
operations. See
[Configuring data types for data service operations](./configuring-data-types-for-data-service-operations.md)
for details.

## Accessing BlazeDS

You can only access BlazeDS services if you have installed Adobe® BlazeDS and
configured a Remote Development Services (RDS) server. See the LiveCycle Data
Services ES documentation for information on installing and configuring BlazeDS.

You typically access BlazeDS data services from a Flex project configured with
J2EE as the application server type.

### Connecting to BlazeDS services

This procedure assumes that you have installed BlazeDS, configured a Remote
Development Server, and have created a Flex project for accessing BlazeDS
services.

1.  From the Flash Builder Data menu, select Connect to BlazeDS to open the
    Service wizard.

2.  Select a destination to import.

3.  (Optional) Modify the service details.

    <table>
    <colgroup>
    <col style="width: 50%" />
    <col style="width: 50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td><p><strong>Service Name</strong></p></td>
    <td><p>Specify a name for the service.</p>
    <p>Flash Builder generates a name for the service, based on the
    destination.</p>
    <p>There are restrictions to names you can use for a service. See <a
    href="WSbde04e3d3e6474c42469a02f1237727e033-8000.html">Naming data
    services</a></p></td>
    </tr>
    <tr class="even">
    <td><p><strong>Service Package</strong></p></td>
    <td><p>Specify a name for the package that contains generated
    ActionScript files that access the service.</p>
    <p>Flash Builder generates a package based on the service name and
    places it in a <samp>services</samp> package.</p></td>
    </tr>
    <tr class="odd">
    <td><p><strong>Data Types Package</strong></p></td>
    <td><p>Specify a name for the package that contains generated
    ActionScript class files that define data types retrieved from the
    service.</p>
    <p>By default, Flash Builder creates the <samp>valueObjects</samp>
    package.</p></td>
    </tr>
    </tbody>
    </table>

4.  Click Finish.

    Flash Builder generates ActionScript files that access the service.

Note: After connecting to a service, you can modify the service properties.
Select the service in the Data/Services view. From the context menu, select
Properties.

## Accessing LiveCycle Data Services

You can only access services available from LiveCycle Data Services if you have
installed LiveCycle Data Services and configured a Remote Development Services
(RDS) server. See the LiveCycle Data Services documentation for information.

You can access LiveCycle Data Services from a Flex project configured with
either J2EE or ColdFusion as the application server type.

### Service types for LiveCycle Data Services

When connecting to LiveCycle Data Services, the following types of data services
are available as destinations:

- Remoting service

  Remoting services are implemented using AMF typing. These services do not
  provide server-side data management. You can use Flash Builder tools to
  configure client side data management. See
  [Enabling data management](../building-data-centric-applications-with-flash-builder/managing-the-access-of-data-from-the-server.md#enabling-data-management).

- Data service

  Data services are services that implement server-side data management. For
  more information, see your LiveCycle Data Services documentation.

- Web service

  Web services available through a LiveCycle Data Services proxy that is
  configured as an LiveCycle Data Services destination. Server-side typing is
  not typically provided when connecting to a web service.

### Data type configuration and data management

Flash Builder provides tools for client side data configuration and client side
data management. The Flash Builder tools that are available depends on the type
of the LiveCycle Data Services destination:

- Remoting service

  Remoting services implement AMF typing on the service. You do not configure
  return data types for remoting service destinations.

  However, you can use Flash Builder to generate code for client side data
  management. See
  [Enabling data management](../building-data-centric-applications-with-flash-builder/managing-the-access-of-data-from-the-server.md#enabling-data-management).

- Data service

  Data services implement server-side data types. You do not configure return
  data type for data service destinations.

  Data service destinations also provide server-side data management. You do not
  use client side data management with data service destinations.

- Web service

  Web service destinations available through a LiveCycle Data Service proxy
  typically do not implement server side typing. You can use Flash Builder tools
  to configure return types for web service operations. See
  [Configuring data types for data service operations](./configuring-data-types-for-data-service-operations.md).

  You can use Flash Builder to generate code for client side data management.
  See
  [Enabling data management](../building-data-centric-applications-with-flash-builder/managing-the-access-of-data-from-the-server.md#enabling-data-management).

### Connecting to LiveCycle Data Service destinations (Data service and remoting service destinations)

This procedure assumes that you have installed LiveCycle Data Services,
configured a Remote Development Server, and have created a Flex project for
accessing LCDS services.

1.  From the Flash Builder Data menu, select Connect to Data/Service to open the
    Service wizard.

2.  In the Select Service Type dialog, select LCDS. Click Next.

3.  Provide login credentials, if needed.

4.  (Optional) Modify the service details.

    <table>
    <colgroup>
    <col style="width: 50%" />
    <col style="width: 50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td><p><strong>Service Name</strong></p></td>
    <td><p>You do not provide a service name. Flesh Builder generates a
    service name. Flash Builder generates a name for the service, based on
    the destination.</p></td>
    </tr>
    <tr class="even">
    <td><p><strong>Service Package</strong></p></td>
    <td><p>Specify a name for the package that contains generated
    ActionScript files that access the service.</p>
    <p>Flash Builder generates a package based on the service name and
    places it in a <samp>services</samp> package.</p></td>
    </tr>
    <tr class="odd">
    <td><p><strong>Destinations</strong></p></td>
    <td><p>Specify one or more destinations available from the LiveCycle
    Data Services server.</p></td>
    </tr>
    <tr class="even">
    <td><p><strong>Data Types Package</strong></p></td>
    <td><p>Specify a name for the data type package. this package contains
    generated ActionScript class files that define data types retrieved from
    the service.</p>
    <p>By default, Flash Builder creates the <samp>valueObjects</samp>
    package.</p></td>
    </tr>
    </tbody>
    </table>

5.  Click Finish.

    Flash Builder generates ActionScript files that access the service.

Note: After connecting to a service, you can modify the service properties.
Select the service in the Data/Services view. From the context menu, select
Properties.

### Connecting to LiveCycle Data Service destinations (Web service destinations)

This procedure assumes that you have installed LiveCycle Data Services,
configured a Remote Development Server, and have created a Flex project for
accessing DS services.

1.  From the Flash Builder Data menu, select Connect to Data/Service to open the
    Service wizard.

2.  In the Select Service Type dialog, select Web Service. Click Next.

3.  Select through a LCDS/BlazeDS proxy destination.

4.  Provide login credentials, if needed.

5.  Select the destination.

6.  (Optional) Modify the service details. Click Next.

    <table>
    <colgroup>
    <col style="width: 50%" />
    <col style="width: 50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td><p><strong>Service Name</strong></p></td>
    <td><p>Specify a name for the service.</p>
    <p>Flash Builder generates a name for the service, based on the
    destination name.</p>
    <p>There are restrictions to names you can use for a service. See <a
    href="WSbde04e3d3e6474c42469a02f1237727e033-8000.html">Naming data
    services</a></p></td>
    </tr>
    <tr class="even">
    <td><p><strong>Service Package</strong></p></td>
    <td><p>Specify a name for the package that contains generated
    ActionScript files that access the service.</p>
    <p>Flash Builder generates a package based on the service name and
    places it in a <samp>services</samp> package.</p></td>
    </tr>
    <tr class="odd">
    <td><p><strong>Data Types Package</strong></p></td>
    <td><p>Specify a name for the package that contains generated
    ActionScript class files that define data types retrieved from the
    service.</p>
    <p>By default, Flash Builder creates the <samp>dataValues</samp>
    package.</p></td>
    </tr>
    </tbody>
    </table>

7.  (Optional) Configure the code generation for the service:

    <table>
    <colgroup>
    <col style="width: 50%" />
    <col style="width: 50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td><p><strong>Service</strong></p>
    <p><strong>Port</strong></p></td>
    <td><p>Select a service and Port from the services and ports
    available.</p></td>
    </tr>
    <tr class="even">
    <td><p><strong>Operation List</strong></p></td>
    <td><p>Select the operations from the service that you want to access in
    your client application.</p></td>
    </tr>
    </tbody>
    </table>

8.  Click Finish.

    Flash Builder generates ActionScript files that access the service.

Note: After connecting to a service, you can modify the service properties.
Select the service in the Data/Services view. From the context menu, select
Properties.

## Naming data services

Data services that are accessed from Flash Builder have restrictions on the
names allowed for the service. Some of these restrictions are not apparent until
you compile your application.

The naming guidelines for services are:

- The initial letter of the service cannot be a number.

- Service names cannot be ActionScript keywords.

- Do not use any ActionScript class name, including custom classes, as a service
  name.

- (PHP only) If a service name contains underscores, Flash Builder cannot import
  the service.

Note: It is good practice to use service names that are different from the names
of your MXML files.

More Help topics

[Creating a Flex project to access data services](./creating-a-flex-project-to-access-data-services.md)

[Using cross-domain policy files](https://web.archive.org/web/20170121212148mp_/http://livedocs.adobe.com/flex/3/html/security2_04.html)

![](../img/flashbuilderLinkIndicator.png) 
[Configure access to data services](https://web.archive.org/web/20170121212148mp_/http://help.adobe.com/en_US/flashbuilder/using/WSe4e4b720da9dedb5-1a92eab212e75b9d8b2-7f94.html)
