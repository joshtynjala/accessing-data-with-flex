# Handling service results

After an RPC component calls a service, the data that the service returns is
placed in a lastResult object. By default, the `resultFormat` property value of
HTTPService components and WebService component operations is `object`, and the
data that is returned is represented as a simple tree of ActionScript objects.
Flex interprets the XML data that a web service or HTTP service returns to
appropriately represent base types, such as String, Number, Boolean, and Date.
To work with strongly typed objects, populate those objects by using the object
tree that Flex creates.

WebService and HTTPService components both return anonymous objects and arrays
that are complex types. If `makeObjectsBindable` is `true`, which it is by
default, objects are wrapped in mx.utils.ObjectProxy instances and arrays are
wrapped in mx.collections.ArrayCollection instances.

> **Note:** ColdFusion is not case sensitive, so it internally uppercases all of
> its data. Keep this in mind when consuming a ColdFusion web service.

## Handling result and fault events

When a service call is completed, the RemoteObject method, WebService operation,
or HTTPService component dispatches a result event or a fault event. A _result
event_ indicates that the result is available. A _fault event_ indicates that an
error occurred. The result event acts as a trigger to update properties that are
bound to the lastResult. You can handle fault and result events explicitly by
adding event listeners to RemoteObject methods or WebService operations. For an
HTTPService component, you specify result and fault event listeners on the
component itself because an HTTPService component does not have multiple
operations or methods.

When you do not specify event listeners for result or fault events on a
RemoteObject method or a WebService operation, the events are passed to the
component level; you can specify component-level result and fault event
listeners.

In the following MXML example, the `result` and `fault` events of a WebService
operation specify event listeners; the `fault` event of the WebService component
also specifies an event listener:

    <?xml version="1.0"?>
    <!-- fds\rpc\RPCResultFaultMXML.mxml -->
    <mx:Application xmlns:mx="http://www.adobe.com/2006/mxml">
        <mx:Script>
            <![CDATA[
                import mx.rpc.soap.SOAPFault;
                import mx.rpc.events.ResultEvent;
                import mx.rpc.events.FaultEvent;
                import mx.controls.Alert;
                public function showErrorDialog(event:FaultEvent):void {
                    // Handle operation fault.
                    Alert.show(event.fault.faultString, "Error");
                }
                public function defaultFault(event:FaultEvent):void {
                    // Handle service fault.
                    if (event.fault is SOAPFault) {
                         var fault:SOAPFault=event.fault as SOAPFault;
                         var faultElement:XML=fault.element;
                         // You could use E4X to traverse the raw fault element returned in the SOAP envelope.
                         // ...
                    }
                    Alert.show(event.fault.faultString, "Error");
                }
                public function log(event:ResultEvent):void {
                    // Handle result.
                }
            ]]>
        </mx:Script>
        <mx:WebService id="WeatherService" wsdl="http://myserver:8500/flexapp/app1.cfc?wsdl"
            fault="defaultFault(event)">
            <mx:operation name="GetWeather"
                fault="showErrorDialog(event)"
                result="log(event)">
                <mx:request>
                    <ZipCode>{myZip.text}</ZipCode>
                </mx:request>
            </mx:operation>
        </mx:WebService>
        <mx:TextInput id="myZip"/>
    </mx:Application>

In the following ActionScript example, a result event listener is added to a
WebService operation; a fault event listener is added to the WebService
component:

    <?xml version="1.0"?>
    <!-- fds\rpc\RPCResultFaultAS.mxml -->
    <mx:Application xmlns:mx="http://www.adobe.com/2006/mxml">
        <mx:Script>
            <![CDATA[
                import mx.rpc.soap.WebService;
                import mx.rpc.soap.SOAPFault;
                import mx.rpc.events.ResultEvent;
                import mx.rpc.events.FaultEvent;

                private var ws:WebService;

                public function useWebService(intArg:int, strArg:String):void {
                    ws = new WebService();
                    ws.destination = "wsDest";
                    ws.echoArgs.addEventListener("result", echoResultHandler);
                    ws.addEventListener("fault", faultHandler);
                    ws.loadWSDL();
                    ws.echoArgs(intArg, strArg);
                }

                public function echoResultHandler(event:ResultEvent):void {
                    var retStr:String = event.result.echoStr;
                    var retInt:int = event.result.echoInt;
                    //do something
            }

                public function faultHandler(event:FaultEvent):void {
                //deal with event.fault.faultString, etc.
                    if (event.fault is SOAPFault) {
                         var fault:SOAPFault=event.fault as SOAPFault;
                         var faultElement:XML=fault.element;
                         // You could use E4X to traverse the raw fault element returned in the SOAP envelope.
                         // ...
                    }
                }
            ]]>
        </mx:Script>
    </mx:Application>

You can also use the
[mx.rpc.events.InvokeEvent](https://help.adobe.com/en_US/FlashPlatform/reference/actionscript/3/mx/rpc/events/InvokeEvent.html)
event to indicate when a data access component request has been invoked. This is
useful if operations are queued and invoked at a later time.

## Handling results as XML with the e4x result format

You can set the `resultFormat` property value of HTTPService components and
WebService operations to e4x to create a lastResult property of type XML. You
can access the `lastResult` property by using ECMAScript for XML (E4X)
expressions. You do not include the root node of the XML structure in the dot
notation when using an `E4X` XML object in a binding expression; this is
different from the syntax for a `lastResult` property set to object for which
you do include the root node of the XML structure in the dot notation. For
example, when the lastResult property is set to `e4x`, you would use
`{srv.lastResult.product}`; when the lastResult property is set to object, you
would use `{srv.lastResult.products.product}`.

Using a result format of `e4x` is the preferred way to work directly with XML,
but you can also set the `resultFormat` property to `xml` to create a
`lastResult`object of type flash.xml.XMLNode, which is a legacy object for
working with XML. Also, you can set the `resultFormat` property of HTTPService
components to `flashvars` or `text` to create results as ActionScript objects
that contain name-value pairs or as raw text, respectively.

> **Note:** To use E4X syntax on service results, you must set the
> `resultFormat` property of your HTTPService or WebService component to `e4x`.
> The default value is `object`.

When you set the `resultFormat` property of an HTTPService component or
WebService operation to `e4x`, you may have to handle namespace information
contained in the XML that is returned. For a WebService component, namespace
information is included in the body of the SOAP envelope that the web service
returns. The following example shows part of a SOAP body that contains namespace
information. This data was returned by a web service that gets stock quotes. The
namespace information is in bold text.

    ...
    <soap:Body>
    <GetQuoteResponse
    xmlns="http://ws.invesbot.com/">
    <GetQuoteResult><StockQuote xmlns="">
    <Symbol>ADBE</Symbol>
    <Company>ADOBE SYSTEMS INC</Company>
    <Price>&lt;big&gt;&lt;b&gt;35.90&lt;/b&gt;&lt;/big&gt;</Price>
    ...
    </soap:Body>
    ...

Because this soap:Body contains namespace information, if you set the
`resultFormat` property of the WebService operation to `e4x`, create a namespace
object for the http://ws.invesbot.com/namespace. The following example shows an
application that does this:

    <?xml version="1.0"?>
    <!-- fds\rpc\WebServiceE4XResult1.mxml -->
    <mx:Application xmlns:mx="http://www.adobe.com/2006/mxml" xmlns="*"
        pageTitle="Test" >
        <mx:Script>
            <![CDATA[
                import mx.controls.Alert;
                private namespace invesbot = "http://ws.invesbot.com/";
                use namespace invesbot;
            ]]>
        </mx:Script>
        <mx:WebService
            id="WS"
            destination="stockservice" useProxy="true"
            fault="Alert.show(event.fault.faultString), 'Error'">
            <mx:operation name="GetQuote" resultFormat="e4x">
                <mx:request>
                    <symbol>ADBE</symbol>
                </mx:request>
            </mx:operation>
        </mx:WebService>
        <mx:HBox>
            <mx:Button label="Get Quote" click="WS.GetQuote.send()"/>
            <mx:Text
                text="{WS.GetQuote.lastResult.GetQuoteResult.StockQuote.Price}"
            />
        </mx:HBox>
    </mx:Application>

Optionally, you can create a var for a namespace and access it in a binding to
the service result, as the following example shows:

    <?xml version="1.0"?>
    <!-- fds\rpc\WebServiceE4XResult2.mxml -->
    <mx:Application xmlns:mx="http://www.adobe.com/2006/mxml" xmlns="*"
        pageTitle="Test" >
        <mx:Script>
            <![CDATA[
            import mx.controls.Alert;
            public var invesbot:Namespace =
                new Namespace("http://ws.invesbot.com/");
            ]]>
        </mx:Script>
        <mx:WebService
            id="WS"
            destination="stockservice" useProxy="true"
            fault="Alert.show(event.fault.faultString), 'Error'">
            <mx:operation name="GetQuote" resultFormat="e4x">
                <mx:request>
                    <symbol>ADBE</symbol>
                </mx:request>
            </mx:operation>
        </mx:WebService>
        <mx:HBox>
            <mx:Button label="Get Quote" click="WS.GetQuote.send()"/>
            <mx:Text
              text="{WS.GetQuote.lastResult.invesbot::GetQuoteResult.StockQuote.Price}"
            />
        </mx:HBox>
    </mx:Application>

You use E4X syntax to access elements and attributes of the XML that is returned
in a lastResult object. You use different syntax, depending on whether a
namespace or namespaces are declared in the XML.

#### No namespace

The following example shows how to get an element or attribute value when no
namespace is specified on the element or attribute:

    var attributes:XMLList = XML(event.result).Description.value;

The previous code returns `xxx` for the following XML document:

    <RDF xmlns="http://www.w3.org/1999/02/22-rdf-syntax-ns#">
        <Description>
            <value>xxx</value>
        </Description>
    </RDF>

#### Any namespace

The following example shows how to get an element or attribute value when any
namespace is specified on the element or attribute:

    var attributes:XMLList = XML(event.result).*::Description.*::value;

The previous code returns `xxx` for either one of the following XML documents:

XML document one:

    <rdf:RDF xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#">
        <rdf:Description>
            <rdf:value>xxx</rdf:value>
        </rdf:Description>
    </rdf:RDF>

XML document two:

    <rdf:RDF xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#" xmlns:cm="http://www.w3.org/1999/02/22-rdf-syntax-ns#">
        <cm:Description>
            <rdf:value>xxx</rdf:value>
        </cm:Description>
    </rdf:RDF>

#### Specific namespace

The following example shows how to get an element or attribute value when the
declared rdf namespace is specified on the element or attribute:

    var rdf:Namespace = new Namespace("http://www.w3.org/1999/02/22-rdf-syntax-ns#");
    var attributes:XMLList = XML(event.result).rdf::Description.rdf::value;

The previous code returns `xxx` for the following XML document:

    <rdf:RDF xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#">
        <rdf:Description>
            <rdf:value>xxx</rdf:value>
        </rdf:Description>
    </rdf:RDF>

The following example shows an alternate way to get an element or attribute
value when the declared rdf namespace is specified on an element or attribute:

    namespace rdf = "http://www.w3.org/1999/02/22-rdf-syntax-ns#";
    use namespace rdf;
    var attributes:XMLList = XML(event.result).rdf::Description.rdf::value;

The previous code also returns `xxx` for the following XML document:

    <rdf:RDF xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#">
        <rdf:Description>
            <rdf:value>xxx</rdf:value>
        </rdf:Description>
    </rdf:RDF>

## Handling web service results that contain .NET DataSets or DataTables

Web services written with the Microsoft .NET Framework can return special .NET
DataSet or DataTable objects to the client. A .NET web service provides a basic
WSDL document without information about the type of data that it manipulates.
When the web service returns a DataSet or a DataTable, data type information is
embedded in an XML Schema element in the SOAP message, which specifies how the
rest of the message must be processed. To best handle results from this type of
web service, you set the `resultFormat` property of a Flex WebService operation
to `object`. You can optionally set the WebService operation's `resultFormat`
property to `e4x`, but the XML and e4x formats are inconvenient because you must
navigate through the unusual structure of the response and implement workarounds
if you want to bind the data, for example, to a DataGrid control.

When you set the `resultFormat` property of a Flex WebService operation to
`object`, a DataTable or DataSet returned from a .NET web service is
automatically converted to an object with a Tables property, which contains a
map of one or more DataTable objects. Each DataTable object from the Tables map
contains two properties: Columns and Rows. The Rows property contains the data.
The event.result object gets the following properties corresponding to DataSet
and DataTable properties in .NET. Arrays of DataSets or DataTables have the same
structures described here, but are nested in a top-level Array on the result
object.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<thead>
<tr class="header">
<th><p>Property</p></th>
<th><p>Description</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><div>
<pre><code>result.Tables</code></pre>
</div></td>
<td><p>Map of table names to objects that contain table data.</p></td>
</tr>
<tr class="even">
<td><div>
<pre><code>result.Tables[&quot;someTable&quot;].Columns</code></pre>
</div></td>
<td><p>Array of column names in the order specified in the DataSet or
DataTable schema for the table.</p></td>
</tr>
<tr class="odd">
<td><div>
<pre><code>result.Tables[&quot;someTable&quot;].Rows</code></pre>
</div></td>
<td><p>Array of objects that represent the data of each table row. For
example, {columnName1:value, columnName2:value,
columnName3:value}.</p></td>
</tr>
</tbody>
</table>

The following MXML application populates a DataGrid control with DataTable data
returned from a .NET web service.

    <?xml version="1.0" encoding="utf-8"?>
    <mx:Application xmlns="*" xmlns:mx="http://www.adobe.com/2006/mxml" layout="vertical">
        <mx:WebService
            id="nwCL"
            wsdl="http://localhost/data/CustomerList.asmx?wsdl"
            result="onResult(event)"
            fault="onFault(event)" />
        <mx:Button label="Get Single DataTable" click="nwCL.getSingleDataTable()"/>
        <mx:Button label="Get MultiTable DataSet" click="nwCL.getMultiTableDataSet()"/>
        <mx:Panel id="dataPanel" width="100%" height="100%" title="Data Tables"/>

        <mx:Script>
            <![CDATA[
                import mx.controls.Alert;
                import mx.controls.DataGrid;
                import mx.rpc.events.FaultEvent;
                import mx.rpc.events.ResultEvent;

                private function onResult(event:ResultEvent):void {
                    // A DataTable or DataSet returned from a .NET webservice is
                    // automatically converted to an object with a "Tables" property,
                    // which contains a map of one or more dataTables.
                    if (event.result.Tables != null)
                    {
                        // clean up panel from previous calls.
                        dataPanel.removeAllChildren();

                        for each (var table:Object in event.result.Tables)
                        {
                            displayTable(table);
                        }

                        // Alternatively, if a table's name is known beforehand,
                        // it can be accessed using this syntax:
                        var namedTable:Object = event.result.Tables.Customers;
                        //displayTable(namedTable);
                    }
                }

                private function displayTable(tbl:Object):void {
                    var dg:DataGrid = new DataGrid();
                    dataPanel.addChild(dg);
                    // Each table object from the "Tables" map contains two properties:
                    // "Columns" and "Rows". "Rows" is where the data is, so we can set
                    // that as the dataProvider for a DataGrid.
                    dg.dataProvider = tbl.Rows;
                }

                private function onFault(event:FaultEvent):void {
                    Alert.show(event.fault.toString());
                }
            ]]>
        </mx:Script>

    </mx:Application>

The following example shows the .NET C# class that is the backend web service
implementation called by the application; this class uses the Microsoft SQL
Server Northwind sample database:

:

    <%@ WebService Language="C#" Class="CustomerList" %>
    using System.Web;
    using System.Web.Services;
    using System.Web.Services.Protocols;
    using System.Web.Services.Description;
    using System.Data;
    using System.Data.SqlClient;
    using System;

    public class CustomerList : WebService {
        [WebMethod]
        public DataTable getSingleDataTable() {
            string cnStr = "[Your_Database_Connection_String]";
            string query = "SELECT TOP 10 * FROM Customers";
            SqlConnection cn = new SqlConnection(cnStr);
            cn.Open();
            SqlDataAdapter adpt = new SqlDataAdapter(new SqlCommand(query, cn));
            DataTable dt = new DataTable("Customers");

            adpt.Fill(dt);
            return dt;
        }

        [WebMethod]
        public DataSet getMultiTableDataSet() {
            string cnStr = "[Your_Database_Connection_String]";
            string query1 = "SELECT TOP 10 CustomerID, CompanyName FROM Customers";
            string query2 = "SELECT TOP 10 OrderID, CustomerID, ShipCity,
            ShipCountry FROM Orders";
            SqlConnection cn = new SqlConnection(cnStr);
            cn.Open();

            SqlDataAdapter adpt = new SqlDataAdapter(new SqlCommand(query1, cn));
            DataSet ds = new DataSet("TwoTableDataSet");
            adpt.Fill(ds, "Customers");

            adpt.SelectCommand = new SqlCommand(query2, cn);
            adpt.Fill(ds, "Orders");

            return ds;
        }
    }
