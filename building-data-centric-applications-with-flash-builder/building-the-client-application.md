# Building the client application

You use the MXML code editor to create a user interface.

After defining the components for the application using the code editor, you can
bind data returned from the service to user interface components. Generate event
handlers as needed for user interaction with the application.

You can also generate a Form from service operations that are available in the
Data/Services view.

## Binding service operations to controls

Use the Bind To Data dialog to bind a service operation to a user interface
component.

The Bind to Data dialog is available from the Data menu in toolbar in the
Data/Services view.

When you bind a service operation to a component, Flash Builder generates MXML
and ActionScript code to access the service operation from the client
application.

### Return types for service operations

When binding a service operation to a control, Flash Builder uses the data type
of the data returned by the operation. Often, you configure the return type for
a service operation before binding it to a component.

If the return type for a service operation has not been configured, the Bind to
Data dialog prompts you to complete this step.

See
[Configuring data types for data service operations](./configuring-data-types-for-data-service-operations.md).

### Bind a DataGrid control to a service operation (Bind to Data dialog)

This procedure assumes that you have connected to a data service.

1.  In the Outline view, select the DataGrid control. Or, place your cursor
    within the \<s:DataGrid\> tag in the MXML editor.

2.  With the DataGrid selected, open the Bind to Data dialog by selecting Bind
    to Data from the Flash Builder Data menu.

3.  Select New Service Call, then select a Service and Operation.

    If you previously bound a service operation to a component, you can use
    those results. In this case, specify Existing Call Result and select the
    operation to use.

4.  (Optional) Select Change Return Type:

    Select Change Return Type if you want to reconfigure the return type for the
    service operation.

    If the return type for the operation has not been previously configured,
    select Configure Return Type.

    See
    [Configuring data types for data service operations](./configuring-data-types-for-data-service-operations.md).

5.  Click OK.

    The DataGrid component changes to show the fields retrieved from the
    database.

    See
    [Configure DataGrid and AdvancedDataGrid components](https://web.archive.org/web/20160115171050mp_/http://help.adobe.com/en_US/flashbuilder/using/WSe4e4b720da9dedb5-1a92eab212e75b9d8b2-7fa1.html).

6.  Save and run the application.

## Generating a service call to an operation

Flash Builder can generate an ActionScript method that calls an operation of a
service. The method is not bound to a user interface component, but is available
to use in your application code.

In addition to generating the ActionScript method, Flash Builder creates a
CallResponder that provides access to the data returned from the service call.
See
[Call Responder](./flash-builder-code-generation-for-client-applications.md#call-responder).

### Generate a service call to an operation

This procedure assumes that you have connected to a data service.

1.  In Data/Services view, select an operation.

2.  From the context menu for the operation, select Generate Service Call.

    Flash Builder generates a method for calling the operation and displays the
    generated method in Source mode of the MXML editor. Flash Builder creates a
    CallResponder to hold the results of the service call.

    This option is also available from the Data/Services toolbar.

## Generating a Form for an application

Forms are one of the most common methods that web applications use to collect
information from users. Flash Builder can generate forms for data retrieved from
service calls or for custom data types used to access remote data.

When generating a form, Flash Builder creates a Form layout container and adds
components to display or edit the specific data retrieved from the service.

Flash Builder generates the following types of forms.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<thead>
<tr class="header">
<th><p>Form</p></th>
<th><p>Description</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>Data type</p></td>
<td><p>Contains components representing the fields of a data
type.</p></td>
</tr>
<tr class="even">
<td><p>Master-detail Form</p></td>
<td><p>The "master" component is typically a data control listing data
retrieved from a service.</p>
<p>The "detail" form represents individual items selected in the master
component.</p></td>
</tr>
<tr class="odd">
<td><p>Service call</p></td>
<td><p>Create two forms. One form specifies the inputs to an operation.
The other form displays the returned data.</p></td>
</tr>
</tbody>
</table>

When generating a form, specify which fields to include, the type of user
interface control to represent each field, and whether to make the form
editable.

### Generating a form

This procedure shows how to generate a form for a service call. The procedures
for generating other types of forms are similar.

1.  To run the Generate Form wizard, fom the Data/Services view, select an
    operation. Then, do one of the following:

    - From the context menu for the operation, select Generate Form.

    - From the Flash Builder Data menu, select Generate Form.

2.  In the Generate Form wizard, select Generate Form for Service Call.

3.  Select New Service Call or Existing Call Result.

    Specify Existing Call Result to use the code previously generated for a
    service call.

    Otherwise, specify New Service Call and select a Service and Operation for
    the Form.

4.  (Optional) Depending on the operation, you have several options on the Form
    that is generated.

    If the Operation accepts parameters, then you can choose to include a form
    for the parameters.

    If the Operation returns a value, then you can choose to include a form for
    the return value.

    You can choose whether to make the form editable or read-only.

5.  (Optional) Configure the Input Types or Return Types.

    If the operation you select has input parameters or returns a value, you can
    configure the input type or return types.

    Configure the input types and return type for the operation before you can
    generate the form. If you previously configured these types, you have the
    option to configure them again.

    See
    [Configuring data types for data service operations](./configuring-data-types-for-data-service-operations.md).

6.  Click Next. In the Property Control Mapping dialog, select which fields to
    include in the form and the type of control to represent the data.

7.  Click Finish.

When Flash Builder generates Forms, one Form can be placed on top of another
Form. To rearrange the generated Forms, make sure that you select and move a
Form, and not a component of the Form.

![](../img/tip_help.png) When Flash Builder places one Form on top of another,
it can be confusing on how to select the Form. In the code editor, select the
tag for one of the Forms.

### Generating a master-detail form

To create a master-detail form, first add a data control component to the
application and bind the results of an operation to the control.

For example, add a DataGrid component and bind the results of an operation such
as `getItems_paged()` to the DataGrid.

1.  In the Outline view, select a data control, such as a DataGrid.

2.  From the Data menu, select Generate Details Form.

3.  Continue generating the Form, as described in Generating a Form.

### Generating a form for a data type

To generate a form with components representing the fields of a custom data
type, first configure the data type. See
[Configuring data types for data service operations](./configuring-data-types-for-data-service-operations.md).

1.  In the Data/Services view, select a custom data type.

2.  From the context menu, select Generate Form.

3.  Make sure that Generate Form for Data Type is selected.and choose a Data
    Type.

4.  (Optional) You can choose whether to make the form editable.

5.  Click Finish.

## Generating event handlers to retrieve remote data

When you bind a data service operation to a component, Flash Builder generates
an event handler that retrieves data from the service to populate the component.

For example, if you bind an operation such as `getAllItems()` to a DataGrid,
Flash Builder generates a `creationComplete` event handler. The DataGrid
references the generated event handler. The results of the call become the data
provider for the DataGrid.

    . . .
    protected function dataGrid_creationCompleteHandler(event:FlexEvent):void
    {
        getAllItemsResult.token = productService.getAllItems();
    }
    . . .
    <mx:DataGrid creationComplete="dataGrid_creationCompleteHandler(event)" dataProvider="{getAllItemsResult.lastResult}">
    . . .
    </mx:DataGrid>
    . . .

When you run the application, after the DataGrid has been created the event
handler populates the DataGrid with data retrieved from the service.

When generating event handlers, you can accept the generated handlers or replace
them with others according to your needs. For example, you can replace the
`creationComplete` event handler on the DataGrid with a `creationComplete`
handler on the Application.

You can also generate or create event handlers for controls that accept user
input, such as Buttons or Text.

### Generate an event handler for a user interface component

1.  Create an application that contains a user interface component, such as a
    DataGrid or a Button.

2.  Flash Builder provides Content Assist to help you generate the event
    handler. Press Control+Space or Cmd+Space ( Mac) and select Generate Event
    Handler.

3.  Flash Builder generates a unique name for the event handler and places the
    event handler in the Script block.

    Flash Builder highlights the generated stub for the event handler in the
    code editor. Fill in the remaining code for the event handler. Use Content
    Assist to help you code the event handler.

More Help topics

![](../img/flexLinkIndicator.png) 
[MX Form, FormHeading, and FormItem layout containers](https://web.archive.org/web/20160115171050mp_/http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf69084-7e53.html)

![](../img/flashbuilderLinkIndicator.png) 
[Generate event handlers](https://web.archive.org/web/20160115171050mp_/http://help.adobe.com/en_US/flashbuilder/using/WSe4e4b720da9dedb5-1a92eab212e75b9d8b2-7fa0.html)
