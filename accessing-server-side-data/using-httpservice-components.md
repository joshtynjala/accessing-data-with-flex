# Using HTTPService components

You can use an HTTPService component with any kind of server-side technology,
including PHP pages, ColdFusion Pages, Javaserver Pages (JSPs), Java servlets,
Ruby on Rails, and Microsoft ASP pages. Additionally, you use HTTPService to
access REST-based web services.

For API reference information about the HTTPService component, see
[mx.rpc.http.mxml.HTTPService](https://help.adobe.com/en_US/FlashPlatform/reference/actionscript/3/mx/rpc/http/mxml/HTTPService.html).

## Working with PHP and SQL data

You can use an HTTPService component with PHP and a SQL database management
system to display the results of a database query in an application. You can
also use the component to insert, update, and delete data in a database. You can
call a PHP page with GET or POST to perform a database query. You can then
format the query result data in an XML structure and return the XML structure to
the application in the HTTP response. When the result has been returned to the
application, you can display it in one or more user interface controls.

#### MXML code

The application in the following example calls a PHP page with the POST method.
The PHP page queries a MySQL database table called users. It formats the query
results as XML and returns the XML to the application, where it is bound to the
`dataProvider` property of a DataGrid control and displayed in the DataGrid
control. The application also sends the user name and email address of new users
to the PHP page, which performs an insert into the user database table.

    <?xml version="1.0" encoding="utf-8"?>
    <s:Application xmlns:fx="http://ns.adobe.com/mxml/2009"
            xmlns:s="library://ns.adobe.com/flex/spark"
            xmlns:mx="library://ns.adobe.com/flex/mx" minWidth="955" minHeight="600"
            creationComplete="send_data()">
        <fx:Declarations>
            <s:HTTPService id="userRequest" url="http://myserver/myproj/request_post2.php"
                                                        useProxy="false" method="POST">
                <mx:request xmlns="">
                    <username>{username.text}</username>
                    <emailaddress>{emailaddress.text}</emailaddress>
                </mx:request>
            </s:HTTPService>
        </fx:Declarations>
        <fx:Script>
            <![CDATA[
            private function send_data():void {
            userRequest.send();
            }
            ]]>
        </fx:Script>
        <mx:Form x="20" y="10" width="300">
            <mx:FormItem>
                <s:Label text="Username" />
                <s:TextInput id="username"/>
            </mx:FormItem>
            <mx:FormItem>
                <s:Label text="Email Address" />
                <s:TextInput id="emailaddress"/>
            </mx:FormItem>
            <s:Button label="Submit" click="send_data()"/>
        </mx:Form>
        <mx:DataGrid id="dgUserRequest" x="20" y="160"
            dataProvider="{userRequest.lastResult.users.user}">
            <mx:columns>
                <mx:DataGridColumn headerText="User ID" dataField="userid"/>
                <mx:DataGridColumn headerText="User Name" dataField="username"/>
            </mx:columns>
        </mx:DataGrid>
        <s:TextInput x="20" y="340" id="selectedemailaddress" text="{dgUserRequest.selectedItem.emailaddress}"/>

    </s:Application>

The HTTPService's `send()` method makes the call to the PHP page. This call is
made in the `send_data()` method in the Script block of the MXML file.

The `resultFormat` property of the HTTPService component is set to `object`, so
the data is sent back to the application as a graph of ActionScript objects.
This is the default value for the `resultFormat` property. Alternatively, you
can use a `resultFormat` of `e4x` to return data as an XMLList object on which
you can perform ECMAScript for XML (E4X) operations. Switching the
`resultFormat` property to `e4x` requires the following minor changes to the
MXML code.

> **Note:** If the result format is `e4x`, you do not include the root node of
> the XML structure in the dot notation when binding to the DataGrid.

The XML returned in this example contains no namespace information. For
information about working with XML that does contain namespaces, see
[Handling results as XML with the e4x result format](../accessing-server-side-data/handling-service-results.md#handling-results-as-xml-with-the-e4x-result-format).

    ...
    <s:HTTPService id="userRequest" url="http://myserver/myproj/request_post2.php"
                                useProxy="false" method="POST" resultFormat="e4x">
    ...
    <mx:DataGrid id="dgUserRequest" x="22" y="150"
        dataProvider="{userRequest.lastResult.user}">
    ...

When using the `e4x` result format, you can optionally bind the `lastResult`
property to an XMLListCollection object and then bind that object to the
`DataGrid.dataProvider` property, as the following code snippet shows:

    <fx:Declarations>
    ...
        <mx:XMLListCollection id="xc"
             source="{userRequest.lastResult.user}"/>
    ...
    </fx:Declarations>
    ...
        <mx:DataGrid id="dgUserRequest" x="22" y="128" dataProvider="{xc}">
    ...

#### MySQL database script

The PHP code for this application uses a database table called users in a MySQL
database called sample. The following MySQL script creates the table:

    CREATE TABLE `users` (
    `userid` int(10) unsigned NOT NULL auto_increment,
    `username` varchar(255) collate latin1_general_ci NOT NULL,
    `emailaddress` varchar(255) collate latin1_general_ci NOT NULL,
    PRIMARY KEY (`userid`)
    ) ENGINE=MyISAM DEFAULT CHARSET=latin1 COLLATE=latin1_general_ci AUTO_INCREMENT=3 ;

#### PHP code

This application calls the following PHP page. This PHP code performs SQL
database inserts and queries, and returns query results to the application in an
XML structure.

    <?php
    define( "DATABASE_SERVER", "servername" );
    define( "DATABASE_USERNAME", "username" );
    define( "DATABASE_PASSWORD", "password" );
    define( "DATABASE_NAME", "sample" );

    //connect to the database.
    $mysql = mysql_connect(DATABASE_SERVER, DATABASE_USERNAME, DATABASE_PASSWORD);

    mysql_select_db( DATABASE_NAME );

    // Quote variable to make safe
    function quote_smart($value)
    {
        // Stripslashes
        if (get_magic_quotes_gpc()) {
        $value = stripslashes($value);
        }
        // Quote if not integer
        if (!is_numeric($value)) {
        $value = "'" . mysql_real_escape_string($value) . "'";
        }
        return $value;
    }

    if( $_POST["emailaddress"] AND $_POST["username"])
    {
    //add the user
    $Query = sprintf("INSERT INTO users VALUES ('', %s, %s)",
            quote_smart($_POST['username']), quote_smart($_POST['emailaddress']));

    $Result = mysql_query( $Query );
    }

    //return a list of all the users
    $Query = "SELECT * from users";
    $Result = mysql_query( $Query );

    $Return = "<users>";

    while ( $User = mysql_fetch_object( $Result ) )
    {
    $Return .= "<user><userid>".$User->userid."</userid><username>".
                $User->username."</username><emailaddress>".
                $User->emailaddress."</emailaddress></user>";
    }
    $Return .= "</users>";
    mysql_free_result( $Result );
    print ($Return)
    ?>

## Working with ColdFusion and SQL data

You can use an HTTPService component with a ColdFusion page and a SQL database
management system to display the results of a database query in an application.
You can also use the component to insert, update, and delete data in a database.
You call a ColdFusion page with GET or POST to perform a database query. You
then format the query result data in an XML structure and return the XML
structure to the application in the HTTP response. When the result has been
returned to the application, you display it in one or more user interface
controls.

#### MXML code

The application in the following example calls a ColdFusion page with the POST
method. The ColdFusion page queries a MySQL database table called users. It
formats the query results as XML and returns the XML to the application, where
it is bound to the `dataProvider` property of a DataGrid control and displayed
in the DataGrid control. The application also sends the user name and email
address of new users to the ColdFusion page, which performs an insert into the
user database table.

    <?xml version="1.0" encoding="utf-8"?>
    <s:Application xmlns:fx="http://ns.adobe.com/mxml/2009"
            xmlns:s="library://ns.adobe.com/flex/spark"
            xmlns:mx="library://ns.adobe.com/flex/mx" minWidth="955" minHeight="600"
            creationComplete="userRequest.send()">

        <fx:Declarations>
        <s:HTTPService id="userRequest" url="http://server:8500/flexapp/returncfxml.cfm"
            useProxy="false" method="POST">
            <mx:request xmlns="">
                <username>{username.text}</username>
                <emailaddress>{emailaddress.text}</emailaddress>
            </mx:request>
        </s:HTTPService>
        </fx:Declarations>
        <mx:Form x="22" y="10" width="300">
            <mx:FormItem>
                <s:Label text="Username" />
                <s:TextInput id="username"/>
            </mx:FormItem>
            <mx:FormItem>
                <s:Label text="Email Address" />
                <s:TextInput id="emailaddress"/>
            </mx:FormItem>
            <s:Button label="Submit" click="userRequest.send()"/>
        </mx:Form>
        <mx:DataGrid id="dgUserRequest" x="22" y="128"
            dataProvider="{userRequest.lastResult.users.user}">
            <mx:columns>
                <mx:DataGridColumn headerText="User ID" dataField="userid"/>
                <mx:DataGridColumn headerText="User Name" dataField="username"/>
            </mx:columns>
        </mx:DataGrid>
        <s:TextInput x="22" y="300" id="selectedemailaddress" text="{dgUserRequest.selectedItem.emailaddress}"/>
    </s:Application>

The HTTPService's `send()` method makes the call to the ColdFusion page. This
call is made in the `send_data()` method in the Script block of the MXML file.

The `resultFormat` property of the HTTPService component is set to `object`, so
the data is sent back to the application as a graph of ActionScript objects.
This is the default value for the `resultFormat` property. Alternatively, you
can use a result format of `e4x` to return data as an XMLList object on which
you can perform ECMAScript for XML (E4X) operations. Switching the
`resultFormat` property to `e4x` requires the following minor changes to the
MXML code.

> **Note:** If the result format is `e4x`, you do not include the root node of
> the XML structure in the dot notation when binding to the DataGrid.

The XML returned in this example contains no namespace information. For
information about working with XML that does contain namespaces, see
[Handling results as XML with the e4x result format](../accessing-server-side-data/handling-service-results.md#handling-results-as-xml-with-the-e4x-result-format).

    ...
    <s:HTTPService id="userRequest" url="http://myserver:8500/flexapp/returncfxml.cfm"
                            useProxy="false" method="POST" resultFormat="e4x">
    ...
    <mx:DataGrid id="dgUserRequest" x="22" y="128"
        dataProvider="{userRequest.lastResult.user}">
    ...

When using the `e4x` result format, you can optionally bind the `lastResult`
property to an XMLListCollection object and then bind that object to the
DataGrid `dataProvider` property, as the following code snippet shows:

    <fx:Declarations>
    ...
        <mx:XMLListCollection id="xc"
             source="{userRequest.lastResult.user}"/>
    ...
    </fx:Declarations>
    ...
        <mx:DataGrid id="dgUserRequest" x="22" y="128" dataProvider="{xc}">
    ...

#### SQL script

The ColdFusion code for this application uses a database table called users in a
MySQL database called sample. The following MySQL script creates the table:

    CREATE TABLE `users` (
    `userid` int(10) unsigned NOT NULL auto_increment,
    `username` varchar(255) collate latin1_general_ci NOT NULL,
    `emailaddress` varchar(255) collate latin1_general_ci NOT NULL,
    PRIMARY KEY (`userid`)
    ) ENGINE=MyISAM DEFAULT CHARSET=latin1 COLLATE=latin1_general_ci AUTO_INCREMENT=3 ;

#### ColdFusion code

The application that is listed in the Working with ColdFusion and SQL data
section calls the following ColdFusion application, `returncfxml.cfm`. This
ColdFusion code performs SQL database inserts and queries, and returns query
results to the application. The ColdFusion page uses the `cfquery` tag to insert
data into the database and query the database, and it uses the `cfxml` tag to
format the query results in an XML structure.

    <!--- returncfxml.cfm --->

    <cfprocessingdirective pageencoding = "utf-8" suppressWhiteSpace = "Yes">
    <cfif isDefined("username") and isDefined("emailaddress") and username NEQ "">
        <cfquery name="addempinfo" datasource="sample">
        INSERT INTO users (username, emailaddress) VALUES (
        <cfqueryparam value="#username#" cfsqltype="CF_SQL_VARCHAR" maxlength="255">,
            <cfqueryparam value="#emailaddress#" cfsqltype="CF_SQL_VARCHAR" maxlength="255"> )
    </cfquery>
    </cfif>
    <cfquery name="alluserinfo" datasource="sample">
        SELECT userid, username, emailaddress FROM users
    </cfquery>
    <cfxml variable="userXML">
        <users>
            <cfloop query="alluserinfo">
                <cfoutput>
                    <user>
                        <userid>#toString(userid)#</userid>
                         <username>#username#</username>
                        <emailaddress>#emailaddress#</emailaddress>
                    </user>
            </cfoutput>
            </cfloop>
        </users>
    </cfxml>
    <cfoutput>#userXML#</cfoutput>
    </cfprocessingdirective>

## Working with Javaserver Pages

You can use a Flex HTTPService component with a JSP page and a SQL database
management system to display the results of a database query in an application.
You can also use the component to insert, update, and delete data in a database.
You call a JSP page with GET or POST to perform a database query. You then
format the query result data in an XML structure and return the XML structure to
the application in the HTTP response. When the result has been returned to the
application, you display it in one or more user interface controls.

#### MXML code

The application in the following example calls a JSP page that retrieves data
from a SQL database. It formats database query results as XML and returns the
XML to the application, where it is bound to the `dataProvider` property of a
DataGrid control and displayed in the DataGrid control.

    <s:Application xmlns:fx="http://ns.adobe.com/mxml/2009"
            xmlns:s="library://ns.adobe.com/flex/spark"
            xmlns:mx="library://ns.adobe.com/flex/mx" minWidth="955" minHeight="600">

        <fx:Declarations>
            <s:HTTPService id="srv" url="catalog.jsp"/>
        </fx:Declarations>

        <mx:DataGrid dataProvider="{srv.lastResult.catalog.product}"
                                        width="100%" height="100%"/>

        <s:Button label="Get Data" click="srv.send()"/>

    </mx:Application>

The HTTPService's `send()` method makes the call to the JSP page. This call is
made in the `click` event of the Button in the MXML file.

The `resultFormat` property of the HTTPService component is set to `object`, so
the data is sent back to the application as a graph of ActionScript objects.
This is the default value for the `resultFormat` property. Alternatively, you
can use a result format of `e4x` to return data as an XMLList object on which
you can perform ECMAScript for XML (E4X) operations. Switching the
`resultFormat` property to `e4x` requires the following minor changes to the
MXML code.

> **Note:** If the result format is `e4x`, you do not include the root node of
> the XML structure in the dot notation when binding to the DataGrid.

The XML returned in this example contains no namespace information. For
information about working with XML that does contain namespaces, see
[Handling results as XML with the e4x result format](../accessing-server-side-data/handling-service-results.md#handling-results-as-xml-with-the-e4x-result-format).

    ...
        <s:HTTPService id="srv" url="catalog.jsp" resultFormat="e4x"/>
    ...
        <mx:DataGrid dataProvider="{srv.lastResult.product}" width="100%" height="100%"/>

When using the e4x result format, you can optionally bind the `lastResult`
property to an XMLListCollection object and then bind that object to the
DataGrid.dataProvider property:

    <fx:Declarations>
    ...
        <mx:XMLListCollection id="xc"
             source="{userRequest.lastResult.user}"/>
    ...
    </fx:Declarations>
    ...
        <mx:DataGrid id="dgUserRequest" x="22" y="128" dataProvider="{xc}">
    ...

#### JSP code

The following example shows the JSP page used in this application. This JSP page
does not call a database directly. It gets its data from a Java class called
ProductService, which in turn uses a Java class called Product to represent
individual products.

    <%@page import="flex.samples.product.ProductService,
                    flex.samples.product.Product,
                    java.util.List"%>
    <?xml version="1.0" encoding="utf-8"?>
    <catalog>
    <%
        ProductService srv = new ProductService();
        List list = null;
        list = srv.getProducts();
        Product product;
        for (int i=0; i<list.size(); i++)
        {
            product = (Product) list.get(i);
    %>
    <product productId="<%= product.getProductId()%>">
    <name><%= product.getName() %></name>
    <description><%= product.getDescription() %></description>
    <price><%= product.getPrice() %></price>
    <image><%= product.getImage() %></image>
    <category><%= product.getCategory() %></category>
    <qtyInStock><%= product.getQtyInStock() %></qtyInStock>
    </product>
    <%
        }
    %>
    </catalog>

## Calling HTTP services in ActionScript

The following example shows an HTTP service call in an ActionScript script
block. Calling the `useHTTPService()` method declares the service, sets the
destination, sets up result and fault event listeners, and calls the service's
`send()` method.

    <?xml version="1.0"?>
    <!-- fds\rpc\HttpServiceInAS.mxml. Compiles -->
    <mx:Application xmlns:mx="http://www.adobe.com/2006/mxml" verticalGap="10">
        <mx:Script>
            <![CDATA[
                import mx.controls.Alert;
                import mx.rpc.http.HTTPService;
                import mx.rpc.events.ResultEvent;
                import mx.rpc.events.FaultEvent;

                private var service:HTTPService

                public function useHttpService(parameters:Object):void {
                    service = new HTTPService();
                    service.url = "catalog.jsp";
                    service.method = "POST";
                    service.addEventListener("result", httpResult);
                    service.addEventListener("fault", httpFault);
                    service.send(parameters);
                }

                public function httpResult(event:ResultEvent):void {
                    var result:Object = event.result;
                //Do something with the result.
                }

                public function httpFault(event:FaultEvent):void {
                    var faultstring:String = event.fault.faultString;
                    Alert.show(faultstring);
                }
            ]]>
        </mx:Script>
    </mx:Application>
