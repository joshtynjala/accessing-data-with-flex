# Implementing ColdFusion services

When implementing services in ColdFusion, implement the services as ColdFusion
component (CFC) files. Each CFC contains functions that provide the service
operations.

You can create ColdFusion services in any IDE, such as Adobe ColdFusion®
Builder™. Flash Builder does not provide an editor optimized for editing
ColdFusion files. However, if you open a ColdFusion file in Flash Builder, Flash
Builder launches the application on your system that is associated with
ColdFusion files.

## Example ColdFusion services

You can implement a basic ColdFusion service by creating a ColdFusion component
(CFC) that contains functions for the service operations. The following example,
`employeeService.cfc`, illustrates an EmployeeService that implements two
functions. The `getAllIEmployees()` function retrieves all employee records in
the database. The `getEmployees()` function returns a single employee record
based on the `emp_no` argument to the function.

This example illustrates client-side typing. The service returns untyped data.
Flash Builder uses client-side typing to introspect the returned data and define
a data type.

Subsequent examples illustrate how to implement services for paging and data
management.

You can also use Flash Builder to access services that implement server-side
typing. See
[Client-side and server-side typing](./client-side-and-server-side-typing.md).

Examples illustrating server-side typing were not available at the time this
document was completed. For server-side typing examples, see
[Flash Builder server-side type examples](https://web.archive.org/web/20160902121422mp_/http://www.adobe.com/go/learn_flex4_serversidetype_en).

### ColdFusion example implementing a basic service

This example shows how to implement a basic service in ColdFusion. The example
is based on code that Flash Builder generates when accessing a database table.
See
[Generating a sample ColdFusion service from a database table](../building-data-centric-applications-with-flash-builder/connecting-to-data-services.md#generating-a-sample-coldfusion-service-from-a-database-table).

This example implements client-side typing. See
[Client-side and server-side typing](./client-side-and-server-side-typing.md).

For examples of server-side typing, see
[Flash Builder server-side type examples](https://web.archive.org/web/20160902121422mp_/http://www.adobe.com/go/learn_flex4_phpservertype_en).

Important: Example services are for prototyping only. Use the example service
only in a trusted development environment. Before deploying this service, be
sure to increase security and restrict access appropriately. For information on
writing secure ColdFusion services, see the ColdFusion documentation
[About User Security](https://web.archive.org/web/20160902121422mp_/http://www.adobe.com/go/cf9_usersecurity).

    <cfcomponent output="false">

    <!---
        This sample service contains functions that illustrate typical service operations.
        This code is for prototyping only.

        Authenticate the user prior to allowing them to call these methods. You can find more
        information at http://www.adobe.com/go/cf9_usersecurity

    --->
        <cffunction name="getAllemployees" output="false" access="remote" returntype="any" >

            <!--- Retrieve set of records and return them as a query or array.
                    Add authorization or any logical checks for secure access to your data --->

            <cfset var qAllItems="">
            <cfquery name="qAllItems" datasource="employees">
                SELECT * FROM employees
            </cfquery>
            <cfreturn qAllItems>

        </cffunction>

        <cffunction name="getemployees" output="false" access="remote"  returntype="any" >
            <cfargument name="emp_no" type = "numeric" required="true" />

            <!--- Retrieve a single record and return it as a query or array.
                    Add authorization or any logical checks for secure access to your data --->

            <cfset var qItem="">
            <cfquery name="qItem" datasource="employees">
                SELECT *
                FROM employees
                WHERE emp_no = <CFQUERYPARAM CFSQLTYPE="CF_SQL_INTEGER" VALUE="#ARGUMENTS.emp_no#">
            </cfquery>

            <cfreturn qItem>

        </cffunction>

    </cfcomponent>

Highlights of EmployeeService:

- Connects to the employees database and accesses the employees table in the
  database.

- Returns an array of objects.

  When programming using the Flex framework, services return data only. The
  client application handles the formatting and presentation of the data. This
  model differs from traditional ColdFusion CFM applications that return data
  formatted in an HTML template.

  Flex handles returned recordsets as an array of objects. Each row represents a
  retrieved record from the database. Each column of the database record becomes
  a property of the returned object. The client application can now access the
  returned data as objects with a set of properties.

  Configure the data type for the returned object. See
  [Client-side and server-side typing](./client-side-and-server-side-typing.md).

- The ColdFusion server provides error handling.

  The error handling provided by ColdFusion is useful when debugging a service.
  In the ColdFusion Administrator, modify the Debugging and Logging settings to
  provide robust debugging information.

  The Flash Builder Test Operation interface displays information returned by
  ColdFusion server.

  See [Debugging remote services](./debugging-remote-services.md) for more
  information on testing services.

- Uses `cfqueryparam` for constructing database queries.

  `cfqueryparam` is a defense against SQL injection statements in calls to the
  server. For more information, see
  [Enhancing security with cfqueryparam](https://web.archive.org/web/20160902121422mp_/http://help.adobe.com/en_US/ColdFusion/9.0/Developing/WSc3ff6d0ea77859461172e0811cbec22c24-7c36.html)
  in the ColdFusion documentation.

- Authenticate users before providing access to the functions in this service.

  The sample code does not illustrate how to authenticate users. See the
  ColdFusion documentation,
  [About User Security](https://web.archive.org/web/20160902121422mp_/http://www.adobe.com/go/cf9_usersecurity).

### ColdFusion example implementing paging

Flash Builder tools allow you to implement paging of data retrieved from a
remote service. Paging is the incremental retrieval of large data sets.

Flash Builder requires specific function signatures to implement paging. The
following code example provides an example of one way to implement a ColdFusion
service for paged data.

The EmployeeServicePaged example is based on the code generated by Flash Builder
when accessing a database table. See
[Generating a sample ColdFusion service from a database table](../building-data-centric-applications-with-flash-builder/connecting-to-data-services.md#generating-a-sample-coldfusion-service-from-a-database-table).

Important: Example services are for prototyping only. Use the example service
only in a trusted development environment. The example allows anyone with
network access to your server to be able to access, modify, or delete data in
the database table. Before deploying this service, be sure to increase security
and restrict access appropriately. For information on writing secure ColdFusion
services, see the ColdFusion documentation
[About User Security](https://web.archive.org/web/20160902121422mp_/http://www.adobe.com/go/cf9_usersecurity).

    <cfcomponent output="false">

    <!---
        This sample service contains functions that illustrate typical service operations.
        This code is for prototyping only.

        Authenticate the user prior to allowing them to call these methods. You can find more
        information at http://www.adobe.com/go/cf9_usersecurity
    --->
        <cffunction name="count" output="false" access="remote" returntype="numeric" >

            <!---        Return the number of items in your table.
                    Add authorization or any logical checks for secure access to your data --->
            <cfquery name="qread" datasource="employees">
                SELECT COUNT(emp_no) AS itemCount FROM employees
            </cfquery>

            <cfreturn qread.itemCount>

        </cffunction>

        <cffunction name="getemployees_paged" output="false" access="remote" returntype="any" >
            <cfargument name="startIndex" type="numeric" required="true" />
            <cfargument name="numItems" type="numeric" required="true" />

        <!---    Return a page of numRows number of records as an array or
            query from the database for this startRow.
            Add authorization or any logical checks for secure access to your data --->
        <!---    The LIMIT keyword is valid for mysql database only.
            Modify it for your database --->

            <cfset var qRead="">
            <cfquery name="qRead" datasource="employees">
                SELECT * FROM employees LIMIT #startIndex#, #numItems#
            </cfquery>
            <cfreturn qRead>

        </cffunction>
    </cfcomponent>

The EmployeeServicePaged service returns untyped data. Use the Flash Builder
tools to configure the return type for `getEmployees_Paged()`. After configuring
the return type, enable paging on the `getEmployees_Paged()` operation.

### ColdFusion example implementing data management operations

Flash Builder tools allow you to implement data management functionality for
remote services. Data management is the synchronization of updates to data on a
server from the client application.

Flash Builder requires a combination of specific function signatures to
implement data management. The following code example provides an example of one
way to implement a ColdFusion service for data management.

The following EmployeeServiceDM example is based on the code generated by Flash
Builder when accessing a database table. See
[Generating a sample ColdFusion service from a database table](../building-data-centric-applications-with-flash-builder/connecting-to-data-services.md#generating-a-sample-coldfusion-service-from-a-database-table).

Important: Example services are for prototyping only. Use the example service
only in a trusted development environment. Before deploying this service, be
sure to increase security and restrict access appropriately. For information on
writing secure ColdFusion services, see the ColdFusion documentation
[About User Security](https://web.archive.org/web/20160902121422mp_/http://www.adobe.com/go/cf9_usersecurity).

    <cfcomponent output="false">

    <!---
        This sample service contains functions that illustrate typical service operations.
        This code is for prototyping only.

        Authenticate the user prior to allowing them to call these methods. You can find more
        information at http://www.adobe.com/go/cf9_usersecurity
    --->
        <cffunction name="getAllemployees" output="false" access="remote" returntype="any" >

            <!--- Auto-generated method
                    Retrieve set of records and return them as a query or array.
                    Add authorization or any logical checks for secure access to your data --->

            <cfset var qAllItems="">
            <cfquery name="qAllItems" datasource="employees">
                SELECT * FROM employees
            </cfquery>
            <cfreturn qAllItems>

        </cffunction>

        <cffunction name="getemployees" output="false" access="remote"  returntype="any" >
            <cfargument name="emp_no" type = "numeric" required="true" />

            <!---
                    Retrieve a single record and return it as a query or array.
                    Add authorization or any logical checks for secure access to your data --->

            <cfset var qItem="">
            <cfquery name="qItem" datasource="employees">
                SELECT *
                FROM employees
                WHERE emp_no = <CFQUERYPARAM CFSQLTYPE="CF_SQL_INTEGER" VALUE="#ARGUMENTS.emp_no#">
            </cfquery>

            <cfreturn qItem>

        </cffunction>

        <cffunction name="createemployees" output="false" access="remote" returntype="any" >
            <cfargument name="item" required="true" />

            <!---   Insert a new record in the database.
                Add authorization or any logical checks for secure access to your data --->

            <cfquery name="createItem" datasource="employees" result="result">
                INSERT INTO employees (birth_date, first_name, last_name, gender, hire_date)
                        VALUES (<CFQUERYPARAM cfsqltype="CF_SQL_DATE" VALUE="#item.birth_date#">,
                                <CFQUERYPARAM cfsqltype="CF_SQL_VARCHAR" VALUE="#item.first_name#">,
                                <CFQUERYPARAM cfsqltype="CF_SQL_VARCHAR" VALUE="#item.last_name#">,
                                <CFQUERYPARAM cfsqltype="CF_SQL_CHAR" VALUE="#item.gender#">,
                                <CFQUERYPARAM cfsqltype="CF_SQL_DATE" VALUE="#item.hire_date#">)

            </cfquery>

            <!--- The GENERATED_KEY is valid for mysql database only, you can modify it for your database --->
            <cfreturn result.GENERATED_KEY/>

        </cffunction>

        <cffunction name="updateemployees" output="false" access="remote" returntype="void" >
            <cfargument name="item" required="true" />

            <!--- Update an existing record in the database.
                  Add authorization or any logical checks for secure access to your data --->

             <cfquery name="updateItem" datasource="employees">
                UPDATE employees SET birth_date = <CFQUERYPARAM cfsqltype=CF_SQL_DATE VALUE="#item.birth_date#">,
                                 first_name = <CFQUERYPARAM cfsqltype=CF_SQL_VARCHAR VALUE="#item.first_name#">,
                                 last_name = <CFQUERYPARAM cfsqltype=CF_SQL_VARCHAR VALUE="#item.last_name#">,
                                 gender = <CFQUERYPARAM cfsqltype=CF_SQL_CHAR VALUE="#item.gender#">,
                                 hire_date = <CFQUERYPARAM cfsqltype=CF_SQL_DATE VALUE="#item.hire_date#">

                WHERE emp_no = <CFQUERYPARAM CFSQLTYPE="CF_SQL_INTEGER" VALUE="#item.emp_no#">
            </cfquery>

        </cffunction>

        <cffunction name="deleteemployees" output="false" access="remote" returntype="void" >
            <cfargument name="emp_no" type="numeric" required="true" />

            <!--- Delete a record in the database.
                  Add authorization or any logical checks for secure access to your data --->

            <cfquery name="delete" datasource="employees">
                DELETE FROM employees
                WHERE emp_no = <CFQUERYPARAM CFSQLTYPE="CF_SQL_INTEGER" VALUE="#ARGUMENTS.emp_no#">
            </cfquery>

        </cffunction>
    </cfcomponent>

The EmployeeServiceDM service returns untyped data. Use the Flash Builder tools
to configure the return type for `getAllEmployeess()` and `getEmployees()`. Use
Employee for the custom data type returned by these operations.

After configuring the return type, enable data management on the Employee data
type.

## Generating CFCs using Adobe ColdFusion Builder

Adobe® ColdFusion® Builder™ provides the Adobe CFC Generator. Use CFC Generator
to generate an ORM CFC or a traditional CFC from a set of database tables. The
CFC generated by ColdFusion Builder can then be used as a data service in Flash
Builder. The Adobe CFC Generator creates services that implement server side
typing.

For details, see
[Using Adobe CFC Generator](https://web.archive.org/web/20160902121422mp_/http://help.adobe.com/en_US/ColdFusionBuilder/Using//WS0ef8c004658c1089-1b4fc34c122964e1318-8000.html).

Note: ColdFusion object relational mapping (ORM uses an object model to define a
mapping strategy for storing and retrieving data from a relational database. See
[ColdFusion ORM](https://web.archive.org/web/20160902121422mp_/http://help.adobe.com/en_US/ColdFusion/9.0/Developing/WSD628ADC4-A5F7-4079-99E0-FD725BE9B4BD.html).

More Help topics

[Configuring data types for data service operations](../building-data-centric-applications-with-flash-builder/configuring-data-types-for-data-service-operations.md)

[Accessing ColdFusion services](../building-data-centric-applications-with-flash-builder/connecting-to-data-services.md#accessing-coldfusion-services)

[Generating a sample ColdFusion service from a database table](../building-data-centric-applications-with-flash-builder/connecting-to-data-services.md#generating-a-sample-coldfusion-service-from-a-database-table)

[Managing the access of data from the server](../building-data-centric-applications-with-flash-builder/managing-the-access-of-data-from-the-server.md)
