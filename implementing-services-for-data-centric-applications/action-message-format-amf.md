# Action Message Format (AMF)

Flex uses remote object services and AMF to access services implemented in
ColdFusion, PHP, BlazeDS, and Adobe® LiveCycle® Data Services ES. AMF provides
the messaging for exchanging data between a database and the client application.

ColdFusion, BlazeDS, and Adobe LiveCycle Data Services ES each provide an AMF
framework for remote object services. For services implemented in PHP, Flash
Builder uses the Zend AMF framework.

ColdFusion and PHP services can provide server-side typing. In server-side
typing, the service defines the type of data returned. However, if the service
implementation does not define the return data type, Flash Builder provides
client-side typing. Flash Builder samples data from the service, allowing you to
configure the return type in the client application.
