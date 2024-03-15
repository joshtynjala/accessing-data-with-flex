# Testing service operations

You can use Flash Builder to test service operations and view data returned from
an operation. This feature is useful to verify the behavior of services.

Important: Some operations, such as update and delete, modify data on the
server.

## Test a service operation

This procedure assumes you connected to a data service.

1.  In the Data/Service view, select an operation in a service. From the context
    menus, select Test Operation.

    The Test Operation view opens, displaying the selected operation. If the
    operation requires input parameters, the Test Operation view lists the
    parameters.

2.  For any required input parameters, click the Enter Value field and specify a
    value for the parameter.

    If the parameter requires a complex type, click the Ellipsis button in the
    field to open an Input Argument Editor. Specify the value in editor.

    The Input Argument Editor accepts JSON notation for representing complex
    types in ActionScript.

3.  If authentication is needed from the server, select Authentication Required.
    Click Test.

    Provide authentication credentials as needed. See
    [Authenticating access to services](./configuring-data-types-for-data-service-operations.md#authenticating-access-to-services).

    Flash Builder displays the data returned from the service.

4.  (Optional) In the Test Operation view, select additional services and
    operations that are available to test.
