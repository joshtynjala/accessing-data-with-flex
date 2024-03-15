# Using RemoteObject components

You can use a Flex RemoteObject component to call methods on a ColdFusion
component or Java class.

You can also use RemoteObject components with PHP and .NET objects with
third-party software, such as the open source projects AMFPHP and SabreAMF, and
Midnight Coders WebORB. For more information, see the following websites:

- Zend
  Framework [http://framework.zend.com/](https://web.archive.org/web/20151031200901mp_/http://framework.zend.com/)

- AMFPHP [http://amfphp.sourceforge.net/](https://web.archive.org/web/20151031200901mp_/http://amfphp.sourceforge.net/)

- SabreAMF [http://www.osflash.org/sabreamf](https://web.archive.org/web/20151031200901mp_/http://www.osflash.org/sabreamf)

- Midnight Coders
  WebORB [http://www.themidnightcoders.com/](https://web.archive.org/web/20151031200901mp_/http://www.themidnightcoders.com/)

RemoteObject components use the AMF protocol to send and receive data, while
WebService and HTTPService components use the HTTP protocol. AMF is
significantly faster than HTTP, however server-side coding and configuration is
typically more complex.

Flash Builder for PHP is a development tool created in partnership with Zend
Technologies that includes an integrated copy of Zend Studio. For more
information, see the
[Adobe website](https://web.archive.org/web/20151031200901mp_/http://www.adobe.com/products/flash-builder-php.html).

As with HTTPService and WebService components, you can use a RemoteObject
component to display the result of a database query in an application. You can
also use the component to insert, update, and delete data in a database. When
the result of the query has been returned to the application, you can display it
in one or more user interface controls.

For API reference information about the RemoteObject component, see
[mx.rpc.remoting.mxml.RemoteObject](https://help.adobe.com/en_US/FlashPlatform/reference/actionscript/3/mx/rpc/remoting/mxml/RemoteObject.html).

## Sample RemoteObject application

#### MXML code

The application in the following example uses a RemoteObject component to call a
ColdFusion component. The ColdFusion component queries a MySQL database table
called users. It returns the query result to the application where it is bound
to the `dataProvider` property of a DataGrid control and displayed in the
DataGrid control. The application also sends the user name and e-mail address of
new users to the ColdFusion component, which performs an insert into the user
database table.

    <?xml version="1.0" encoding="utf-8"?>
    <s:Application xmlns:fx="http://ns.adobe.com/mxml/2009"
            xmlns:s="library://ns.adobe.com/flex/spark"
            xmlns:mx="library://ns.adobe.com/flex/mx" minWidth="955" minHeight="600">
        <fx:Declarations>
            <mx:RemoteObject
                id="userRequest"
                destination="ColdFusion"
                source="flexapp.returnusers">

                <mx:method name="returnRecords" result="returnHandler(event)"
                           fault="mx.controls.Alert.show(event.fault.faultString)"/>
                <mx:method name="insertRecord" result="insertHandler()"
                           fault="mx.controls.Alert.show(event.fault.faultString)"/>
            </mx:RemoteObject>
        </fx:Declarations>

        <fx:Script>
            <![CDATA[
                import mx.rpc.events.ResultEvent;

                private function returnHandler(e:ResultEvent):void
                {
                    dgUserRequest.dataProvider = e.result;
                }
                private function insertHandler():void
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

        <mx:DataGrid id="dgUserRequest" x="22" y="200">
            <mx:columns>
                <mx:DataGridColumn headerText="User ID" dataField="userid"/>
                <mx:DataGridColumn headerText="User Name" dataField="username"/>
            </mx:columns>
        </mx:DataGrid>
    </s:Application>

In this application, the RemoteObject component's `destination` property is set
to `Coldfusion` and the `source` property is set to the fully qualified name of
the ColdFusion component.

In contrast, when working with LiveCycle Data Services or BlazeDS, you specify a
fully qualified class name in the `source` property of a remoting service
destination in a configuration file, which by default is the remoting-config.xml
file. You specify the name of the destination in the RemoteObject component's
`destination` property. The destination class also must have a no-args
constructor. You can optionally configure a destination this way when working
with ColdFusion instead of by using the `source` property on the RemoteObject
component.

#### ColdFusion component

The application calls the following ColdFusion component. This ColdFusion code
performs SQL database inserts and queries and returns query results to the
application. The ColdFusion page uses the `cfquery` tag to insert data into the
database and query the database, and it uses the `cfreturn` tag to format the
query results as a ColdFusion query object.

    <cfcomponent name="returnusers">
        <cffunction name="returnRecords" access="remote" returnType="query">

            <cfquery name="alluserinfo" datasource="flexcf">
                    SELECT userid, username, emailaddress FROM users
            </cfquery>
            <cfreturn alluserinfo>
        </cffunction>
        <cffunction name="insertRecord" access="remote" returnType="void">

            <cfargument name="username" required="true" type="string">
            <cfargument name="emailaddress" required="true" type="string">
            <cfquery name="addempinfo" datasource="flexcf">
                INSERT INTO users (username, emailaddress) VALUES (
                <cfqueryparam value="#arguments.username#" cfsqltype="CF_SQL_VARCHAR" maxlength="255">,
                <cfqueryparam value="#arguments.emailaddress#" cfsqltype="CF_SQL_VARCHAR" maxlength="255"> )
            </cfquery>
            <cfreturn>
        </cffunction>
    </cfcomponent>

## Calling RemoteObject components in ActionScript

In the following ActionScript example, calling the `useRemoteObject()` method
declares the service, sets the destination, sets up result and fault event
listeners, and calls the service's `getList()` method.

    <?xml version="1.0"?>
    <!-- fds\rpc\ROInAS.mxml -->
    <mx:Application xmlns:mx="http://www.adobe.com/2006/mxml">
        <mx:Script>
            <![CDATA[
                import mx.controls.Alert;
                import mx.rpc.remoting.RemoteObject;
                import mx.rpc.events.ResultEvent;
                import mx.rpc.events.FaultEvent;

                [Bindable]
                public var empList:Object;
                public var employeeRO:RemoteObject;

                public function useRemoteObject(intArg:int, strArg:String):void {
                    employeeRO = new RemoteObject();
                    employeeRO.destination = "SalaryManager";
                    employeeRO.getList.addEventListener("result", getListResultHandler);
                    employeeRO.addEventListener("fault", faultHandler);
                    employeeRO.getList(deptComboBox.selectedItem.data);
                }

                public function getListResultHandler(event:ResultEvent):void {
                     // Do something
                    empList=event.result;
                }

                public function faultHandler (event:FaultEvent):void {
                 // Deal with event.fault.faultString, etc.
                    Alert.show(event.fault.faultString, 'Error');
                }
            ]]>
        </mx:Script>
        <mx:ComboBox id="deptComboBox"/>
    </mx:Application>

## Accessing Java objects in the source path

The RemoteObject component lets you access stateless and stateful Java objects
that are in the LiveCycle Data Services, BlazeDS, or ColdFusion web
application's source path. You can place stand-alone class files in the web
application's WEB‑INF/classes directory to add them to the source path. You can
place classes contained in Java Archive (JAR) files in the web application's
WEB‑INF/lib directory to add them to the source path. You specify the fully
qualified class name in the `source` property of a remoting service destination
in the LiveCycle Data Services, BlazeDS, or ColdFusion services-config.xml file,
or a file that it includes by reference, such as the remoting-config.xml file.
The class also must have a no-args constructor. For ColdFusion, you can
optionally set the RemoteObject component's `destination` property to
`Coldfusion` and the `source` property to the fully qualified name of a
ColdFusion component or Java class.

When you configure a remoting service destination to access stateless objects
(the request scope), Flex creates a different object for each method call
instead of calling methods on the same object. You can set the scope of an
object to the request scope (default value), the application scope, or the
session scope. Objects in the application scope are available to the web
application that contains the object. Objects in the session scope are available
to the entire client session.

When you configure a remote object destination to access stateful objects, Flex
creates the object once on the server and maintains state between method calls.
If storing the object in the application or session scope causes memory
problems, use the request scope.

## Accessing EJBs and other objects in JNDI

You can access Enterprise JavaBeans (EJBs) and other objects stored in the Java
Naming and Directory Interface (JNDI) by calling methods on a destination that
is a service facade class that looks up an object in JNDI and calls its methods.

You can use stateless or stateful objects to call the methods of Enterprise
JavaBeans and other objects that use JNDI. For an EJB, you can call a service
facade class that returns the EJB object from JNDI and calls a method on the
EJB.

In your Java class, you use the standard Java coding pattern, in which you
create an initial context and perform a JNDI lookup. For an EJB, you also use
the standard coding pattern in which your class contains methods that call the
EJB home object's `create()` method and the resulting EJB's business methods.

The following example uses a method called `getHelloData()` on a facade class
destination:

    <mx:RemoteObject id="Hello" destination="roDest">
        <mx:method name="getHelloData"/>
    </mx:RemoteObject>

On the Java side, the `getHelloData()` method could encapsulate everything
necessary to call a business method on an EJB. The Java method in the following
example performs the following actions:

- Creates new initial context for calling the EJB

- Performs a JNDI lookup that gets an EJB home object

- Calls the EJB home object's `create()` method

- Calls the EJB's `sayHello()` method

      ...
      public void getHelloData() {
          try{
              InitialContext ctx = new InitialContext();
              Object obj = ctx.lookup("/Hello");
              HelloHome ejbHome = (HelloHome)
              PortableRemoteObject.narrow(obj, HelloHome.class);
              HelloObject ejbObject = ejbHome.create();
              String message = ejbObject.sayHello();
              }
          catch (Exception e);
          }
      ...

## Reserved method names

The Flex remoting library uses the following method names; do not use these
method names as your own method names:

    addHeader()
    addProperty()
    deleteHeader()
    hasOwnProperty()
    isPropertyEnumerable()
    isPrototypeOf()
    registerClass()
    toLocaleString()
    toString()
    unwatch()
    valueOf()
    watch()

Also, do not begin method names with an underscore (`_`) character.

RemoteObject methods (operations) are accessible by simply naming them after the
service variable. However, naming conflicts can occur if an operation name
happens to match a defined method on the service. You can use the following
method in ActionScript on a RemoteObject component to return the operation of
the given name:

    public function getOperation(name:String):Operation

## Serializing between ActionScript and Java

LiveCycle Data Services and BlazeDS serialize data between ActionScript (AMF 3)
and Java and ColdFusion data types in both directions. For information about
ColdFusion data types, see the ColdFusion documentation set.

#### Converting data from ActionScript to Java

When method parameters send data from an application to a Java object, the data
is automatically converted from an ActionScript data type to a Java data type.
When LiveCycle Data Services or BlazeDS searches for a suitable method on the
Java object, it uses further, more lenient conversions to find a match.

Simple data types on the client, such as Boolean and String values, typically
match exactly a remote API. However, Flex attempts some simple conversions when
searching for a suitable method on a Java object.

An ActionScript array can index entries in two ways. A _strict array_ is one in
which all indexes are numbers. An _associative array_ is one in which at least
one index is based on a string. It is important to know which type of array you
are sending to the server, because it changes the data type of parameters that
are used to invoke a method on a Java object. A _dense array_ is one in which
all numeric indexes are consecutive, with no gap, starting from 0 (zero). A
_sparse array_ is one in which there are gaps between the numeric indexes; the
array is treated like an object and the numeric indexes become properties that
are deserialized into a java.util.Map object to avoid sending many null entries.

The following table lists the supported ActionScript (AMF 3) to Java conversions
for simple data types.

<table>
<colgroup>
<col style="width: 33%" />
<col style="width: 33%" />
<col style="width: 33%" />
</colgroup>
<thead>
<tr class="header">
<th><p>ActionScript type (AMF 3)</p></th>
<th><p>Deserialization to Java</p></th>
<th><p>Supported Java type binding</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>Array (dense)</p></td>
<td><p>java.util.List</p></td>
<td><p>java.util.Collection, <em>Object</em>[ ] (native array)</p>
<p>If the type is an interface, it is mapped to the following interface
implementations:</p>
<ul class="incremental">
<li><p>List becomes ArrayList</p></li>
<li><p>SortedSet becomes TreeSet</p></li>
<li><p>Set becomes HashSet</p></li>
<li><p>Collection becomes ArrayList</p></li>
</ul>
<p>A new instance of a custom Collection implementation is bound to that
type.</p></td>
</tr>
<tr class="even">
<td><p>Array (sparse)</p></td>
<td><p>java.util.Map</p></td>
<td><p>java.util.Map</p></td>
</tr>
<tr class="odd">
<td><p>Boolean</p>
<p>String of <samp>"true"</samp> or <samp>"false"</samp></p></td>
<td><p>java.lang.Boolean</p></td>
<td><p>Boolean, boolean, String</p></td>
</tr>
<tr class="even">
<td><p>flash.utils.ByteArray</p></td>
<td><p>byte []</p></td>
<td><p> </p></td>
</tr>
<tr class="odd">
<td><p>flash.utils.IExternalizable</p></td>
<td><p>java.io.Externalizable</p></td>
<td><p> </p></td>
</tr>
<tr class="even">
<td><p>Date</p></td>
<td><p>java.util.Date</p>
<p>(formatted for Coordinated Universal Time (UTC))</p></td>
<td><p>java.util.Date, java.util.Calendar, java.sql.Timestamp,
java.sql.Time, java.sql.Date</p></td>
</tr>
<tr class="odd">
<td><p>int/uint</p></td>
<td><p>java.lang.Integer</p></td>
<td><p>java.lang.Double, java.lang.Long, java.lang.Float,
java.lang.Integer, java.lang.Short, java.lang.Byte,
java.math.BigDecimal, java.math.BigInteger, String, primitive types of
double, long, float, int, short, byte</p></td>
</tr>
<tr class="even">
<td><p>null</p></td>
<td><p>null</p></td>
<td><p>primitives</p></td>
</tr>
<tr class="odd">
<td><p>Number</p></td>
<td><p>java.lang.Double</p></td>
<td><p>java.lang.Double, java.lang.Long, java.lang.Float,
java.lang.Integer, java.lang.Short, java.lang.Byte,
java.math.BigDecimal, java.math.BigInteger, String, 0 (zero)</p>
<p>if <samp>null</samp> is sent, primitive types of double, long, float,
int, short, byte</p></td>
</tr>
<tr class="even">
<td><p>Object (generic)</p></td>
<td><p>java.util.Map</p></td>
<td><p>If a Map interface is specified, creates a java.util.HashMap for
java.util.Map and a new java.util.TreeMap for
java.util.SortedMap.</p></td>
</tr>
<tr class="odd">
<td><p>String</p></td>
<td><p>java.lang.String</p></td>
<td><p>java.lang.String, java.lang.Boolean, java.lang.Number,
java.math.BigInteger, java.math.BigDecimal, char[], any primitive number
type</p></td>
</tr>
<tr class="even">
<td><p>typed Object</p></td>
<td><p>typed Object</p>
<p>When you use <samp>[RemoteClass]</samp> metadata tag that specifies
remote class name. Bean type must have a public no args
constructor.</p></td>
<td><p>typed Object</p></td>
</tr>
<tr class="odd">
<td><p>undefined</p></td>
<td><p>null</p></td>
<td><p><samp>null</samp> for object, default values for
primitives</p></td>
</tr>
<tr class="even">
<td><p>XML</p></td>
<td><p>org.w3c.dom.Document</p></td>
<td><p>org.w3c.dom.Document</p></td>
</tr>
<tr class="odd">
<td><p>XMLDocument</p>
<p>(legacy XML type)</p></td>
<td><p>org.w3c.dom.Document</p></td>
<td><p>org.w3c.dom.Document</p>
<p>You can enable legacy XML support for the XMLDocument type on any
channel defined in the services-config.xml file. This setting is
important only for sending data from the server back to the client; it
controls how org.w3c.dom.Document instances are sent to ActionScript.
For more information, see <a
href="https://web.archive.org/web/20151031200901mp_/http://help.adobe.com/en_US/dataservicesjee/4.6/Developing/WS7dd5c1363434bb70-163458dc12fc0c818a5-7ffb.html">Configuring
AMF serialization on a channel</a>.</p></td>
</tr>
</tbody>
</table>

Primitive values cannot be set to `null` in Java. When passing Boolean and
Number values from the client to a Java object, Flex interprets `null` values as
the default values for primitive types; for example, 0 for double, float, long,
int, short, byte, \u0000 for char, and `false` for Boolean. Only primitive Java
types get default values.

LiveCycle Data Services and BlazeDS handle java.lang.Throwable objects like any
other typed object. They are processed with rules that look for public fields
and bean properties, and typed objects are returned to the client. The rules are
like normal bean rules except that they look for getters for read-only
properties. This lets you get more information from a Java exception. If you
require legacy behavior for Throwable objects, you can set the
`legacy-throwable`property to `true` on a channel; for more information, see
[Configuring AMF serialization on a channel](https://web.archive.org/web/20151031200901mp_/http://help.adobe.com/en_US/dataservicesjee/4.6/Developing/WS7dd5c1363434bb70-163458dc12fc0c818a5-7ffb.html).

You can pass strict arrays as parameters to methods that expect an
implementation of the java.util.Collection or native Java Array APIs.

A Java Collection can contain any number of object types, whereas a Java Array
requires that entries are the same type (for example, java.lang.Object\[ \], and
int\[ \]).

LiveCycle Data Services and BlazeDS also convert ActionScript strict arrays to
appropriate implementations for common Collection API interfaces. For example,
if an ActionScript strict array is sent to the Java object method
`public void addProducts(java.util.Set products)`, LiveCycle Data Services and
BlazeDS convert it to a java.util.HashSet instance before passing it as a
parameter, because HashSet is a suitable implementation of the java.util.Set
interface. Similarly, LiveCycle Data Services and BlazeDS pass an instance of
java.util.TreeSet to parameters typed with the java.util.SortedSet interface.

LiveCycle Data Services and BlazeDS pass an instance of java.util.ArrayList to
parameters typed with the java.util.List interface and any other interface that
extends java.util.Collection. Then these types are sent back to the client as
mx.collections.ArrayCollection instances. If you require normal ActionScript
arrays to be sent back to the client, you must set the `legacy-collection`
element to `true` in the `serialization` section of a channel-definition's
properties. For more information, see
[Configuring AMF serialization on a channel](https://web.archive.org/web/20151031200901mp_/http://help.adobe.com/en_US/dataservicesjee/4.6/Developing/WS7dd5c1363434bb70-163458dc12fc0c818a5-7ffb.html).

#### Explicitly mapping ActionScript and Java objects

For Java objects that LiveCycle Data Services and BlazeDS do not handle
implicitly, values found in public bean properties with get/set methods and
public variables are sent to the client as properties on an 0bject. Private
properties, constants, static properties, read-only properties, and so on are
not serialized. For ActionScript objects, public properties defined with the
get/set accessors and public variables are sent to the server.

LiveCycle Data Services and BlazeDS use the standard Java class,
java.beans.Introspector, to get property descriptors for a JavaBean class. It
also uses reflection to gather public fields on a class. It uses bean properties
in preference to fields. The Java and ActionScript property names should match.
Native Flash Player code determines how ActionScript classes are introspected on
the client.

In the ActionScript class, you use the `[RemoteClass(alias=" ")]` metadata tag
to create an ActionScript object that maps directly to the Java object. The
ActionScript class to which data is converted must be used or referenced in the
MXML file for it to be linked into the SWF file and available at runtime. A good
way to do this is by casting the result object, as the following example shows:

    var result:MyClass = MyClass(event.result);

The class itself should use strongly typed references so that its dependencies
are also linked.

The following examples shows the source code for an ActionScript class that uses
the `[RemoteClass(alias=" ")]` metadata tag:

    package samples.contact {
        [Bindable]
        [RemoteClass(alias="samples.contact.Contact")]
        public class Contact {
            public var contactId:int;

            public var firstName:String;

            public var lastName:String;

            public var address:String;

            public var city:String;

            public var state:String;

            public var zip:String;
        }
    }

You can use the `[RemoteClass]` metadata tag without an alias if you do not map
to a Java object on the server, but you do send back your object type from the
server. Your ActionScript object is serialized to a special Map object when it
is sent to the server, but the object returned from the server to the clients is
your original ActionScript type.

To restrict a specific property from being sent to the server from an
ActionScript class, use the `[Transient]` metadata tag above the declaration of
that property in the ActionScript class.

#### Converting data from Java to ActionScript

An object returned from a Java method is converted from Java to ActionScript.
LiveCycle Data Services and BlazeDS also handle objects found within objects.
LiveCycle Data Services implicitly handles the Java data types in the following
table.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<thead>
<tr class="header">
<th><p>Java type</p></th>
<th><p>ActionScript type (AMF 3)</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>java.lang.String</p></td>
<td><p>String</p></td>
</tr>
<tr class="even">
<td><p>java.lang.Boolean, boolean</p></td>
<td><p>Boolean</p></td>
</tr>
<tr class="odd">
<td><p>java.lang.Integer, int</p></td>
<td><p>int</p>
<p>If value &lt; 0xF0000000 || value &gt; 0x0FFFFFFF, the value is
promoted to Number due to AMF encoding requirements.</p></td>
</tr>
<tr class="even">
<td><p>java.lang.Short, short</p></td>
<td><p>int</p>
<p>If i &lt; 0xF0000000 || i &gt; 0x0FFFFFFF, the value is promoted to
Number.</p></td>
</tr>
<tr class="odd">
<td><p>java.lang.Byte, byte[]</p></td>
<td><p>int</p>
<p>If i &lt; 0xF0000000 || i &gt; 0x0FFFFFFF, the value is promoted to
Number.</p></td>
</tr>
<tr class="even">
<td><p>java.lang.Byte[]</p></td>
<td><p>flash.utils.ByteArray</p></td>
</tr>
<tr class="odd">
<td><p>java.lang.Double, double</p></td>
<td><p>Number</p></td>
</tr>
<tr class="even">
<td><p>java.lang.Long, long</p></td>
<td><p>Number</p></td>
</tr>
<tr class="odd">
<td><p>java.lang.Float, float</p></td>
<td><p>Number</p></td>
</tr>
<tr class="even">
<td><p>java.lang.Character, char</p></td>
<td><p>String</p></td>
</tr>
<tr class="odd">
<td><p>java.lang.Character[], char[]</p></td>
<td><p>String</p></td>
</tr>
<tr class="even">
<td><p>java. math.BigInteger</p></td>
<td><p>String</p></td>
</tr>
<tr class="odd">
<td><p>java.math.BigDecimal</p></td>
<td><p>String</p></td>
</tr>
<tr class="even">
<td><p>java.util.Calendar</p></td>
<td><p>Date</p>
<p>Dates are sent in the Coordinated Universal Time (UTC) time zone.
Clients and servers must adjust time accordingly for time
zones.</p></td>
</tr>
<tr class="odd">
<td><p>java.util.Date</p></td>
<td><p>Date</p>
<p>Dates are sent in the UTC time zone. Clients and servers must adjust
time accordingly for time zones.</p></td>
</tr>
<tr class="even">
<td><p>java.util.Collection (for example, java.util.ArrayList)</p></td>
<td><p>mx.collections.ArrayCollection</p></td>
</tr>
<tr class="odd">
<td><p>java.lang.Object[]</p></td>
<td><p>Array</p></td>
</tr>
<tr class="even">
<td><p>java.util.Map</p></td>
<td><p>Object (untyped). For example, a java.util.Map[] is converted to
an array (of objects).</p></td>
</tr>
<tr class="odd">
<td><p>java.util.Dictionary</p></td>
<td><p>Object (untyped)</p></td>
</tr>
<tr class="even">
<td><p>org.w3c.dom.Document</p></td>
<td><p>XML object</p></td>
</tr>
<tr class="odd">
<td><p>null</p></td>
<td><p>null</p></td>
</tr>
<tr class="even">
<td><p>java.lang.Object (other than previously listed types)</p></td>
<td><p>Typed Object</p>
<p>Objects are serialized by using JavaBean introspection rules and also
include public fields. Fields that are static, transient, or nonpublic,
as well as bean properties that are nonpublic or static, are
excluded.</p></td>
</tr>
</tbody>
</table>

#### Configuring AMF serialization on a channel

You can support legacy AMF type serialization used in earlier versions of Flex
and configure other serialization properties in channel definitions in the
services-config.xml file.

The following table describes the properties you can set in the
`<serialization>` element of a channel definition:

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
<pre><code>&lt;ignore-property-errors&gt;true&lt;/ignore-property-errors&gt;</code></pre>
</div></td>
<td><p>Default value is <samp>true</samp>. Determines if the endpoint
should throw an error when an incoming client object has unexpected
properties that cannot be set on the server object.</p></td>
</tr>
<tr class="even">
<td><div>
<pre><code>&lt;log-property-errors&gt;false&lt;/log-property-errors&gt;</code></pre>
</div></td>
<td><p>Default value is <samp>false</samp>. When <samp>true</samp>,
unexpected property errors are logged.</p></td>
</tr>
<tr class="odd">
<td><div>
<pre><code>&lt;legacy-collection&gt;false&lt;/legacy-collection&gt;</code></pre>
</div></td>
<td><p>Default value is <samp>false</samp>. When <samp>true</samp>,
instances of java.util.Collection are returned as ActionScript arrays.
When <samp>false</samp>, instances of java.util.Collection are returned
as mx.collections.ArrayCollection.</p></td>
</tr>
<tr class="even">
<td><div>
<pre><code>&lt;legacy-map&gt;false&lt;/legacy-map&gt;</code></pre>
</div></td>
<td><p>Default value is <samp>false</samp>. When <samp>true</samp>,
java.util.Map instances are serialized as an ECMA array or associative
array instead of an anonymous object.</p></td>
</tr>
<tr class="odd">
<td><div>
<pre><code>&lt;legacy-xml&gt;false&lt;/legacy-xml&gt;</code></pre>
</div></td>
<td><p>Default value is <samp>false</samp>. When <samp>true</samp>,
org.w3c.dom.Document instances are serialized as flash.xml.XMLDocument
instances instead of intrinsic XML (E4X capable) instances.</p></td>
</tr>
<tr class="even">
<td><div>
<pre><code>&lt;legacy-throwable&gt;false&lt;/legacy-throwable&gt;</code></pre>
</div></td>
<td><p>Default value is <samp>false</samp>. When <samp>true</samp>,
java.lang.Throwable instances are serialized as AMF status-info objects
(instead of normal bean serialization, including read-only
properties).</p></td>
</tr>
<tr class="odd">
<td><div>
<pre><code>&lt;type-marshaller&gt;className&lt;/type-marshaller&gt;</code></pre>
</div></td>
<td><p>Specifies an implementation of flex.messaging.io.TypeMarshaller
that translates an object into an instance of a desired class. Used when
invoking a Java method or populating a Java instance and the type of the
input object from deserialization (for example, an ActionScript
anonymous object is always deserialized as a java.util.HashMap) doesn't
match the destination API (for example, java.util.SortedMap). Thus the
type can be marshalled into the desired type.</p></td>
</tr>
<tr class="even">
<td><div>
<pre><code>&lt;restore-references&gt;false&lt;/restore-references&gt;</code></pre>
</div></td>
<td><p>Default value is <samp>false</samp>. An advanced switch to make
the deserializer keep track of object references when a type translation
has to be made; for example, when an anonymous object is sent for a
property of type java.util.SortedMap, the object is first deserialized
to a java.util.Map as normal, and then translated to a suitable
implementation of SortedMap (such as java.util.TreeMap). If other
objects pointed to the same anonymous object in an object graph, this
setting restores those references instead of creating SortedMap
implementations everywhere. Notice that setting this property to
<samp>true</samp> can slow down performance significantly for large
amounts of data.</p></td>
</tr>
<tr class="odd">
<td><div>
<pre><code>&lt;instantiate-types&gt;true&lt;/instantiate-types&gt;</code></pre>
</div></td>
<td><p>Default value is <samp>true</samp>. Advanced switch that when set
to <samp>false</samp> stops the deserializer from creating instances of
strongly typed objects and instead retains the type information and
deserializes the raw properties in a Map implementation, specifically
flex.messaging.io.ASObject. Notice that any classes under flex.* package
are always instantiated.</p></td>
</tr>
</tbody>
</table>

### Using custom serialization

If the standard mechanisms for serializing and deserializing data between
ActionScript on the client and Java on the server do not meet your needs, you
can write your own serialization scheme. You implement the ActionScript-based
[flash.utils.IExternalizable](https://help.adobe.com/en_US/FlashPlatform/reference/actionscript/3/flash/utils/IExternalizable.html)
interface on the client and the corresponding Java-based java.io.Externalizable
interface on the server.

A typical reason to use custom serialization is to avoid passing all of the
properties of either the client-side or server-side representation of an object
across the network tier. When you implement custom serialization, you can code
your classes so that specific properties that are client-only or server-only are
not passed over the wire. When you use the standard serialization scheme, all
public properties are passed back and forth between the client and the server.

On the client side, the identity of a class that implements the
flash.utils.IExternalizable interface is written in the serialization stream.
The class serializes and reconstructs the state of its instances. The class
implements the `writeExternal()` and `readExternal()` methods of the
IExternalizable interface to get control over the contents and format of the
serialization stream, but not the class name or type, for an object and its
supertypes. These methods supersede the native AMF serialization behavior. These
methods must be symmetrical with their remote counterpart to save the class's
state.

On the server side, a Java class that implements the java.io.Externalizable
interface performs functionality that is analogous to an ActionScript class that
implements the flash.utils.IExternalizable interface.

Note: If precise by-reference serialization is required, do not use types that
implement the IExternalizable interface with the HTTPChannel. When you do this,
references between recurring objects are lost and appear to be cloned at the
endpoint.

The following example shows the complete source code for the client
(ActionScript) version of a Product class that maps to a Java-based Product
class on the server. The client Product implements the IExternalizable
interface, and the server Product implements the Externalizable interface.

    // Product.as
    package samples.externalizable {

    import flash.utils.IExternalizable;
    import flash.utils.IDataInput;
    import flash.utils.IDataOutput;

    [RemoteClass(alias="samples.externalizable.Product")]
    public class Product implements IExternalizable {
        public function Product(name:String=null) {
            this.name = name;
        }

        public var id:int;
        public var name:String;
        public var properties:Object;
        public var price:Number;

        public function readExternal(input:IDataInput):void {
            name = input.readObject() as String;
            properties = input.readObject();
            price = input.readFloat();
        }

        public function writeExternal(output:IDataOutput):void {
            output.writeObject(name);
            output.writeObject(properties);
            output.writeFloat(price);
        }
    }
    }

The client Product uses two kinds of serialization. It uses the standard
serialization that is compatible with the java.io.Externalizable interface and
AMF 3 serialization. The following example shows the `writeExternal()` method of
the client Product, which uses both types of serialization:

    public function writeExternal(output:IDataOutput):void {
        output.writeObject(name);
        output.writeObject(properties);
        output.writeFloat(price);
    }

As the following example shows, the `writeExternal()` method of the server
Product is almost identical to the client version of this method:

    public void writeExternal(ObjectOutput out) throws IOException {
        out.writeObject(name);
        out.writeObject(properties);
        out.writeFloat(price);
    }

In the client Product's `writeExternal()` method, the
`flash.utils.IDataOutput.writeFloat()` method is an example of standard
serialization methods that meet the specifications for the Java
`java.io.DataInput.readFloat()` methods for working with primitive types. This
method sends the `price` property, which is a Float, to the server Product.

The example of AMF 3 serialization in the client Product's `writeExternal()`
method is the call to the `flash.utils.IDataOutput.writeObject()` method, which
maps to the `java.io.ObjectInput.readObject()` method call in the server
Product's `readExternal()` method. The `flash.utils.IDataOutput.writeObject()`
method sends the `properties` property, which is an object, and the name
property, which is a string, to the server Product. This is possible because the
AMFChannel endpoint has an implementation of the java.io.ObjectInput interface
that expects data sent from the `writeObject()` method to be formatted as AMF 3.

In turn, when the `readObject()` method is called in the server Product's
`readExternal()` method, it uses AMF 3 deserialization; this is why the
ActionScript version of the `properties` value is assumed to be of type Map and
`name` is assumed to be of type String.

The following example shows the complete source of the server Product class:

    // Product.java
    package samples.externalizable;

    import java.io.Externalizable;
    import java.io.IOException;
    import java.io.ObjectInput;
    import java.io.ObjectOutput;
    import java.util.Map;

    /**
    * This Externalizable class requires that clients sending and
    * receiving instances of this type adhere to the data format
    * required for serialization.
    */
    public class Product implements Externalizable {
        private String inventoryId;
        public String name;
        public Map properties;
        public float price;

        public Product()
        {
        }

            /**
            * Local identity used to track third-party inventory. This property is
            * not sent to the client because it is server specific.
            * The identity must start with an 'X'.
            */
            public String getInventoryId() {
                return inventoryId;
            }

            public void setInventoryId(String inventoryId) {
                if (inventoryId != null && inventoryId.startsWith("X"))
                {
                    this.inventoryId = inventoryId;
                }
                else
                {
                    throw new IllegalArgumentException("3rd party product
                    inventory identities must start with 'X'");
                }
            }

            /**
             * Deserializes the client state of an instance of ThirdPartyProxy
             * by reading in String for the name, a Map of properties
             * for the description, and
             * a floating point integer (single precision) for the price.
             */
            public void readExternal(ObjectInput in) throws IOException,
                ClassNotFoundException {
                // Read in the server properties from the client representation.
                name = (String)in.readObject();
                properties = (Map)in.readObject();
                price = in.readFloat();
                setInventoryId(lookupInventoryId(name, price));
            }

            /**
             * Serializes the server state of an instance of ThirdPartyProxy
             * by sending a String for the name, a Map of properties
             * String for the description, and a floating point
             * integer (single precision) for the price. Notice that the inventory
             * identifier is not sent to external clients.
             */
            public void writeExternal(ObjectOutput out) throws IOException {
                // Write out the client properties from the server representation.
                out.writeObject(name);
                out.writeObject(properties);
                out.writeFloat(price);
            }

            private static String lookupInventoryId(String name, float price) {
                String inventoryId = "X" + name + Math.rint(price);
                return inventoryId;
            }
    }

The following example shows the server Product's `readExternal()` method:

    public void readExternal(ObjectInput in) throws IOException,
        ClassNotFoundException {
        // Read in the server properties from the client representation.
        name = (String)in.readObject();
        properties = (Map)in.readObject();
        price = in.readFloat();
        setInventoryId(lookupInventoryId(name, price));
        }

The client Product's `writeExternal()` method does not send the `id` property to
the server during serialization because it is not useful to the server version
of the Product object. Similarly, the server Product's `writeExternal()` method
does not send the `inventoryId` property to the client because it is a
server-specific property.

Notice that the names of a Product's properties are not sent during
serialization in either direction. Because the state of the class is fixed and
manageable, the properties are sent in a well-defined order without their names,
and the `readExternal()` method reads them in the appropriate order.
