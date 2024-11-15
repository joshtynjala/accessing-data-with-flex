# Explicit parameter passing and parameter binding

There are two distinct ways to call HTTPService, WebService, and RemoteObject
components: explicit parameter passing and parameter binding. When you use
explicit parameter passing, you provide input to a service in the form of
parameters to an ActionScript function. This way of calling a service closely
resembles the way that you call methods in Java. You cannot use Flex data
validators automatically in combination with explicit parameter passing.

Parameter binding lets you copy data from user-interface controls or models to
request parameters. Parameter binding is available only for data access
components that you declare in MXML. You can apply validators to parameter
values before submitting requests to services. For more information about data
binding and data models, see
[Data binding](https://web.archive.org/web/20150219140329mp_/http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf69084-7fe7.html)
and
[Storing data](https://web.archive.org/web/20150219140329mp_/http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf69084-7ff3.html).
For more information about data validation, see
[Validating Data](https://web.archive.org/web/20150219140329mp_/http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf69084-7ff0.html).

When you use parameter binding, you declare RemoteObject method parameter tags
nested in an `<mx:arguments>` tag under an `<mx:method>` tag, HTTPService
parameter tags nested in an `<mx:request>` tag, or WebService operation
parameter tags nested in an `<mx:request>` tag under an `<mx:operation>` tag.
You use the `send()` method to send the request.

## Explicit parameter passing with RemoteObject and WebService components

The way you use explicit parameter passing with RemoteObject and WebService
components is very similar. The following example shows MXML code for declaring
a RemoteObject component and calling a service by using explicit parameter
passing in the click event listener of a Button control. A ComboBox control
provides data to the service. Simple event listeners handle the service-level
result and fault events.

    <?xml version="1.0"?>
    <!-- fds\rpc\RPCParamPassing.mxml -->
    <mx:Application xmlns:mx="http://www.adobe.com/2006/mxml"
        verticalGap="10">
        <mx:Script>
            <![CDATA[
                import mx.controls.Alert;
                [Bindable]
                public var empList:Object;
            ]]>
        </mx:Script>

        <mx:RemoteObject
            id="employeeRO"
            destination="SalaryManager"
            result="empList=event.result"
            fault="Alert.show(event.fault.faultString, 'Error');"/>

        <mx:ComboBox id="dept" width="150">
            <mx:dataProvider>
                <mx:ArrayCollection>
                    <mx:source>
                        <mx:Object label="Engineering" data="ENG"/>
                        <mx:Object label="Product Management" data="PM"/>
                        <mx:Object label="Marketing" data="MKT"/>
                    </mx:source>
                </mx:ArrayCollection>
            </mx:dataProvider>
        </mx:ComboBox>

        <mx:Button label="Get Employee List" click="employeeRO.getList(dept.selectedItem.data);"/>
    </mx:Application>

## Explicit parameter passing with HTTPService components

Explicit parameter passing with HTTPService components is different than it is
with RemoteObject and WebService components. You always use an HTTPService
component's `send()` method to call a service. This is different from
RemoteObject and WebService components, on which you call methods that are
client-side versions of the methods or operations of the RPC service.

When you use explicit parameter passing, you can specify an object that contains
name-value pairs as a `send()` method parameter. A `send()` method parameter
must be a simple base type; you cannot use complex nested objects because there
is no generic way to convert them to name-value pairs.

If you do not specify a parameter to the `send()` method, the HTTPService
component uses any query parameters specified in an `<mx:request>` tag.

The following examples show two ways to call an HTTP service by using the
`send()` method with a parameter. The second example also shows how to call the
`cancel()` method to cancel an HTTP service call.

    <?xml version="1.0"?>
    <!-- fds\rpc\RPCSend.mxml -->
    <mx:Application xmlns:mx="http://www.adobe.com/2006/mxml">

        <mx:Script>
            <![CDATA[
                public function callService():void {
                    // Cancel all previous pending calls.
                    myService.cancel();

                    var params:Object = new Object();
                    params.param1 = 'val1';
                    myService.send(params);
                }
            ]]>
        </mx:Script>

        <mx:HTTPService
            id="myService"
            destination="Dest"
            useProxy="true"/>
    <!-- HTTP service call with a send() method that takes a variable as its parameter. The value of the variable is an Object. -->
        <mx:Button click="myService.send({param1: 'val1'});"/>

    <!-- HTTP service call with an object as a send() method parameter that provides query parameters. -->
        <mx:Button click="callService();"/>
    </mx:Application>

## Parameter binding with RemoteObject components

When you use parameter binding with RemoteObject components, you always declare
methods in a RemoteObject component's `<mx:method>` tag.

An `<mx:method>` tag can contain an `<mx:arguments>` tag that contains child
tags for the method parameters. The name property of an `<mx:method>` tag must
match one of the service's method names. The order of the argument tags must
match the order of the service's method parameters. You can name argument tags
to match the actual names of the corresponding method parameters as closely as
possible, but this is not necessary.

> **Note:** If argument tags inside an `<mx:arguments>` tag have the same name,
> service calls fail if the remote method is not expecting an array as the only
> input source. There is no warning about this when the application is compiled.

You can bind data to a RemoteObject component's method parameters. You reference
the tag names of the parameters for data binding and validation.

The following example shows a method with two parameters bound to the text
properties of TextInput controls. A PhoneNumberValidator validator is assigned
to `arg1`, which is the name of the first argument tag.

    <?xml version="1.0"?>
    <!-- fds\rpc\ROParamBind1.mxml. -->
    <mx:Application xmlns:mx="http://www.adobe.com/2006/mxml">
        <mx:RemoteObject
            id="ro"
            destination="roDest">

            <mx:method name="setData">
                <mx:arguments>
                    <arg1>{text1.text}</arg1>
                    <arg2>{text2.text}</arg2>
                </mx:arguments>
            </mx:method>
        </mx:RemoteObject>
        <mx:TextInput id="text1"/>
        <mx:TextInput id="text2"/>

        <mx:PhoneNumberValidator source="{ro.setData.arguments}" property="arg1"/>
    </mx:Application>

Flex sends the argument tag values to the method in the order that the MXML tags
specify.

The following example uses parameter binding in a RemoteObject component's
`<mx:method>` tag to bind the data of a selected ComboBox item to the
`employeeRO.getList` operation when the user clicks a Button control. When you
use parameter binding, you call a service by using the `send()` method with no
parameters.

    <?xml version="1.0"?>
    <!-- fds\rpc\ROParamBind2.mxml -->
    <mx:Application xmlns:mx="http://www.adobe.com/2006/mxml" verticalGap="10">
        <mx:Script>
            <![CDATA[
                import mx.controls.Alert;
                    import mx.utils.ArrayUtil;
            ]]>
        </mx:Script>
        <mx:RemoteObject
            id="employeeRO"
            destination="roDest"
            showBusyCursor="true"
            fault="Alert.show(event.fault.faultString, 'Error');">
            <mx:method name="getList">
                <mx:arguments>
                    <deptId>{dept.selectedItem.data}</deptId>
                </mx:arguments>
            </mx:method>
        </mx:RemoteObject>
        <mx:ArrayCollection id="employeeAC"
            source="{ArrayUtil.toArray(employeeRO.getList.lastResult)}"/>

        <mx:HBox>
            <mx:Label text="Select a department:"/>
            <mx:ComboBox id="dept" width="150">

                <mx:dataProvider>
                    <mx:ArrayCollection>
                        <mx:source>
                            <mx:Object label="Engineering" data="ENG"/>
                            <mx:Object label="Product Management" data="PM"/>
                            <mx:Object label="Marketing" data="MKT"/>
                        </mx:source>
                    </mx:ArrayCollection>
                </mx:dataProvider>
            </mx:ComboBox>
                <mx:Button label="Get Employee List"
                    click="employeeRO.getList.send()"/>
        </mx:HBox>
        <mx:DataGrid dataProvider="{employeeAC}" width="100%">
            <mx:columns>
                <mx:DataGridColumn dataField="name" headerText="Name"/>
                <mx:DataGridColumn dataField="phone" headerText="Phone"/>
                <mx:DataGridColumn dataField="email" headerText="Email"/>
            </mx:columns>
        </mx:DataGrid>
    </mx:Application>

If you are unsure whether the result of a service call contains an array or an
individual object, you can use the `toArray()` method of the mx.utils.ArrayUtil
class to convert it to an array, as this example shows. If you pass the
`toArray()` method to an individual object, it returns an array with that object
as the only Array element. If you pass an array to the method, it returns the
same array. For information about working with ArrayCollection objects, see
[Data providers and collections](https://web.archive.org/web/20150219140329mp_/http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf69084-7fb8.html).

## Parameter binding with HTTPService components

When an HTTP service takes query parameters, you can declare them as child tags
of an `<mx:request>` tag. The names of the tags must match the names of the
query parameters that the service expects.

The following example uses parameter binding in an HTTPService component's
`<mx:request>` tag to bind the data of a selected ComboBox item to the
`employeeSrv` request when the user clicks a Button control. When you use
parameter binding, you call a service by using the `send()` method with no
parameters. This example shows a `url` property on the HTTPService component,
but the way you call a service is the same whether you connect to the service
directly or go through a destination.

    <?xml version="1.0"?>
    <!-- fds\rpc\HttpServiceParamBind.mxml. Compiles -->
    <mx:Application xmlns:mx="http://www.adobe.com/2006/mxml" verticalGap="20">
        <mx:Script>
            <![CDATA[
                import mx.utils.ArrayUtil;
            ]]>
        </mx:Script>

        <mx:HTTPService
            id="employeeSrv"
            url="employees.jsp">
            <mx:request>
                <deptId>{dept.selectedItem.data}</deptId>
            </mx:request>
        </mx:HTTPService>
        <mx:ArrayCollection
            id="employeeAC"
            source=
                "{ArrayUtil.toArray(employeeSrv.lastResult.employees.employee)}"/>
        <mx:HBox>
            <mx:Label text="Select a department:"/>
            <mx:ComboBox id="dept" width="150">
                <mx:dataProvider>
                    <mx:ArrayCollection>
                        <mx:source>
                            <mx:Object label="Engineering" data="ENG"/>
                            <mx:Object label="Product Management" data="PM"/>
                            <mx:Object label="Marketing" data="MKT"/>
                        </mx:source>
                    </mx:ArrayCollection>
                </mx:dataProvider>
            </mx:ComboBox>
            <mx:Button label="Get Employee List" click="employeeSrv.send();"/>
        </mx:HBox>
        <mx:DataGrid dataProvider="{employeeAC}"
            width="100%">
            <mx:columns>
                <mx:DataGridColumn dataField="name" headerText="Name"/>
                <mx:DataGridColumn dataField="phone" headerText="Phone"/>
                <mx:DataGridColumn dataField="email" headerText="Email"/>
            </mx:columns>
        </mx:DataGrid>
    </mx:Application>

If you are unsure whether the result of a service call contains an array or an
individual object, you can use the `toArray()` method of the mx.utils.ArrayUtil
class to convert it to an array, as the previous example shows. If you pass the
`toArray()` method to an individual object, it returns an array with that object
as the only Array element. If you pass an array to the method, it returns the
same array. For information about working with ArrayCollection objects, see
[Data providers and collections](https://web.archive.org/web/20150219140329mp_/http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf69084-7fb8.html).

## Parameter binding with WebService components

When you use parameter binding with a WebService component, you always declare
operations in the WebService component's `<mx:operation>` tags. An
`<mx:operation>` tag can contain an `<mx:request>` tag that contains the XML
nodes that the operation expects. The name property of an `<mx:operation>` tag
must match one of the web service operation names.

You can bind data to parameters of web service operations. You reference the tag
names of the parameters for data binding and validation.

The following example uses parameter binding in a WebService component's
`<mx:operation>` tag to bind the data of a selected ComboBox item to the
`employeeWS.getList` operation when the user clicks a Button control. The
`<deptId>` tag corresponds directly to the getList operation's `deptId`
parameter. When you use parameter binding, you call a service by using the
`send()` method with no parameters. This example shows a destination property on
the WebService component, but the way you call a service is the same whether you
connect to the service directly or go through a destination.

    <?xml version="1.0"?>
    <!-- fds\rpc\WebServiceParamBind.mxml -->
    <mx:Application xmlns:mx="http://www.adobe.com/2006/mxml" verticalGap="10">
        <mx:Script>
            <![CDATA[
                import mx.utils.ArrayUtil;
                import mx.controls.Alert;
            ]]>
        </mx:Script>

        <mx:WebService
            id="employeeWS"
            destination="wsDest"
            showBusyCursor="true"
            fault="Alert.show(event.fault.faultString)">
            <mx:operation name="getList">
                <mx:request>
                    <deptId>{dept.selectedItem.data}</deptId>
                </mx:request>
            </mx:operation>
        </mx:WebService>
        <mx:ArrayCollection
            id="employeeAC"
            source="{ArrayUtil.toArray(employeeWS.getList.lastResult)}"/>
        <mx:HBox>
            <mx:Label text="Select a department:"/>
            <mx:ComboBox id="dept" width="150">
                <mx:dataProvider>
                    <mx:ArrayCollection>
                        <mx:source>
                            <mx:Object label="Engineering" data="ENG"/>
                            <mx:Object label="Product Management" data="PM"/>
                            <mx:Object label="Marketing" data="MKT"/>
                        </mx:source>
                    </mx:ArrayCollection>
                </mx:dataProvider>
            </mx:ComboBox>
                <mx:Button label="Get Employee List"
                    click="employeeWS.getList.send()"/>
        </mx:HBox>
        <mx:DataGrid dataProvider="{employeeAC}" width="100%">
            <mx:columns>
                    <mx:DataGridColumn dataField="name" headerText="Name"/>
                    <mx:DataGridColumn dataField="phone" headerText="Phone"/>
                    <mx:DataGridColumn dataField=" to email" headerText="Email"/>
            </mx:columns>
        </mx:DataGrid>
    </mx:Application>

You can also manually specify an entire SOAP request body in XML with all of the
correct namespace information defined in an `<mx:request>` tag. To do so, set
the value of the `format` attribute of the `<mx:request>` tag to `xml`, as the
following example shows:

    <?xml version="1.0"?>
    <!-- fds\rpc\WebServiceSOAPRequest.mxml -->
    <mx:Application xmlns:mx="http://www.adobe.com/2006/mxml" verticalGap="10">
        <mx:WebService id="ws" wsdl="http://api.google.com/GoogleSearch.wsdl"
            useProxy="true">
            <mx:operation name="doGoogleSearch" resultFormat="xml">
                <mx:request format="xml">
                    <ns1:doGoogleSearch xmlns:ns1="urn:GoogleSearch"
                        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                        xmlns:xsd="http://www.w3.org/2001/XMLSchema">
                        <key xsi:type="xsd:string">XYZ123</key>
                        <q xsi:type="xsd:string">Balloons</q>
                        <start xsi:type="xsd:int">0</start>
                        <maxResults xsi:type="xsd:int">10</maxResults>
                        <filter xsi:type="xsd:boolean">true</filter>
                        <restrict xsi:type="xsd:string"/>
                        <safeSearch xsi:type="xsd:boolean">false</safeSearch>
                        <lr xsi:type="xsd:string" />
                        <ie xsi:type="xsd:string">latin1</ie>
                        <oe xsi:type="xsd:string">latin1</oe>
                    </ns1:doGoogleSearch>
                </mx:request>
            </mx:operation>
        </mx:WebService>
    </mx:Application>
