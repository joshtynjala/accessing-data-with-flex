# Configuring data types for data service operations

When connecting to a data service, Flash Builder needs to know the data type for
the data returned by a service operation. The data types supported are those
types recognized by AMF to exchange data with a data service or remote service.

Many data services define the type of returned data on the server (server-side
typing). However, if the server does not define the type, then the client
application must configure the type for returned data (client-side typing).

Service operations that specify parameters must also specify a type
corresponding to data accessed on the service. With client-side typing, you
configure the type for input parameters.

When configuring types for client-side typing, Flash builder recognizes only AMF
data types. The type can also be a custom data type representing complex data,
or void to indicate the operation does not return any data.

You can configure user-defined types for service operations that return complex
data. For example, if you are retrieving records from an employee database, then
you would define a complex data return as Employee. In this case, the custom
data type for Employee would contain entries for each field in the database
record.

**Data Types for client side typing**

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<thead>
<tr class="header">
<th><p>Data Type</p></th>
<th><p>Description</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>ActionScript types</p></td>
<td><p>Boolean</p>
<p>Boolean[]</p>
<p>ByteArray</p>
<p>ByteArray[]</p>
<p>Date</p>
<p>Date[]</p>
<p>int</p>
<p>int[]</p>
<p>Number</p>
<p>Number[]</p>
<p>Object</p>
<p>Object[]</p>
<p>String</p>
<p>String[]</p></td>
</tr>
<tr class="even">
<td><p>No data returned</p></td>
<td><p>void</p></td>
</tr>
<tr class="odd">
<td><p>User-defined type</p></td>
<td><p><em>CustomType</em></p>
<p><em>CustomType</em>[]</p></td>
</tr>
</tbody>
</table>

**User-defined type (Employee)**

| Field      | Data Type |
| ---------- | --------- |
| emp_no     | Number    |
| first_name | String    |
| last_name  | String    |
| hire_date  | Date      |
| birth_date | Date      |

## Authenticating access to services

Typically data services require user authentication before allowing access to
the services. PHP, BlazeDS, and ColdFusion services that provide access using
the HTTP protocol can require additional authentication. In some cases, these
types of services require both HTTP and remote authentication.

Flash Builder provides an option for service authentication when you are doing
the following:

- Configuring the return type for an operation

  See
  [Configuring the return type for data from an operation](#configuring-the-return-type-for-data-from-an-operation).

- Using the Test Operation interface

  See [Testing service operations](./testing-service-operations.md).

When you specify Authentication Required, Flash Builder opens the Service
Authentication dialog. Depending on the type of service you are accessing, you
can specify Basic Authentication or Remote Authentication.

### Basic authentication

Basic authentication provides access to HTTP and web services. Provide the user
name and password for access to these services.

Specify Remember Username and Password if you want Flash Builder to use the
specified credentials throughout the session.

### Remote authentication

Remote authentication provides access to remote object services. Remote object
services are services implemented as remote objects using ColdFusion, PHP,
BlazeDS, or LiveCycle Data Services.

Flash Builder does not provide the remote authentication login interface for
projects that do not implement remote object services.

Provide the user name and password for access to the remote object services.

Specify Remember Username and Password if you want Flash Builder to use the
specified credentials throughout the session.

## Configuring input parameters to an operation

For client side typing, you configure input parameters to operations available
from the data service.

The following procedure assumes that you have connected to a data service in
Flash Builder, and the data service has operations that require configurable
input parameters.

1.  In the Data/Services view, select an operation that contains configurable
    input parameters. From the context menu for the operation, select Configure
    Input Types.

2.  In the Configure Input Types dialog, for each argument to the operation,
    select a data type from the list of available type. Click OK.

    If you previously defined custom return data types for the service, those
    types are available for selection.

For server-side typing, the service specifies the data type for input
parameters.

## Configuring the return type for data from an operation

A service that defines data types returned by an operation provides server-side
typing. If a service does not define the data type returned by an operation,
then Flash Builder uses client-side typing to define the returned data type.

Flash Builder introspects the data returned from a service operation to
determine the data type. When you configure the return type of an operation, you
have two options:

- Auto-detect the Return Type from Sample Data.

  If the service implements server-side typing, Flash Builder detects the data
  type defined by the service.

  If the service does not implement server-side typing, Flash Builder creates a
  custom type for the client application. For client-side typing, you provide a
  name for the custom type. Typically, the name describes the data returned. For
  example, if the operation returns an array of books from a database table,
  then you name the type Book.

- Use an existing type

  An existing type can be a type defined by the service, an ActionScript type,
  or a previously defined custom type.

The procedures Flash Builder uses for introspecting data differs slightly,
depending on the type of data service. For example, the procedure to introspect
and configure the return type for an HTTP service differs from the procedure for
PHP or ColdFusion services.

### Merging and changing data types

During the introspection of server data, you can merge fields from another data
type or create a data type based on an existing data type. Here are some of the
way you can modify a custom data type:

- Use a new name for an existing data type

  Use a new name if you plan to use returned data in different ways in the
  client application.

  For example, retrieving employee data that can be used in employee summary and
  employee detail tables in the client application.

- Merge fields

  You can add returned fields to an existing data type. Adding additional fields
  is useful when associating data from multiple sources. For example, for a JOIN
  operation that returns data retrieved from multiple database tables.

  Another example is data received from different services. For example, merging
  Book data received from both an HTTP service and a ColdFusion service.

### Configuring a custom data type (PHP or ColdFusion services)

This procedure assumes that you have connected to a data service implemented
with PHP or ColdFusion.

1.  In the Data/Services view, from the context menu for an operation, select
    Configure Return Type.

2.  If the operation has arguments, enter the argument values. Specify the
    correct data type for the argument.

3.  (New or Modified Custom Type) Select Auto Detect Type of Data Returned by
    this Operation.

    If the service requires authentication, select Authentication Required and
    provide credentials as needed. See
    [Authenticating access to services](#authenticating-access-to-services).

    Flash Builder introspects the operation and builds a custom data type.

    Specify a name for the custom data type.

    If you have previously defined a custom data type, you can choose to add the
    returned fields to the definition of the existing custom data type.

4.  (Use Existing Type) Use this option to specify an ActionScript type or a
    type you previously configured.

5.  Click Finish.

### Configuring a custom data type (HTTP service)

This procedure assumes that you have connected to an HTTP service.

1.  In the Data/Services view, from the context menu for an operation, select
    Configure Return Type.

2.  (New Custom Type) Select Auto Detect Type of Data Returned by this
    Operation.

    If the service requires authentication, select Authentication Required and
    provide credentials as needed.

    Flash Builder introspects the operation and builds a custom data type.
    Choose a method for Flash Builder to pass parameter values for the operation
    and click Next:

    - (Enter Parameter Values) For each parameter, specify a value.

      You can also specify the data type for a parameter. Flash Builder
      automatically selects the default data type.

    - (Enter Service URL) Enter the URL to the HTML service, including
      parameters and values in the URL. For example:

      > http://httpserviceaddress/service_operation?param1=94105

    - (Enter XML/JSON Response) Copy the XML/JSON response to the text box.

      Use this option if you are offline, or if the HTTP service is still under
      development, but you know the response from the server.

3.  (New Custom Type, continued) Specify a name for a custom data type or select
    a node from the returned data.

    If you select a node for the returned data, Flash Builder creates a custom
    data type for data returned for that node.

    Indicate if the returned data is returned as an array.

    If the service returns an XML file, the Select Root drop-down list is
    enabled. Select a node from the XML file to specify a data type.

4.  (Use Existing Type) Use this option to specify an ActionScript type or a
    type you previously configured.

5.  Click Finish.
