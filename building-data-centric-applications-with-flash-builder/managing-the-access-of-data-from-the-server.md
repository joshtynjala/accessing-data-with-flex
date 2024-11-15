# Managing the access of data from the server

Paging  
Paging is the incremental retrieval of large data sets from a remote service.

For example, suppose you want to access a database that has 10,000 records and
then display the data in a DataGrid that has 20 rows. You can implement a paging
operation to fetch the rows in 20 set increments. When the user requests
additional data (scrolling in the DataGrid), the next page of records is fetched
and displayed.

Data management  
In Flash Builder, data management is the synchronization of updates to data on
the server from the client application. Using data management, you can modify
one or more items in a client application without making any updates to the
server. You then commit all the changes to the server with one operation. You
can also revert the modifications without updating any data.

Data management involves coordinating several operations (create, get, update,
delete) to respond to events from the client application, such as updating an
Employee record.

When enabling data management with Flash Builder, Flash Builder also generates
code that automatically updates user interface components. For example, Flash
Builder generates code to keep DataGrids synchronized with data on the server.

## Enabling paging

You can enable paging for a data service that implements a paging function with
the following signature:

    getItems_paged(startIndex:Number, numItems:Number): myDataType

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td><p><strong>function name</strong></p></td>
<td><p>You can use any valid name for the function.</p></td>
</tr>
<tr class="even">
<td><p><strong>startIndex</strong></p></td>
<td><p>The initial row of data to retrieve.</p>
<p>Define the data type for startIndex as Number in the client
operation.</p></td>
</tr>
<tr class="odd">
<td><p><strong>numItems</strong></p></td>
<td><p>The number of rows of data to retrieve in each page.</p>
<p>Define the data type for numItems as Number in the client
operation.</p></td>
</tr>
<tr class="even">
<td><p><em><strong>myDataType</strong></em></p></td>
<td><p>The data type returned by the data service.</p></td>
</tr>
</tbody>
</table>

When implementing paging from a service, you can also implement a
`count()`operation. A `count()`operation returns the number of items returned
from the service. Flash Builder requires that the `count()`operation implement
the following signature:

    count(): Number

|                   |                                                     |
| ----------------- | --------------------------------------------------- |
| **function name** | You can use any valid name for the function.        |
| **Number**        | The number of records retrieved from the operation. |

Flex uses the count operation to properly display user interface components that
retrieve large data sets. For example, the `count()` operation helps determine
the thumb size for a scroll bar of a DataGrid.

Some remote services do not provide a `count()`operation. Paging still works,
but the control displaying the paged data does not properly represent the size
of the data set.

#### Paging operations for filtered queries

You can enable paging for queries that filter results from the database. When
filtering results in the query, use these signatures for the paging and count
functions.

    getItems_pagedFiltered(filterParam1:String, filterParam2:String, startIndex:Number, numItems:Number): myDataType


    countFiltered(filterParam1:String, filterParam2:String)

|              |                                                                                                        |
| ------------ | ------------------------------------------------------------------------------------------------------ |
| filterParam1 | Optional filter parameter. This parameter is the same in getItems_PagedFiltered() and countFiltered(). |
| filterParam2 | Optional filter parameter. This parameter is the same in getItems_PagedFiltered() and countFiltered(). |

Here is a code snippet of a `getItems_pagedFiltered()` function that is
implemented in PHP to access a MySQL database. The code snippet shows how to use
the optional filter parameter.

    get_Items_paged($expression, $startIndex, $numItems) {
        . . .
        SELECT * from employees where name LIKE $expression LIMIT $startIndex, $numItems;
        . . .
    }

#### Enable paging for an operation

This procedure assumes that you have coded both `getItems_paged()`and `count()`
operations in your remote service. It also assumes that you have configured the
return data type for the operation, as explained in
[Configuring data types for data service operations](./configuring-data-types-for-data-service-operations.md).

1.  In the Data/Services view, from the context menu for the `getItems_paged()`
    operation, select Enable Paging.

2.  If you have not previously identified a unique key for your data type,
    specify the attributes that uniquely identify an instance of this data type.
    Click Next.

    Typically, this attribute is the primary key.

3.  (Optional) Specify the number of items to fetch to define a custom page
    size.

    If you do not specify a custom page size, a default page size is set at the
    service level. The default page size is 20 records per page.

4.  (Optional) Specify the `count()` operation. Click Finish.

    The `count()` operation allows Flash Builder to properly display user
    interface elements, such as the thumb size for a scroll bar.

    Paging is now enabled for that operation.

    In Data/Services View, the signature of the function that implements paging
    no longer includes the startIndex and numItems parameters. Flash Builder now
    dynamically adds these values. Flash Builder determines these values based
    on a custom page size you provided or the default page size of 20 records
    per page.

## Enabling data management

To enable data management for a service, the service implements one or more of
the following functions. The Data management feature uses these functions to
synchronize updates to data on the remote server:

- Add (createItem)

      createItem(item: myDatatype):int
      createItem(item: myDatatype):String
      createItem(item: myDatatype):myDataType

  The return type for createItem() is the type of the primary key of the
  database.

- Get All Properties (getItem)

      getItem(itemID:Number): myDatatype

- Update (updateItem)

      updateItem((item: myDataType):void
      updateItem((item: myDataType, originalItem: myDataType):void
      updateItem((item: myDataType, originalItem: myDataType, changes: String[]):void

- Delete (deleteItem)

      deleteItem(itemID:Number):void

Flash Builder requires these functions to have the following signatures:

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td><p><strong>function name</strong></p></td>
<td><p>You can use any valid name for the function.</p></td>
</tr>
<tr class="even">
<td><p><strong>item</strong></p>
<p><strong>originalItem</strong></p></td>
<td><p>An item of the data type returned by the data service.</p></td>
</tr>
<tr class="odd">
<td><p><strong>itemID</strong></p></td>
<td><p>A unique identifier for the item, usually the primary key in the
database.</p></td>
</tr>
<tr class="even">
<td><p><strong>changes[]</strong></p></td>
<td><p>An array corresponding to fields in a specified Item. This
argument is only used in one version of
<samp>updateItem()</samp>.</p></td>
</tr>
<tr class="odd">
<td><p><em><strong>myDataType</strong></em></p></td>
<td><p>The data type of the item available from the data service.
Typically, you define a custom data type when retrieving data from a
service.</p></td>
</tr>
</tbody>
</table>

### autoCommit flag

Data management allows you to synchronize updates to data on the server. Changes
to data made in the client application are not updated on the server until you
call the _`service`_`.commit()` method.

However, if you want to disable this feature, set the autoCommit flag to true.
If autoCommit is true, then updates to server data are not cached, but are made
immediately. See
[Enabling data management for a service](./flash-builder-code-generation-for-client-applications.md#enabling-data-management-for-a-service).

### deleteItemOnRemoveFromFill flag

When you delete items with data management enabled, use the
deleteItemOnRemoveFromFill flag. By default, this flag is set to true. When you
delete an item, the item is immediately removed from the database.

Set deleteItemOnRemoveFromFill to false to defer the deletion until the commit()
method is called. The following example shows the code for a creation complete
event handler for a DataGrid. If the user deletes a selected Employee item in
the DataGrid, the selected item is not removed from the database until the
commit() method is called.

    protected function dg_creationCompleteHandler(event:FlexEvent):void
    {
        employeeService.      getDataManager(employeeService.DATA_MANAGER_EMPLOYEE).autoCommit=false;
        employeeService.getDataManager(  e m p l oyeeService.DATA_MANAGER_EMPLOYEE).deleteItemOnRemoveFromFill=      true;
        getAllEmployeesResult.token = employeeService.getAllEmployees();
    }

### Enable data management for an operation

This procedure assumes that you have implemented the required operations in your
remote service. It also assumes that you have configured the return data type
for the operations that use a custom data type. See
[Configuring data types for data service operations](./configuring-data-types-for-data-service-operations.md).

1.  In the Data/Services view, expand the Data Types node.

2.  From the context menu for a data type, select Enable Data Management.

3.  If you have not previously identified a unique key for your data type,
    specify the attributes that uniquely identify an instance of this data type.
    Click Next.

    Typically, this attribute is the primary key.

4.  Specify the Add, Get All Properties, Update, and Delete operations that you
    have implemented. Click Finish.

    > **Note:** You do not have to implement all of these functions. Implement
    > only those functions required for your application.

Data management is now enabled for that operation.
