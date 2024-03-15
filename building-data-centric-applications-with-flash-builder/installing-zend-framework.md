# Installing Zend Framework

When initially accessing PHP services, Flash Builder determines if the supported
version of the Zend Framework is installed. If the supported version of the Zend
Framework is not found, Flash Builder prompts you to confirm installation of the
Zend Framework. If you accept, then Flash Builder installs a minimal version of
the Zend Framework. If you decline, then manually install the Zend Framework if
you are going to access PHP services.

## Default Flash Builder installation

Flash Builder installs the Zend Framework into a `ZendFramework` folder in the
root directory of your web server.

    <web root>/ZendFramework/

For Flex projects that access PHP services, Flash Builder creates the following
configuration files in the project output folder:

- `amf_config.ini`

- `gateway.php`

## Production servers

For productions servers, Adobe recommends that you move the `ZendFramework`
folder outside the web root. Update the `zend_path` variable defined in
`amf_config.ini`.

If the `zend_path` variable is commented out, uncomment the `zend_path`
variable. Specify the location of your Zend Framework installation.

## Manual installation of Zend Framework

You can choose to manually install the Zend Framework.

1.  Download the
    [latest release of the Zend Framework](https://web.archive.org/web/20160902051506mp_/http://framework.zend.com/).

    You can install either the minimal or full package. Flash Builder installs
    the minimal package.

2.  Extract the downloaded version to a location on your system.

3.  In the Flex project folder for accessing PHP services, update the
    `zend_path` variable defined in `amf_config.ini`.

    If the `zend_path` variable is commented out, uncomment the `zend_path`
    variable. Specify the absolute path to the location of your Zend Framework
    installation.

## Troubleshooting a Zend Framework installation

If you get an error connecting to the Zend Framework, here are some tips in
resolving the error.

### Manual installation of the Zend Framework

If you manually installed the Zend Framework, examine the zend_path variable in
`amf_config.ini`.

`amf_config.ini` is in the project output folder.

Check the following:

- Make sure `zend_path`is uncommented.

- The specified path to your Zend Framework installation is correct:

  - The path is an absolute path to a destination on the local files system. You
    cannot specify a path to a mapped network resource.

  - The path is to the library folder of your Zend Framework installation.
    Typically, the library folder is in the following locations:

    (Windows) `C:\apache\PHPFrameworks/ZendFramework/library`

    (Mac OS) `/user/apache/PHP/frameworks/ZendFramework/library`

### Flash Builder installation of the Zend Framework

If Flash Builder installed the Zend Framework, check the following:

- The location of the web root folder

  Flash Builder installs the Zend Framework in the project's web root folder.
  Check the location of the web root folder. Select Project \> Properties\> Flex
  Server.

- Ensure that the web server is configured to use PHP.

- Examine the zend_path variable in `amf_config.ini`.

  `amf_config.ini` is in the project output folder.

  Check the following:

  - `zend_path` is uncommented.

  - The specified path points to the Zend Framework installation at the
    project's web root

  - The path is an absolute path to a destination on the local files system. You
    cannot specify a path to a mapped network resource.
