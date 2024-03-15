# Using a single server instance

After you connect to a data service, each application in a project can access
the service. By default, each application creates its own service instance when
accessing the server.

You can modify this behavior so that there is only a single service instance in
a project. Each application in the project accesses the same service instance.
Typically, you create a single server instance when you want to coordinate the
access of data from multiple applications.

You can specify to access a single service instance on a project basis or as a
preference for all projects.

## Access a single server instance for a project

1.  Select Project \> Properties \> Data/Services

2.  Enable the check box for using a single server instance. Click OK.

## Specify single server instance as a preference

1.  Open the Preferences dialog.

2.  Select Flash Builder \> Data/Services

3.  Enable the check box for using a single server instance. Click OK.
