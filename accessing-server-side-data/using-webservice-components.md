# Using WebService components

Applications created with the Flex framework can interact with SOAP-based web
services that define their interfaces in a Web Services Description Language 1.1
(WSDL 1.1) document, which is available as a URL. WSDL is a standard format for
describing the messages that a web service understands, the format of its
responses to those messages, the protocols that the web service supports, and
where to send messages. The Flex web service API generally supports Simple
Object Access Protocol (SOAP) 1.1, XML Schema 1.0 (versions 1999, 2000, and
2001), and WSDL 1.1 RPC-encoded, RPC-literal, and document-literal (bare and
wrapped style parameters). The two most common types of web services use remote
procedure call (RPC) encoded or document-literal SOAP bindings; the terms
_encoded_ and _literal_ indicate the type of WSDL-to-SOAP mapping that a service
uses.

Flex supports web service requests and results that are formatted as SOAP
messages. SOAP provides the definition of the XML-based format that you can use
for exchanging structured and typed information between a web service client,
such as an application built with Flex, and a web service.

Adobe® Flash® Player operates within a security sandbox that limits what
applications built with Flex and other applications built with Flash can access
over HTTP. Applications built with Flash are allowed HTTP access only to
resources on the same domain and by the same protocol from which they were
served. This presents a problem for web services, because they are typically
accessed from remote locations. The proxy service, available in LiveCycle Data
Services and BlazeDS, intercepts requests to remote web services, redirects the
requests, and then returns the responses to the client.

If you are not using LiveCycle Data Services or BlazeDS, you can access web
services in the same domain as your application; or a crossdomain.xml
(cross-domain policy) file that allows access from your application's domain
must be installed on the web server hosting the RPC service.

For API reference information about the WebService component, see
[mx.rpc.soap.mxml.WebService](https://help.adobe.com/en_US/FlashPlatform/reference/actionscript/3/mx/rpc/soap/mxml/WebService.html).

#### Sample WebService application

The following sample code is for an application that uses a WebService component
to call web service operations.

#### MXML code

The application in the following example calls a web service that queries a SQL
database table called users and returns data to the application, where it is
bound to the `dataProvider` property of a DataGrid control and displayed in the
DataGrid control. The application also sends the user name and e-mail address of
new users to the web service, which performs an insert into the user database
table. The back-end implementation of the web service is a ColdFusion component;
the same ColdFusion component is accessed as a remote object in
[Using RemoteObject components](./using-remoteobject-components.md).

    <?xml version="1.0" encoding="utf-8"?>
    <s:Application xmlns:fx="http://ns.adobe.com/mxml/2009"
            xmlns:s="library://ns.adobe.com/flex/spark"
            xmlns:mx="library://ns.adobe.com/flex/mx" minWidth="955" minHeight="600">
        <fx:Declarations>
            <s:WebService
                id="userRequest"
                wsdl="http://localhost:8500/flexapp/returnusers.cfc?wsdl">

                <mx:operation name="returnRecords" resultFormat="object"
                              fault="mx.controls.Alert.show(event.fault.faultString)"
                              result="remotingCFCHandler(event)"/>

                <mx:operation name="insertRecord" result="insertCFCHandler()"
                              fault="mx.controls.Alert.show(event.fault.faultString)"/>
            </s:WebService>
        </fx:Declarations>
        <fx:Script>
            <![CDATA[
                import mx.rpc.events.ResultEvent;

                private function remotingCFCHandler(e:ResultEvent):void
                {
                    dgUserRequest.dataProvider = e.result;
                }

                private function insertCFCHandler():void
                {
                    userRequest.returnRecords();
                }
                private function clickHandler():void
                {
                    userRequest.insertRecord(username.text, emailaddress.text);
                }
            ]]>
        </fx:Script>

        <mx:Form x="22" y="10" width="300">
            <mx:FormItem>
                <s:Label text="Username" />
                <s:TextInput id="username"/>
            </mx:FormItem>
            <mx:FormItem>
                <s:Label text="Email Address" />
                <s:TextInput id="emailaddress"/>
            </mx:FormItem>
            <s:Button label="Submit" click="clickHandler()"/>
        </mx:Form>

        <mx:DataGrid id="dgUserRequest" x="22" y="160">
            <mx:columns>
                <mx:DataGridColumn headerText="User ID" dataField="USERID"/>
                <mx:DataGridColumn headerText="User Name" dataField="USERNAME"/>
            </mx:columns>
        </mx:DataGrid>

        <s:TextInput x="22" y="320" id="selectedemailaddress" text="{dgUserRequest.selectedItem.emailaddress}"/>
    </s:Application>

#### WSDL document

The following example shows the WSDL document that defines the API of the web
service:

    <?xml version="1.0" encoding="UTF-8"?>
    <wsdl:definitions targetNamespace="http://flexapp"
        xmlns:apachesoap="http://xml.apache.org/xml-soap"
        xmlns:impl="http://flexapp" xmlns:intf="http://flexapp"
        xmlns:soapenc="http://schemas.xmlsoap.org/soap/encoding/"
        xmlns:tns1="http://rpc.xml.coldfusion"
        xmlns:wsdl="http://schemas.xmlsoap.org/wsdl/"
        xmlns:wsdlsoap="http://schemas.xmlsoap.org/wsdl/soap/"
        xmlns:xsd="http://www.w3.org/2001/XMLSchema">
    <!--WSDL created by ColdFusion version 8,0,0,171651-->
        <wsdl:types>
    <schema targetNamespace="http://rpc.xml.coldfusion" xmlns="http://www.w3.org/2001/XMLSchema">
        <import namespace="http://flexapp"/>
        <import namespace="http://schemas.xmlsoap.org/soap/encoding/"/>
        <complexType name="CFCInvocationException">
    <sequence/>
        </complexType>

        <complexType name="QueryBean">
    <sequence>
        <element name="columnList" nillable="true" type="impl:ArrayOf_xsd_string"/>
        <element name="data" nillable="true" type="impl:ArrayOfArrayOf_xsd_anyType"/>
    </sequence>
        </complexType>
    </schema>
    <schema targetNamespace="http://flexapp" xmlns="http://www.w3.org/2001/XMLSchema">
        <import namespace="http://rpc.xml.coldfusion"/>

        <import namespace="http://schemas.xmlsoap.org/soap/encoding/"/>
        <complexType name="ArrayOf_xsd_string">
    <complexContent>
        <restriction base="soapenc:Array">
    <attribute ref="soapenc:arrayType" wsdl:arrayType="xsd:string[]"/>
        </restriction>
    </complexContent>
        </complexType>
        <complexType name="ArrayOfArrayOf_xsd_anyType">

    <complexContent>
        <restriction base="soapenc:Array">
    <attribute ref="soapenc:arrayType" wsdl:arrayType="xsd:anyType[][]"/>
        </restriction>
    </complexContent>
        </complexType>
    </schema>
        </wsdl:types>

        <wsdl:message name="CFCInvocationException">

    <wsdl:part name="fault" type="tns1:CFCInvocationException"/>
    </wsdl:message>
    <wsdl:message name="returnRecordsRequest">
    </wsdl:message>
    <wsdl:message name="insertRecordResponse">
    </wsdl:message>
    <wsdl:message name="returnRecordsResponse">
    <wsdl:part name="returnRecordsReturn" type="tns1:QueryBean"/>
    </wsdl:message>
    <wsdl:message name="insertRecordRequest">
    <wsdl:part name="username" type="xsd:string"/>
    <wsdl:part name="emailaddress" type="xsd:string"/>
    </wsdl:message>
    <wsdl:portType name="returncfxml">
    <wsdl:operation name="insertRecord" parameterOrder="username emailaddress">
    <wsdl:input message="impl:insertRecordRequest" name="insertRecordRequest"/>
    <wsdl:output message="impl:insertRecordResponse" name="insertRecordResponse"/>
    <wsdl:fault message="impl:CFCInvocationException" name="CFCInvocationException"/>
    </wsdl:operation>
    <wsdl:operation name="returnRecords">
    <wsdl:input message="impl:returnRecordsRequest" name="returnRecordsRequest"/>
    <wsdl:output message="impl:returnRecordsResponse" name="returnRecordsResponse"/>
    <wsdl:fault message="impl:CFCInvocationException" name="CFCInvocationException"/>
    </wsdl:operation>
    </wsdl:portType>
    <wsdl:binding name="returncfxml.cfcSoapBinding" type="impl:returncfxml">
    <wsdlsoap:binding style="rpc" transport="http://schemas.xmlsoap.org/soap/http"/>
    <wsdl:operation name="insertRecord">
    <wsdlsoap:operation soapAction=""/>
    <wsdl:input name="insertRecordRequest">
    <wsdlsoap:body encodingStyle="http://schemas.xmlsoap.org/soap/encoding/" namespace="http://flexapp" use="encoded"/>
    </wsdl:input>
    <wsdl:output name="insertRecordResponse">
    <wsdlsoap:body encodingStyle="http://schemas.xmlsoap.org/soap/encoding/" namespace="http://flexapp" use="encoded"/>
    </wsdl:output>
    <wsdl:fault name="CFCInvocationException">
    <wsdlsoap:fault encodingStyle="http://schemas.xmlsoap.org/soap/encoding/" name="CFCInvocationException" namespace="http://flexapp" use="encoded"/>
    </wsdl:fault>
    </wsdl:operation>
    <wsdl:operation name="returnRecords">
    <wsdlsoap:operation soapAction=""/>
    <wsdl:input name="returnRecordsRequest">
    <wsdlsoap:body encodingStyle="http://schemas.xmlsoap.org/soap/encoding/" namespace="http://flexapp" use="encoded"/>
    </wsdl:input>
    <wsdl:output name="returnRecordsResponse">
    <wsdlsoap:body encodingStyle="http://schemas.xmlsoap.org/soap/encoding/" namespace="http://flexapp" use="encoded"/>
    </wsdl:output>
    <wsdl:fault name="CFCInvocationException">
    <wsdlsoap:fault encodingStyle="http://schemas.xmlsoap.org/soap/encoding/" name="CFCInvocationException" namespace="http://flexapp" use="encoded"/>
    </wsdl:fault>
    </wsdl:operation>
    </wsdl:binding>
    <wsdl:service name="returncfxmlService">
    <wsdl:port binding="impl:returncfxml.cfcSoapBinding" name="returncfxml.cfc">
    <wsdlsoap:address location="http://localhost:8500/flexapp/returnusers.cfc"/>
    </wsdl:port>
    </wsdl:service>
    </wsdl:definitions>

## Calling web services in ActionScript

The following example shows a web service call in an ActionScript script block.
Calling the `useWebService()` method declares the service, sets the destination,
fetches the WSDL document, and calls the `echoArgs()` method of the service.

> **Note:** When you declare a WebService component in ActionScript, call the
> `WebService.loadWSDL()` method.

    <?xml version="1.0"?>
    <!-- fds\rpc\WebServiceInAS.mxml -->
    <mx:Application xmlns:mx="http://www.adobe.com/2006/mxml">
        <mx:Script>
            <![CDATA[
            import mx.rpc.soap.WebService;
            import mx.rpc.events.ResultEvent;
            import mx.rpc.events.FaultEvent;
            private var ws:WebService;
            public function useWebService(intArg:int, strArg:String):void {
                ws = new WebService();
                ws.wsdl="http://myserver:8500/flexapp/app1.cfc?wsdl";
                ws.echoArgs.addEventListener("result", echoResultHandler);
                ws.addEventListener("fault", faultHandler);
                ws.loadWSDL();
                ws.echoArgs(intArg, strArg);
            }

            public function echoResultHandler(event:ResultEvent):void {
                var retStr:String = event.result.echoStr;
                var retInt:int = event.result.echoInt;
             //Do something.
            }

            public function faultHandler(event:FaultEvent):void {
          //deal with event.fault.faultString, etc
            }
            ]]>
        </mx:Script>
    </mx:Application>

## Reserved Operation names

WebService operations are accessible by simply naming them after a service
variable. However, naming conflicts can occur if an operation name happens to
match a defined method on the service. You can use the following method in
ActionScript on a WebService component to return the operation of the given
name:

    public function getOperation(name:String):Operation

## Reading WSDL documents

You can view a WSDL document in a web browser, a simple text editor, an XML
editor, or a development environment such as Adobe Dreamweaver, which contains a
built-in utility for displaying WSDL documents in an easy-to-read format.

A WSDL document contains the tags described in the following table.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<thead>
<tr class="header">
<th><p>Tag</p></th>
<th><p>Description</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><div>
<pre><code>&lt;binding&gt;</code></pre>
</div></td>
<td><p>Specifies the protocol that clients, such as applications built
with Flex, use to communicate with a web service. Bindings exist for
SOAP, HTTP GET, HTTP POST, and MIME. Flex supports the SOAP binding
only.</p></td>
</tr>
<tr class="even">
<td><div>
<pre><code>&lt;fault&gt;</code></pre>
</div></td>
<td><p>Specifies an error value that is returned as a result of a
problem processing a message.</p></td>
</tr>
<tr class="odd">
<td><div>
<pre><code>&lt;input&gt;</code></pre>
</div></td>
<td><p>Specifies a message that a client, such as an application built
with Flex, sends to a web service.</p></td>
</tr>
<tr class="even">
<td><div>
<pre><code>&lt;message&gt;</code></pre>
</div></td>
<td><p>Defines the data that a WebService operation transfers.</p></td>
</tr>
<tr class="odd">
<td><div>
<pre><code>&lt;operation&gt;</code></pre>
</div></td>
<td><p>Defines a combination of <samp>&lt;input&gt;</samp>,
<samp>&lt;output&gt;</samp>, and <samp>&lt;fault&gt;</samp>
tags.</p></td>
</tr>
<tr class="even">
<td><div>
<pre><code>&lt;output&gt;</code></pre>
</div></td>
<td><p>Specifies a message that the web service sends to a web service
client, such as an application built with Flex.</p></td>
</tr>
<tr class="odd">
<td><div>
<pre><code>&lt;port&gt;</code></pre>
</div></td>
<td><p>Specifies a web service endpoint, which specifies an association
between a binding and a network address.</p></td>
</tr>
<tr class="even">
<td><div>
<pre><code>&lt;portType&gt;</code></pre>
</div></td>
<td><p>Defines one or more operations that a web service
provides.</p></td>
</tr>
<tr class="odd">
<td><div>
<pre><code>&lt;service&gt;</code></pre>
</div></td>
<td><p>Defines a collection of <samp>&lt;port&gt;</samp>tags. Each
service maps to one <samp>&lt;portType&gt;</samp> tag and specifies
different ways to access the operations in that<samp>
&lt;portType&gt;</samp> tag.</p></td>
</tr>
<tr class="even">
<td><div>
<pre><code>&lt;types&gt;</code></pre>
</div></td>
<td><p>Defines data types that a web service's messages use.</p></td>
</tr>
</tbody>
</table>

## RPC-oriented operations and document-oriented operations

A WSDL file can specify either RPC-oriented or document-oriented
(document-literal) operations. Flex supports both operation styles.

When calling an RPC-oriented operation, an application sends a SOAP message that
specifies an operation and its parameters. When calling a document-oriented
operation, an application sends a SOAP message that contains an XML document.

In a WSDL document, each `<port>` tag has a `binding` property that specifies
the name of a particular `<soap:binding>` tag, as the following example shows:

    <binding name="InstantMessageAlertSoap" type="s0:InstantMessageAlertSoap">
        <soap:binding transport="http://schemas.xmlsoap.org/soap/http"
            style="document"/>

The `style` property of the associated `<soap:binding>` tag determines the
operation style. In this example, the style is `document`.

Any operation in a service can specify the same style or override the style that
is specified for the port associated with the service, as the following example
shows:

    <operation name="SendMSN">
        <soap:operation soapAction="http://www.bindingpoint.com/ws/imalert/
            SendMSN"        style="document"/>

## Stateful web services

Flex uses Java server sessions to maintain the state of web service endpoints
that use cookies to store session information. This feature acts as an
intermediary between applications and web services. It adds an endpoint's
identity to whatever the endpoint passes to an application. If the endpoint
sends session information, the application receives it. This feature requires no
configuration, and it is not supported for destinations that use the RTMP
channel when using the proxy service.

## Working with SOAP headers

A SOAP header is an optional tag in a SOAP envelope that usually contains
application-specific information, such as authentication information.

#### Adding SOAP headers to web service requests

Some web services require that you pass along a SOAP header when you call an
operation.

You can add a SOAP header to all web service operations or individual operations
by calling a WebService or Operation object's `addHeader()` method or
`addSimpleHeader()` method in an event listener function.

When you use the `addHeader()` method, you first must create SOAPHeader and
QName objects separately. The `addHeader()`method has the following signature:

    addHeader(header:mx.rpc.soap.SOAPHeader):void

To create a SOAPHeader object, use the following constructor:

    SOAPHeader(qname:QName, content:Object)

To create the QName object in the first parameter of the `SOAPHeader()` method,
use the following constructor:

    QName(uri:String, localName:String)

The `content` parameter of the `SOAPHeader()` constructor is a set of name-value
pairs based on the following format:

    {name1:value1, name2:value2}

The `addSimpleHeader()` method is a shortcut for a single name-value SOAP
header. When you use the `addSimpleHeader()` method, you create SOAPHeader and
QName objects in parameters of the method. The `addSimpleHeader()` method has
the following signature:

    addSimpleHeader(qnameLocal:String, qnameNamespace:String, headerName:String,
        headerValue:Object):void

The `addSimpleHeader()` method takes the following parameters:

- _`qnameLocal`_ is the local name for the header QName.

- _`qnameNamespace`_ is the namespace for the header QName.

- _`headerName`_ is the name of the header.

- _`headerValue`_ is the value of the header. This can be a string if it is a
  simple value, an object that will undergo basic XML encoding, or XML if you
  want to specify the header XML yourself.

  The code in the following example shows how to use the `addHeader()` method
  and the `addSimpleHeader()` method to add a SOAP header. The methods are
  called in an event listener function called `headers`, and the event listener
  is assigned in the `load` property of an `<mx:WebService>` tag:

      <?xml version="1.0"?>
      <!-- fds\rpc\WebServiceAddHeader.mxml -->
      <mx:Application xmlns:mx="http://www.adobe.com/2006/mxml" height="600">
          <mx:WebService id="ws" wsdl="http://myserver:8500/flexapp/app1.cfc?wsdl" load="headers();"/>
          <mx:Script>
              <![CDATA[
                  import mx.rpc.soap.SOAPHeader;
                  private var header1:SOAPHeader;
                  private var header2:SOAPHeader;

                  public function headers():void {

                  // Create QName and SOAPHeader objects.
                  var q1:QName=new QName("http://soapinterop.org/xsd", "Header1");
                  header1=new SOAPHeader(q1, {string:"bologna",int:"123"});
                  header2=new SOAPHeader(q1, {string:"salami",int:"321"});


                  // Add the header1 SOAP Header to all web service requests.
                  ws.addHeader(header1);

                  // Add the header2 SOAP Header to the getSomething operation.
                  ws.getSomething.addHeader(header2);

                  // Within the addSimpleHeader method,
                  // which adds a SOAP header to web
                  //service requests, create SOAPHeader and QName objects.
                  ws.addSimpleHeader
                      ("header3", "http://soapinterop.org/xsd", "foo","bar");
                  }
              ]]>
          </mx:Script>
      </mx:Application>

#### Clearing SOAP headers

You use the WebService or operation object's `clearHeaders()` method to remove
SOAP headers that you added to the object, as the following example shows for a
WebService object. You must call `clearHeaders()` at the level (WebService or
operation) where the header was added.

    <?xml version="1.0"?>
    <!-- fds\rpc\WebServiceClearHeader.mxml -->
    <mx:Application xmlns:mx="http://www.adobe.com/2006/mxml" height="600" >

        <!-- The value of the destination property is for demonstration only and is not a real destination. -->

        <mx:WebService id="ws" wsdl="http://myserver:8500/flexapp/app1.cfc?wsdl" load="headers();"/>

        <mx:Script>
            <![CDATA[
                import mx.rpc.*;
                import mx.rpc.soap.SOAPHeader;

                private function headers():void {
                    // Create QName and SOAPHeader objects.
                    var q1:QName=new QName("Header1", "http://soapinterop.org/xsd");
                    var header1:SOAPHeader=new SOAPHeader(q1, {string:"bologna",int:"123"});
                    var header2:SOAPHeader=new SOAPHeader(q1, {string:"salami",int:"321"});
                        // Add the header1 SOAP Header to all web service request.
                    ws.addHeader(header1);
                    // Add the header2 SOAP Header to the getSomething operation.
                    ws.getSomething.addHeader(header2);

                    // Within the addSimpleHeader method, which adds a SOAP header to all
                    // web service requests, create SOAPHeader and QName objects.
                    ws.addSimpleHeader("header3","http://soapinterop.org/xsd", "foo", "bar");
                }

                // Clear SOAP headers added at the WebService and Operation levels.
                private function clear():void {
                    ws.clearHeaders();
                    ws.getSomething.clearHeaders();
                }
            ]]>
        </mx:Script>

        <mx:HBox>
                <mx:Button label="Clear headers and run again" click="clear();"/>
        </mx:HBox>

    </mx:Application>

## Redirecting a web service to a different URL

Some web services require that you change to a different endpoint URL after you
process the WSDL and make an initial call to the web service. For example,
suppose that you want to use a web service that requires you to pass security
credentials. When you call the web service to send login credentials, it accepts
the credentials and returns the actual endpoint URL that is required to use the
service's business operations. Before calling the business operations, you must
change the `endpointURI` property of your WebService component.

The following example shows a result event listener that stores the endpoint URL
that a web service returns in a variable, and then passes that variable into a
function to change the endpoint URL for subsequent requests:

    ...
    public function onLoginResult(event:ResultEvent):void {

    //Extract the new service endpoint from the login result.
    var newServiceURL = event.result.serverUrl;

    // Redirect all service operations to the URL received in the login result.
        serviceName.endpointURI=newServiceURL;

    }
    ...

A web service that requires you to pass security credentials might also return
an identifier that you must attach in a SOAP header for subsequent requests. For
more information, see [Working with SOAP headers](#working-with-soap-headers).

## Serializing web service data

### Encoding ActionScript data

The following table shows the encoding mappings from ActionScript 3.0 types to
XML schema complex types.

<table>
<colgroup>
<col style="width: 33%" />
<col style="width: 33%" />
<col style="width: 33%" />
</colgroup>
<thead>
<tr class="header">
<th><p>XML schema definition</p></th>
<th><p>Supported ActionScript 3.0 types</p></th>
<th><p>Notes</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>Top-level elements</p></td>
<td><p> </p></td>
<td><p> </p></td>
</tr>
<tr class="even">
<td><p>xsd:element</p>
<p>nillable == true</p></td>
<td><p>Object</p></td>
<td><p>If input value is <samp>null</samp>, encoded output is set with
the <samp>xsi:nil</samp> attribute.</p></td>
</tr>
<tr class="odd">
<td><p>xsd:element</p>
<p>fixed != null</p></td>
<td><p>Object</p></td>
<td><p>Input value is ignored and fixed value is used instead.</p></td>
</tr>
<tr class="even">
<td><p>xsd:element</p>
<p>default != null</p></td>
<td><p>Object</p></td>
<td><p>If input value is <samp>null</samp>, this default value is used
instead.</p></td>
</tr>
<tr class="odd">
<td><p>Local elements</p></td>
<td><p> </p></td>
<td><p> </p></td>
</tr>
<tr class="even">
<td><p>xsd:element</p>
<p>maxOccurs == 0</p></td>
<td><p>Object</p></td>
<td><p>Input value is ignored and omitted from encoded output.</p></td>
</tr>
<tr class="odd">
<td><p>xsd:element</p>
<p>maxOccurs == 1</p></td>
<td><p>Object</p></td>
<td><p>Input value is processed as a single entity. If the associated
type is a SOAP-encoded array, then arrays and
<samp>mx.collection.IList</samp>implementations pass through intact to
be special cased by the SOAP encoder for that type.</p></td>
</tr>
<tr class="even">
<td><p>xsd:element</p>
<p>maxOccurs &gt; 1</p></td>
<td><p>Object</p></td>
<td><p>Input value should be iterable (such as an array or
<samp>mx.collections.IList</samp> implementation), although noniterable
values are wrapped before processing. Individual items are encoded as
separate entities according to the definition.</p></td>
</tr>
<tr class="odd">
<td><p>xsd:element</p>
<p>minOccurs == 0</p></td>
<td><p>Object</p></td>
<td><p>If input value is undefined or <samp>null</samp>, encoded output
is omitted.</p></td>
</tr>
</tbody>
</table>

The following table shows the encoding mappings from ActionScript 3.0 types to
XML schema built-in types.

<table>
<colgroup>
<col style="width: 33%" />
<col style="width: 33%" />
<col style="width: 33%" />
</colgroup>
<thead>
<tr class="header">
<th><p>XML schema type</p></th>
<th><p>Supported ActionScript 3.0 types</p></th>
<th><p>Notes</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p><samp>xsd:anyType</samp></p>
<p><samp>xsd:anySimpleType</samp></p></td>
<td><p>Object</p></td>
<td><p>Boolean -&gt; <samp>xsd:boolean</samp></p>
<p>ByteArray -&gt;<samp> xsd:base64Binary</samp></p>
<p>Date -&gt; <samp>xsd:dateTime</samp></p>
<p>int -&gt;<samp> xsd:int</samp></p>
<p>Number -&gt; <samp>xsd:double</samp></p>
<p>String -&gt; <samp>xsd:string</samp></p>
<p>uint -&gt;<samp> xsd:unsignedInt</samp></p></td>
</tr>
<tr class="even">
<td><p><samp>xsd:base64Binary</samp></p></td>
<td><p><samp>flash.utils.ByteArray</samp></p></td>
<td><p><samp>mx.utils.Base64Encoder</samp> is used (without line
wrapping).</p></td>
</tr>
<tr class="odd">
<td><p><samp>xsd:boolean</samp></p></td>
<td><p>Boolean</p>
<p>Number</p>
<p>Object</p></td>
<td><p>Always encoded as <samp>true</samp> or <samp>false</samp>.</p>
<p><samp>Number == 1</samp> then <samp>true</samp>, otherwise
<samp>false</samp>.</p>
<p><samp>Object.toString()</samp>== "true" or "1" then
<samp>true</samp>, otherwise <samp>false</samp>.</p></td>
</tr>
<tr class="even">
<td><p><samp>xsd:byte</samp></p>
<p><samp>xsd:unsignedByte</samp></p></td>
<td><p>Number</p>
<p>String</p></td>
<td><p>String first converted to Number.</p></td>
</tr>
<tr class="odd">
<td><p><samp>xsd:date</samp></p></td>
<td><p>Date</p>
<p>Number</p>
<p>String</p></td>
<td><p>Date UTC accessor methods are used.</p>
<p>Number used to set Date.time.</p>
<p>String assumed to be preformatted and encoded as is.</p></td>
</tr>
<tr class="even">
<td><p><samp>xsd:dateTime</samp></p></td>
<td><p>Date</p>
<p>Number</p>
<p>String</p></td>
<td><p>Date UTC accessor methods are used.</p>
<p>Number used to set Date.time.</p>
<p>String assumed to be preformatted and encoded as is.</p></td>
</tr>
<tr class="odd">
<td><p><samp>xsd:decimal</samp></p></td>
<td><p>Number</p>
<p>String</p></td>
<td><p><samp>Number.toString()</samp>is used. Infinity, -Infinity, and
NaN are invalid for this type.</p>
<p>String first converted to Number.</p></td>
</tr>
<tr class="even">
<td><p><samp>xsd:double</samp></p></td>
<td><p>Number</p>
<p>String</p></td>
<td><p>Limited to range of Number.</p>
<p>String first converted to Number.</p></td>
</tr>
<tr class="odd">
<td><p><samp>xsd:duration</samp></p></td>
<td><p>Object</p></td>
<td><p><samp>Object.toString()</samp> is called.</p></td>
</tr>
<tr class="even">
<td><p><samp>xsd:float</samp></p></td>
<td><p>Number</p>
<p>String</p></td>
<td><p>Limited to range of Number.</p>
<p>String first converted to Number.</p></td>
</tr>
<tr class="odd">
<td><p><samp>xsd:gDay</samp></p></td>
<td><p>Date</p>
<p>Number</p>
<p>String</p></td>
<td><p><samp>Date.getUTCDate()</samp> is used.</p>
<p>Number used directly for day.</p>
<p>String parsed as Number for day.</p></td>
</tr>
<tr class="even">
<td><p><samp>xsd:gMonth</samp></p></td>
<td><p>Date</p>
<p>Number</p>
<p>String</p></td>
<td><p><samp>Date.getUTCMonth()</samp>is used.</p>
<p>Number used directly for month.</p>
<p>String parsed as Number for month.</p></td>
</tr>
<tr class="odd">
<td><p><samp>xsd:gMonthDay</samp></p></td>
<td><p>Date</p>
<p>String</p></td>
<td><p><samp>Date.getUTCMonth()</samp>and
<samp>Date.getUTCDate()</samp>are used.</p>
<p>String parsed for month and day portions.</p></td>
</tr>
<tr class="even">
<td><p><samp>xsd:gYear</samp></p></td>
<td><p>Date</p>
<p>Number</p>
<p>String</p></td>
<td><p><samp>Date.getUTCFullYear()</samp>is used.</p>
<p>Number used directly for year.</p>
<p>String parsed as Number for year.</p></td>
</tr>
<tr class="odd">
<td><p><samp>xsd:gYearMonth</samp></p></td>
<td><p>Date</p>
<p>String</p></td>
<td><p><samp>Date.getUTCFullYear()</samp>and
<samp>Date.getUTCMonth()</samp> are used.</p>
<p>String parsed for year and month portions.</p></td>
</tr>
<tr class="even">
<td><p><samp>xsd:hexBinary</samp></p></td>
<td><p>flash.utils.ByteArray</p></td>
<td><p>mx.utils.HexEncoder is used.</p></td>
</tr>
<tr class="odd">
<td><p><samp>xsd:integer</samp></p>
<p><samp>and derivatives:</samp></p>
<p><samp>xsd:negativeInteger</samp></p>
<p><samp>xsd:nonNegativeInteger</samp></p>
<p><samp>xsd:positiveInteger</samp></p>
<p><samp>xsd:nonPositiveInteger</samp></p></td>
<td><p>Number</p>
<p>String</p></td>
<td><p>Limited to range of Number.</p>
<p>String first converted to Number.</p></td>
</tr>
<tr class="even">
<td><p><samp>xsd:int</samp></p>
<p><samp>xsd:unsignedInt</samp></p></td>
<td><p>Number</p>
<p>String</p></td>
<td><p>String first converted to Number.</p></td>
</tr>
<tr class="odd">
<td><p><samp>xsd:long</samp></p>
<p><samp>xsd:unsignedLong</samp></p></td>
<td><p>Number</p>
<p>String</p></td>
<td><p>String first converted to Number.</p></td>
</tr>
<tr class="even">
<td><p><samp>xsd:short</samp></p>
<p><samp>xsd:unsignedShort</samp></p></td>
<td><p>Number</p>
<p>String</p></td>
<td><p>String first converted to Number.</p></td>
</tr>
<tr class="odd">
<td><p><samp>xsd:string</samp></p>
<p><samp>and derivatives:</samp></p>
<p><samp>xsd:ID</samp></p>
<p><samp>xsd:IDREF</samp></p>
<p><samp>xsd:IDREFS</samp></p>
<p><samp>xsd:ENTITY</samp></p>
<p><samp>xsd:ENTITIES xsd:language</samp></p>
<p><samp>xsd:Name</samp></p>
<p><samp>xsd:NCName</samp></p>
<p><samp>xsd:NMTOKEN</samp></p>
<p><samp>xsd:NMTOKENS</samp></p>
<p><samp>xsd:normalizedString</samp></p>
<p><samp>xsd:token</samp></p></td>
<td><p>Object</p></td>
<td><p><samp>Object.toString()</samp> is invoked.</p></td>
</tr>
<tr class="even">
<td><p><samp>xsd:time</samp></p></td>
<td><p>Date</p>
<p>Number</p>
<p>String</p></td>
<td><p>Date UTC accessor methods are used.</p>
<p>Number used to set Date.time.</p>
<p>String assumed to be preformatted and encoded as is.</p></td>
</tr>
<tr class="odd">
<td><p><samp>xsi:nil</samp></p></td>
<td><p>null</p></td>
<td><p>If the corresponding XML schema element definition has minOccurs
&gt; 0, a <samp>null</samp> value is encoded by using xsi:nil; otherwise
the element is omitted entirely.</p></td>
</tr>
</tbody>
</table>

The following table shows the mapping from ActionScript 3.0 types to
SOAP-encoded types.

<table>
<colgroup>
<col style="width: 33%" />
<col style="width: 33%" />
<col style="width: 33%" />
</colgroup>
<thead>
<tr class="header">
<th><p>SOAPENC type</p></th>
<th><p>Supported ActionScript 3.0 types</p></th>
<th><p>Notes</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>soapenc:Array</p></td>
<td><p>Array</p>
<p>mx.collections.IList</p></td>
<td><p>SOAP-encoded arrays are special cased and are supported only with
RPC-encoded style web services.</p></td>
</tr>
<tr class="even">
<td><p>soapenc:base64</p></td>
<td><p>flash.utils.ByteArray</p></td>
<td><p>Encoded in the same manner as
<samp>xsd:base64Binary</samp>.</p></td>
</tr>
<tr class="odd">
<td><p>soapenc:*</p></td>
<td><p>Object</p></td>
<td><p>Any other SOAP-encoded type is processed as if it were in the XSD
namespace based on the <samp>localName</samp> of the type's
QName.</p></td>
</tr>
</tbody>
</table>

### Decoding XML schema and SOAP to ActionScript 3.0

The following table shows the decoding mappings from XML schema built-in types
to ActionScript 3.0 types.

<table>
<colgroup>
<col style="width: 33%" />
<col style="width: 33%" />
<col style="width: 33%" />
</colgroup>
<thead>
<tr class="header">
<th><p>XML schema type</p></th>
<th><p>Decoded ActionScript 3.0 types</p></th>
<th><p>Notes</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p><samp>xsd:anyType</samp></p>
<p><samp>xsd:anySimpleType</samp></p></td>
<td><p>String</p>
<p>Boolean</p>
<p>Number</p></td>
<td><p>If content is empty -&gt; <samp>xsd:string</samp>.</p>
<p>If content cast to Number and value is NaN; or</p>
<p>if content starts with "0" or "-0", or</p>
<p>it content ends with "E":</p>
<p>then, if content is "true" or "false" -&gt;
<samp>xsd:boolean</samp></p>
<p>otherwise -&gt; <samp>xsd:string</samp>.</p>
<p>Otherwise content is a valid Number and thus -&gt;
<samp>xsd:double</samp>.</p></td>
</tr>
<tr class="even">
<td><p><samp>xsd:base64Binary</samp></p></td>
<td><p><samp>flash.utils.ByteArray</samp></p></td>
<td><p><samp>mx.utils.Base64Decoder</samp> is used.</p></td>
</tr>
<tr class="odd">
<td><p><samp>xsd:boolean</samp></p></td>
<td><p>Boolean</p></td>
<td><p>If content is "true" or "1" then <samp>true</samp>, otherwise
<samp>false</samp>.</p></td>
</tr>
<tr class="even">
<td><p><samp>xsd:date</samp></p></td>
<td><p>Date</p></td>
<td><p>If no time zone information is present, local time is
assumed.</p></td>
</tr>
<tr class="odd">
<td><p><samp>xsd:dateTime</samp></p></td>
<td><p>Date</p></td>
<td><p>If no time zone information is present, local time is
assumed.</p></td>
</tr>
<tr class="even">
<td><p><samp>xsd:decimal</samp></p></td>
<td><p>Number</p></td>
<td><p>Content is created via <samp>Number(content)</samp> and is thus
limited to the range of Number.</p></td>
</tr>
<tr class="odd">
<td><p><samp>xsd:double</samp></p></td>
<td><p>Number</p></td>
<td><p>Content is created via <samp>Number(content)</samp> and is thus
limited to the range of Number.</p></td>
</tr>
<tr class="even">
<td><p><samp>xsd:duration</samp></p></td>
<td><p>String</p></td>
<td><p>Content is returned with whitespace collapsed.</p></td>
</tr>
<tr class="odd">
<td><p><samp>xsd:float</samp></p></td>
<td><p>Number</p></td>
<td><p>Content is converted through <samp>Number(content)</samp> and is
thus limited to the range of Number.</p></td>
</tr>
<tr class="even">
<td><p><samp>xsd:gDay</samp></p></td>
<td><p>uint</p></td>
<td><p>Content is converted through <samp>uint(content)</samp>.</p></td>
</tr>
<tr class="odd">
<td><p><samp>xsd:gMonth</samp></p></td>
<td><p>uint</p></td>
<td><p>Content is converted through <samp>uint(content)</samp>.</p></td>
</tr>
<tr class="even">
<td><p><samp>xsd:gMonthDay</samp></p></td>
<td><p>String</p></td>
<td><p>Content is returned with whitespace collapsed.</p></td>
</tr>
<tr class="odd">
<td><p><samp>xsd:gYear</samp></p></td>
<td><p>uint</p></td>
<td><p>Content is converted through <samp>uint(content)</samp>.</p></td>
</tr>
<tr class="even">
<td><p><samp>xsd:gYearMonth</samp></p></td>
<td><p>String</p></td>
<td><p>Content is returned with whitespace collapsed.</p></td>
</tr>
<tr class="odd">
<td><p><samp>xsd:hexBinary</samp></p></td>
<td><p><samp>flash.utils.ByteArray</samp></p></td>
<td><p><samp>mx.utils.HexDecoder</samp> is used.</p></td>
</tr>
<tr class="even">
<td><p>x<samp>sd:integer</samp></p>
<p><samp>and derivatives:</samp></p>
<p><samp>xsd:byte</samp></p>
<p><samp>xsd:int</samp></p>
<p><samp>xsd:long</samp></p>
<p><samp>xsd:negativeInteger</samp></p>
<p><samp>xsd:nonNegativeInteger</samp></p>
<p><samp>xsd:nonPositiveInteger</samp></p>
<p><samp>xsd:positiveInteger</samp></p>
<p><samp>xsd:short</samp></p>
<p><samp>xsd:unsignedByte</samp></p>
<p><samp>xsd:unsignedInt</samp></p>
<p><samp>xsd:unsignedLong</samp></p>
<p><samp>xsd:unsignedShort</samp></p></td>
<td><p>Number</p></td>
<td><p>Content is decoded via <samp>parseInt()</samp>.</p></td>
</tr>
<tr class="odd">
<td><p><samp>xsd:string</samp></p>
<p>and derivatives:</p>
<p><samp>xsd:ID</samp></p>
<p><samp>xsd:IDREF</samp></p>
<p><samp>xsd:IDREFS</samp></p>
<p><samp>xsd:ENTITY</samp></p>
<p><samp>xsd:ENTITIES xsd:language</samp></p>
<p><samp>xsd:Name</samp></p>
<p><samp>xsd:NCName</samp></p>
<p><samp>xsd:NMTOKEN</samp></p>
<p><samp>xsd:NMTOKENS</samp></p>
<p><samp>xsd:normalizedString</samp></p>
<p><samp>xsd:token</samp></p></td>
<td><p>String</p></td>
<td><p>The raw content is simply returned as a string.</p></td>
</tr>
<tr class="even">
<td><p><samp>xsd:time</samp></p></td>
<td><p>Date</p></td>
<td><p>If no time zone information is present, local time is
assumed.</p></td>
</tr>
<tr class="odd">
<td><p><samp>xsi:nil</samp></p></td>
<td><p>null</p></td>
<td><p> </p></td>
</tr>
</tbody>
</table>

The following table shows the decoding mappings from SOAP-encoded types to
ActionScript 3.0 types.

<table>
<colgroup>
<col style="width: 33%" />
<col style="width: 33%" />
<col style="width: 33%" />
</colgroup>
<thead>
<tr class="header">
<th><p>SOAPENC type</p></th>
<th><p>Decoded ActionScript type</p></th>
<th><p>Notes</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p><samp>soapenc:Array</samp></p></td>
<td><p>Array</p>
<p><samp>mx.collections.ArrayCollection</samp></p></td>
<td><p>SOAP-encoded arrays are special cased. If
<samp>makeObjectsBindable</samp> is <samp>true</samp>, the result is
wrapped in an ArrayCollection; otherwise a simple array is
returned.</p></td>
</tr>
<tr class="even">
<td><p><samp>soapenc:base64</samp></p></td>
<td><p><samp>flash.utils.ByteArray</samp></p></td>
<td><p>Decoded in the same manner as
<samp>xsd:base64Binary</samp>.</p></td>
</tr>
<tr class="odd">
<td><p><samp>soapenc:*</samp></p></td>
<td><p>Object</p></td>
<td><p>Any other SOAP-encoded type is processed as if it were in the XSD
namespace based on the <samp>localName</samp> of the type's
QName.</p></td>
</tr>
</tbody>
</table>

The following table shows the decoding mappings from custom data types to
ActionScript 3.0 data types.

<table>
<colgroup>
<col style="width: 33%" />
<col style="width: 33%" />
<col style="width: 33%" />
</colgroup>
<thead>
<tr class="header">
<th><p>Custom type</p></th>
<th><p>Decoded ActionScript 3.0 type</p></th>
<th><p>Notes</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>Apache Map</p>
<p><samp>http://xml.apache.org/xml-soap:Map</samp></p></td>
<td><p>Object</p></td>
<td><p>SOAP representation of <samp>java.util.Map. Keys</samp> must be
representable as strings.</p></td>
</tr>
<tr class="even">
<td><p>Apache Rowset</p>
<p><samp>http://xml.apache.org/xml-soap:Rowset</samp></p></td>
<td><p>Array of objects</p></td>
<td><p> </p></td>
</tr>
<tr class="odd">
<td><p>ColdFusion QueryBean</p>
<p><samp>http://rpc.xml.coldfusion:QueryBean</samp></p></td>
<td><p>Array of objects</p>
<p><samp>mx.collections.ArrayCollection</samp> of objects</p></td>
<td><p>If <samp>makeObjectsBindable</samp> is true, the resulting array
is wrapped in an ArrayCollection.</p></td>
</tr>
</tbody>
</table>

### XML Schema element support

The following XML schema structures or structure attributes are only partially
implemented in Flex 4:

    <choice>
    <all>
    <union

The following XML Schema structures or structure attributes are ignored and are
not supported in Flex 4:

    <attribute use="required"/>

    <element
        substitutionGroup="..."
        unique="..."
        key="..."
        keyref="..."
        field="..."
        selector="..."/>

    <simpleType>
        <restriction>
            <minExclusive>
            <minInclusive>
            <maxExclusiv>
            <maxInclusive>
            <totalDigits>
            <fractionDigits>
            <length>
            <minLength>
            <maxLength>
            <enumeration>
            <whiteSpace>
            <pattern>
        </restriction>
    </simpleType>

    <complexType
        final="..."
        block="..."
        mixed="..."
        abstract="..."/>

    <any
    processContents="..."/>

    <annotation>

### Customizing web service type mapping

When consuming data from a web service invocation, Flex usually creates untyped
anonymous ActionScript objects that mimic the XML structure in the body of the
SOAP message. If you want Flex to create an instance of a specific class, you
can use an mx.rpc.xml.SchemaTypeRegistry object and register a QName object with
a corresponding ActionScript class.

For example, suppose you have the following class definition in a file named
User.as:

    package
    {
         public class User
         {
           public function User() {}

           public var firstName:String;
           public var lastName:String;
         }
    }

Next, you want to invoke a `getUser` operation on a web service that returns the
following XML:

    <tns:getUserResponse xmlns:tns="http://example.uri">
         <tns:firstName>Ivan</tns:firstName>
         <tns:lastName>Petrov</tns:lastName>
    </tns:getUserResponse>

Make sure you get an instance of your User class instead of a generic Object
when you invoke the getUser operation, you need the following ActionScript code
inside a method in your application:

    SchemaTypeRegistry.getInstance().registerClass(new QName("http://example.uri", "getUserResponse"), User);

`SchemaTypeRegistry.getInstance()` is a static method that returns the default
instance of the type registry. In most cases, that is all you need. However,
this registers a given QName with the same ActionScript class across all web
service operations in your application. If you want to register different
classes for different operations, you need the following code in a method in
your application:

    var qn:QName = new QName("http://the.same", "qname");
    var typeReg1:SchemaTypeRegistry = new SchemaTypeRegistry();
    var typeReg2:SchemaTypeRegistry = new SchemaTypeRegistry();
    typeReg1.registerClass(qn, someClass);
    myWS.someOperation.decoder.typeRegistry = typeReg1;

    typeReg2.registerClass(qn, anotherClass);
    myWS.anotherOperation.decoder.typeRegistry = typeReg2;

### Using custom web service serialization

There are two approaches to take full control over how ActionScript objects are
serialized into XML and how XML response messages are deserialized. The
recommended one is to work directly with E4X.

If you pass an instance of XML as the only parameter to a web service operation,
it is passed on untouched as the child of the `<SOAP:Body>` node in the
serialized request. Use this strategy when you need full control over the SOAP
message. Similarly, when deserializing a web service response, you can set the
operation's `resultFormat` property to e4x. This returns an XMLList object with
the children of the `<SOAP:Body>` node in the response message. From there, you
can implement the necessary custom logic to create the appropriate ActionScript
objects.

The second and more tedious approach is to provide your own implementations of
mx.rpc.soap.ISOAPDecoder and mx.rpc.soap.ISOAPEncoder. For example, if you have
written a class called MyDecoder that implements ISOAPDecoder, you can have the
following in a method in your application:

    myWS.someOperation.decoder = new MyDecoder();

When you invoke `someOperation`, Flex calls the `decodeResponse()` method of the
MyDecoder class. From that point on it is up to the custom implementation to
handle the full SOAP message and produce the expected ActionScript objects.
