# Debugging remote services

There are several ways to debug applications that access remote services.

- Flash Builder Test Operation view

  The Flash Builder Test Operation view allows you to call service operations
  and view the returned data. The Test Operation view displays any error
  messages displayed by the service.

- Server-side scripts

  For additional debugging of services, you can write scripts that test server
  code and write output stream information to log files.

- Flash Builder Network Monitor

  Use the Network Monitor after building an application with Flash Builder that
  accesses a service. Use the Network Monitor to view data sent between the
  server and client.

<!-- -->

## Flash Builder Test Operation view

Use the Flash Builder Test Operation view to call operations from a service and
view the results of the operation. Results include any error messages returned
from the service.

You can use the Test Operation view to view data returned from operations on
services you write or services available from HTTP or web services.

### Test a service operation

This procedure assumes that you have written a service that you are testing or
have access to an HTTP or web service.

1.  In the Flash Builder Data/Services view, navigate to a service operation you
    want to test.

2.  From the context menu for the service operation, select Test Operation.

3.  (Optional) In the Test Operation view, select Authentication Required to
    provide login credentials to the service.

4.  If the operation takes parameters, click the Enter Value field to provide a
    value for the parameter.

    If the parameter requires a complex type, click the Ellipsis button in the
    Enter Value field to open an editor that accepts JSON notation. Provide the
    value for the parameter using JSON notation.

5.  Click Test to view the results of the operation.

## Scripts to test server code

Use test scripts to view and debug server code before attempting to connect to
the server in Flash Builder. Test scripts provide the following benefits:

- You can view test results from a web browser.

  As you modify the code, simply refresh the browser page to view the results.

- You can echo or print results to the output stream, which you cannot do
  directly from AMF.

- Error displays are nicely formatted and typically more complete than errors
  captured using AMF.

### ColdFusion Scripts

Use the following script, `tester.cfm`, to dump a call to a function.

    <!--- tester.cfm --->
    <cfobject component="EmployeeService" name="o"/>
    <cfdump var="#o.getAllItems()#">

In `tester2.cfm`, you specify the method and arguments to call in the URL.

    <!--- tester2.cfm --->
    <cfdump var="#url#">

    <cfinvoke component="#url.cfc#" method="#url.method#" argumentCollection="#url#" returnVariable="r">

    <p>Result:

    <cfif isDefined("r")>
        <cfdump var="#r#">
    <cfelse>
        (no result)
    </cfif>

For example, call the `getItemID()` method in EmployeeService with the following
URL:

    http://localhost/tester2.cfm?EmployeeService&method=getItemId&id=12

`tester3.cfm` writes a log that records calls to operations and dumps the input
arguments using `cfdump`.

    <!--- tester3.cfm --->
    <cfsavecontent variable="d"><cfdump var="#arguments#"></cfsavecontent>

    <cffile action="append"
    file="#getDirectoryFromPath(getCurrentTemplatePath())#MyServiceLog.htm"
    output="<p>#now()# operationName #d#">

#### PHP Scripts

Use the following script, `tester.php`, to dump a call to a function.

    <pre>
    <?php
    include('MyService.php');
        $o = new MyService();
        var_dump($o->getAllItems());
    ?>
    </pre>

Add the following code to your PHP service to log messages during code
execution.

    $message = 'updateItem: '.$item["id"];
    $log_file = '/Users/me/Desktop/myservice.log';
    error_log(date('d/m/Y H:i:s').' '.$message.PHP_EOL, 3, $log_file);

Add the following code to your PHP service to enable dumping to a log file:

    ob_start();
    var_dump($item);
    $result = ob_get_contents();
    ob_end_clean();

    $message = 'updateItem: '.$result;
    $log_file = '/Users/me/Desktop/myservice.log';
    error_log(date('d/m/Y H:i:s').' '.$message.PHP_EOL, 3, $log_file);

## Network Monitor

The Network Monitor is available in Flash Builder from the Flex Debugging
Perspective. Enable the monitor before you can use it to monitor data. See
[Monitor applications that access data services](https://web.archive.org/web/20121114023209mp_/http://help.adobe.com/en_US/flashbuilder/using/WSe4e4b720da9dedb5-1a92eab212e75b9d8b2-7f7a.html)for
details about enabling and using the Network Monitor.
