# Client-side and server-side typing

In Flex, a client application uses the data type of data returned from a service
call in methods that access and display the data.

However, the services examples listed below return untyped data.

- [Implementing ColdFusion services](./implementing-coldfusion-services.md)

- [Implementing PHP services](./implementing-php-services.md)

- [Example implementing services from multiple sources](./example-implementing-services-from-multiple-sources.md)

For example, for the `getAllEmployees()` operation, the service returns an array
of untyped objects that represent records from the database. Flash Builder
provides tools that enable client-side typing. Using Flash Builder tools, you
introspect the returned data and define a custom data type for the data.

For the returned object of employee records, you define the custom data type,
Employee. Each column of the record becomes a property of the Employee data
type.

Using the Employee custom data type, the client application can access the
returned data and display it properly in the client application.

Flash Builder can also access services that implement server-side typing. For
examples of server-side typing, see
[Flash Builder server-side type examples](https://web.archive.org/web/20160904174503mp_/http://www.adobe.com/go/learn_flex4_serversidetype_en).
