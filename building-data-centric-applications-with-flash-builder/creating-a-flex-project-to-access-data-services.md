# Creating a Flex project to access data services

Flex accesses data services as a remote object, an HTTP (REST-style) service, or
a SOAP web service.

You use a remote object to access the following types of data services:

- ColdFusion services

- AMF-based PHP services

- BlazeDS

- LiveCycle Data Services

For information on using the LiveCycle Service Discovery wizard, see <a
href="https://web.archive.org/web/20151028044846mp_/http://www.adobe.com/go/learn_flex4_lcservicediscovery_en/">Using
LiveCycle Discovery</a>.

For any service accessed as a remote object, create a Flex project configured
for the appropriate application server type. The New Flex Project wizard guides
you through configuring a project for the application server types listed below:

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<thead>
<tr class="header">
<th><p>Server Type</p></th>
<th><p>Remote Object Services Supported</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>PHP</p></td>
<td><div>
<ul class="incremental">
<li><p>AMF-based PHP services</p></li>
</ul>
</div></td>
</tr>
<tr class="even">
<td><p>ColdFusion</p></td>
<td><div>
<ul class="incremental">
<li><p>ColdFusion Flash Remoting</p></li>
<li><p>BlazeDS</p></li>
<li><p>LiveCycle Data Services</p></li>
</ul>
</div></td>
</tr>
<tr class="odd">
<td><p>J2EE</p></td>
<td><div>
<ul class="incremental">
<li><p>BlazeDS</p></li>
<li><p>LiveCycle Data Services</p></li>
</ul>
</div></td>
</tr>
</tbody>
</table>

You can connect to HTTP (REST-style) services and SOAP web services from any
Flex project configuration, including projects that do not specify a server
technology.

A project configured to access a remote object can only access a remote object
service for which it is configured. For example, you cannot access an AMF-based
PHP service from a project configured for ColdFusion. However, you could connect
to a PHP service from this project if you connect to the PHP service as a web
service or HTTP service.

## Changing the server type of a project

Flash Builder notifies you if you attempt to access a service for which a Flex
project is not configured. If the Flex project does not specify the correct
server configuration, Flash Builder provides a link to the Project Properties
dialog. In the Project Properties dialog, you can configure the project to
access the data service. For example, Flash Builder warns you if you attempt to
access an AMF-based PHP service from a project that does not specify a server
configuration.

If the Flex project has previously been configured to access a different type of
service, configure a new Flex project or change the configuration of the current
project. If you change the server configuration of a project, then you can no
longer access any services previously configured. For example, if you change a
project configuration from ColdFusion to PHP, any ColdFusion services you access
in the project are no longer available.

If you want to access different types of services from the same project, you can
access the services as either HTTP services or web services.

## Cross-domain policy file

A cross-domain policy file is necessary when accessing services that are on a
different domain from the SWF file for the application. Typically, AMF-based
services do not need a cross-domain policy file because these services are on
the same domain as the application.

More Help topics

[Accessing data services overview](../accessing-data-services-overview.md)

![](../img/flashbuilderLinkIndicator.png)  <a
href="https://web.archive.org/web/20151028044846mp_/http://help.adobe.com/en_US/flashbuilder/using/WSe4e4b720da9dedb5-6f0dd08a12e51bbb7fa-7ff7.html"
title="http://help.adobe.com/en_US/flashbuilder/using/WSe4e4b720da9dedb5-6f0dd08a12e51bbb7fa-7ff7.html">Manage
projects</a>

![](../img/flashbuilderLinkIndicator.png)  <a
href="https://web.archive.org/web/20151028044846mp_/http://help.adobe.com/en_US/flashbuilder/using/WSe4e4b720da9dedb5-1a92eab212e75b9d8b2-7f9d.html"
title="http://help.adobe.com/en_US/flashbuilder/using/WSe4e4b720da9dedb5-1a92eab212e75b9d8b2-7f9d.html">Access
data services</a>

![](../img/flexLinkIndicator.png)  <a
href="https://web.archive.org/web/20151028044846mp_/http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf69084-7f2b.html"
title="http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf69084-7f2b.html">Using
cross-domain policy files</a>
