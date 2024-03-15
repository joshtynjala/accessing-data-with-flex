# Flash Builder code generation for client applications

Flash Builder generates client code that provides access to remote service
operations. Flash Builder generates code in the following circumstances:

- Connecting to a data service

- Refreshing the data service in Data/Services View

- Configuring a return type for an operation

- Binding a service operation to a user interface control

- Enabling paging for a service operation

- Enabling data management for a service

- Generating an event handler or a service call

<!-- -->

## Service classes

Use the Service wizard to connect to a data service. When you connect to a
service, Flash Builder generates an ActionScript class file that provides access
to the service operations.

For services that access a RemoteObject, the generated class extends the
RemoteObjectServiceWrapper class. Services implemented with PHP, ColdFusion,
BlazeDS, and ADEP Data Services typically access a RemoteObject.

For HTTP services, the generated class extends the HTTPServiceWrapper class.

For web services, the generated class extends WebServiceWrapper class.

Flash Builder bases the name of the generated class file on the name you
provided for the service in the Service wizard. By default, Flash Builder places
this class in the main source folder for a project. Typically, this folder is
`src`. The name of the package is based on the service name. For example, Flash
Builder generates the following ActionScript classes for an EmployeeService
class.

    - project
        |
        - src
            |
            + (default package)
            |
            + services
            |    |
            |    +employeeservice
            |        |
            |        + _Super_EmployeeService.as
            |        |
            |        + EmployeeService.as

The super class contains the implementation for the EmployeeService class.

Never edit the super class, which is a generated class. Modifications you make
to the super class can be overwritten. Any changes you make to the
implementation can result in undefined behavior.

In this example, use `EmployeeService.as` to extend the generated super class
and add your implementation.

## Classes for custom data types

Many remote data services provide server-side typing. These services return
complex data as a custom data type.

For remote data services that do not return typed data, Flash Builder provides
client-side typing. With client-side typing, you use the Flash Builder Connect
wizard to define and configure the data type for complex data returned by the
service. For example, for a service that returns employee database records, you
define and configure an Employee data type.

Flash Builder generates an ActionScript class for the implementation of each
custom data type returned by the service. Flash Builder uses this class to
create value objects, which it then uses to access data from the remote service.

For example, Flash Builder generates the following ActionScript classes for an
EmployeeService class that contains an Employee data type:

    - project
        |
        - src
            |
            + (default package)
            |
            + services
            |    |
            |    +employeeservice
            |        |
            |        + _Super_EmployeeService.as
            |        |
            |        + EmployeeService.as
            |
            + valueObjects
                |
                + _Super_Employee.as
                |
                + Employee.as

The super classes contain the implementation for the EmployeeService and the
Employee data type, respectively.

Never edit a generated super class. Modifications you make to the super class
can be overwritten. Any changes you make to the implementation can result in
undefined behavior.

In this example, use `EmployeeService.as` and `Employee.as` to extend the
generated super class and add your implementation.

## Binding a service operation to a user interface control

[Binding service operations to controls](./building-the-client-application.md#binding-service-operations-to-controls)
shows how you can bind data returned from service operations to a user interface
control. When you bind a service operation to a control, Flash Builder generates
the following code:

- Declarations tag containing a CallResponder and service tag

- Event handler for calling the service call

- Data binding between the control and the data returned from the operation

### Declarations tag

A Declarations tag is an MXML element that declares non-default, non-visual
properties of the current class. When binding a service operation to a user
interface, Flash Builder generates a Declarations tag containing a CallResponder
and a service tag. The CallResponder and generated service class are properties
of the container element, which is usually the Application tag.

The following example shows a Declarations tag providing access to a remote
EmployeeService:

    <fx:Declarations>
        <s:CallResponder id="getAllEmployeesResult"/>
        <employeesservice:EmployeesService id="employeesService"
            fault="Alert.show(event.fault.faultString + '\n'
            + event.fault.faultDetail)" showBusyCursor="true"/>
    </fx:Declarations>

### Call Responder

A CallResponder manages results for calls made to a service. It contains a token
property that is set to the Async token returned by a service call. The
CallResponder also contains a lastResult property, which is set to the last
successful result from the service call. You add event handlers to the
CallResponder to provide access to the data returned through the lastResult
property.

When Flash Builder generates a CallResponder, it generates an id property based
on the name of the service operation to which it is bound. The following code
sample shows CallResponders for two operations of an EmployeeService. The
`getAllItems()` operation is bound to the creationComplete event handler for a
DataGrid. The delete operation is bound to the selected item in the DataGrid.
The DataGrid displays the items retrieved from the `getAllItems()` service call
immediately after it is created. The Delete Item Button control removes the
selected record in the DataGrid from the database.

    <fx:Script>
        <![CDATA[
            import mx.events.FlexEvent;
            import mx.controls.Alert;

            protected function dg_creationCompleteHandler(event:FlexEvent):void
            {
                getAllItemsResult.token = employeesService.getAllItems();
            }

            protected function button_clickHandler(event:MouseEvent):void
            {
                deleteItemResult.token =
                    employeesService.deleteItem(dg.selectedItem.emp_no);
            }
        ]]>
    </fx:Script>

    <fx:Declarations>
        <s:CallResponder id="getAllItemsResult"/>
        <employeesservice:EmployeesService id="employeesService"
            fault="Alert.show(event.fault.faultString + '\n'
            + event.fault.faultDetail)" showBusyCursor="true"/>
        <s:CallResponder id="deleteItemResult"/>
    </fx:Declarations>
    <mx:DataGrid id="dg" editable="true"
            creationComplete="dg_creationCompleteHandler(event)"dataProvider="{getAllItemsResult.lastResult}">
        <mx:columns>
            <mx:DataGridColumn headerText="emp_no" dataField="emp_no"/>
            <mx:DataGridColumn headerText="last_name" dataField="last_name"/>
            <mx:DataGridColumn headerText="hire_date" dataField="hire_date"/>
        </mx:columns>
    </mx:DataGrid>
    <s:Button label="Delete Item" id="button" click="button_clickHandler(event)"/>

### Event handlers

When you bind a service operation to a user interface component, Flash Builder
generates an event handler for the CallResponder. The event handler manages the
results of the operation. You can also create an event handler in an
ActionScript code block, and reference that event handler from a property of a
user interface component.

Typically, you populate controls such as Lists and DataGrids with data returned
from a service. Flash Builder, by default, generates a creationComplete event
handler for the control that fires immediately after the control is created. For
other controls, Flash Builder generates a handler for the control's default
event. For example, for a Button, Flash Builder generates an event for the
Button's click event.

The control's event property is set to the generated event handler. The
following example shows the generated creation complete event handler for a
DataGrid:

    <fx:Script>
        <![CDATA[
            import mx.events.FlexEvent;
            import mx.controls.Alert;

            protected function dg_creationCompleteHandler(event:FlexEvent):void
            {
                getAllItemsResult.token = employeesService.getAllItems();
            }
        ]]>
    </fx:Script>
    . . .

    <mx:DataGrid id="dg" editable="true"
            creationComplete="dg_creationCompleteHandler(event)"
            dataProvider="{getAllItemsResult.lastResult}">
        <mx:columns>
            <mx:DataGridColumn headerText="emp_no" dataField="emp_no"/>
            <mx:DataGridColumn headerText="last_name" dataField="last_name"/>
            <mx:DataGridColumn headerText="hire_date" dataField="hire_date"/>
        </mx:columns>
    </mx:DataGrid>

You can generate event handlers for controls that respond to user events, such
as Buttons. The following example shows a generated event handler for a Button
that populates a DataGrid:

    <fx:Script>
        <![CDATA[
            import mx.events.FlexEvent;
            import mx.controls.Alert;

            protected function button_clickHandler(event:MouseEvent):void
            {
                deleteItemResult.token =
                    employeesService.deleteItem(dg.selectedItem.emp_no);
            }
        ]]>
    </fx:Script>
    . . .

    <s:Button label="Delete Item" id="button" click="button_clickHandler(event)"/>

### Data binding

When you build a user interface, you bind service operations to controls. See
[Binding service operations to controls](./building-the-client-application.md#binding-service-operations-to-controls).

Flash Builder generates code that binds the data returned from a service
operation to the user interface control that displays the data.

The following example shows the code that Flash Builder generates to populate a
DataGrid control. The `getAllItems()` operation returns a set of employee
records for the custom data type, Employee.

The dataProvider property of the DataGrid is bound to the results stored in the
CallResponder, getAllItemsResult. Flash Builder automatically updates the
DataGrid with DataGridColumns corresponding to each field returned for an
Employee record. The headerText and dataField properties of each column are set
according to the data returned in an Employee record.

    <mx:DataGrid creationComplete="datagrid1_creationCompleteHandler(event)"
        dataProvider="{getAllItemsResult.lastResult}" editable="true">
            <mx:columns>
                <mx:DataGridColumn headerText="gender" dataField="gender"/>
                <mx:DataGridColumn headerText="emp_no" dataField="emp_no"/>
                <mx:DataGridColumn headerText="birth_date" dataField="birth_date"/>
                <mx:DataGridColumn headerText="last_name" dataField="last_name"/>
                <mx:DataGridColumn headerText="hire_date" dataField="hire_date"/>
                <mx:DataGridColumn headerText="first_name" dataField="first_name"/>
            </mx:columns>
        </mx:DataGrid>

## Enabling paging for a service operation

When you enable paging, Flash Builder modifies the implementation of the
generated service. When you populate a data control (such as a DataGrid or a
List) with paged data, Flash Builder determines the number of records visible in
the data control and the total number of records in the database. Flash Builder
provides these values as arguments to the service operation that you used to
implement paging.

You do not have to modify any client application code after paging is enabled.

See
[Enabling paging](./managing-the-access-of-data-from-the-server.md#enabling-paging)
for more information.

## Enabling data management for a service

In Flash Builder, data management is the synchronization of a set of updates to
data on the server. You can enable data management for custom data types
returned from the service. With data management enabled, you can modify one or
more items in a client application without making any updates to the server. You
can then commit all the changes to the server with one operation. You can also
revert the modifications without updating any data on the server.
[Enabling data management](./managing-the-access-of-data-from-the-server.md#enabling-data-management)
shows how to implement this feature.

When you enable data management, Flash Builder modifies the implementation of
the generated service class and the generated class for custom data types. Flash
Builder creates a DataManager to implement this functionality.

### Synchronizing updates to server data

When you call service operations for a managed data type, the changes are
reflected in the client application. However, you can specify that data on the
server is not updated until you call the DataManager's `commit()` method.

When data management is enabled for a service, the service has an autoCommit
flag. By default, autoCommit is false.

The autoCommit flag determines whether to commit changes immediately or to wait
until _`service`_`.commit()` is called.

If autoCommit is false, all updates to the service in the client application are
cached until you call _`service`_`.commit()`. You can call the service's
`revertChanges()` method to discard changes.

If autoCommit is true, then updates are sent to the server immediately. You
cannot call `revertChanges()` to discard changes.

The deleteItemOnRemoveFromFill flag determines whether a deleted item is
immediately removed from the database. If set to true, then the item is not
deleted until _`service`_`.commit()` is called.

The following code disables data management synchronization of updates to server
data. Changes to data for the managed type are updated immediately on the
server.

    bookService.getDataManager(bookService.DATA_MANAGER__BOOK).autoCommit = true;

The following code enables data management synchronization of updates to server
data. Changes to data for the managed type are not updated until `commit()` is
called for the service. Additionally, deleted items are not removed from the
database until `commit()` is called.

    bookService.getDataManager(bookService.DATA_MANAGER__BOOK).autoCommit = false;
    bookService.getDataManager(bookService.DATA_MANAGER__BOOK).deleteItemOnRemoveFromFill= true;

### Reverting changes

The DataManager provides a `revertChanges()` method. The r`evertChanges()`
method restores the data displayed in the client application to the values
retrieved from the server before the last commit call.

Call `revertChanges()` before calling `commit()`to reverts changes to a managed
data type in the client application:

    bookService.getDataManager (bookService.DATA_MANAGER_BOOK).revertChanges();

To commit changes made for the managed data type, call the `commit()` method.

    bookService.getDataManager (employeeService.DATA_MANAGER_EMPLOYEE).commit();

You can also call the `commit()` method directly from the bookService instance.
Calling the commit method directly from the service instance commits all changes
for all managed data types.

    bookService.commit();

Note: You cannot call `revertChanges()` directly from the service instance to
revert changes to all managed data types. You can only call it for a specific
managed data type.

If you want to override the default behavior for data management, and disable
the ability to revert changes, set the autoCommit to true. For example, if you
have an instance of bookService and enabled data management for the Book data
type, set autoCommit to true:

    bookService.getDataManager(bookService.DATA_MANAGER__BOOK).autoCommit = true;

Now, changes made to data for the managed type are updated immediately on the
server.

More Help topics

[Connecting to data services](./connecting-to-data-services.md)
