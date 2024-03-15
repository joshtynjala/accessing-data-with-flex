# Implementing PHP services

When implementing services in PHP, you typically implement the services as PHP
classes. The classes in PHP do not necessarily have to be object-oriented
classes. Rather, each class can be a library of functions that provide the
service operations.

You can create PHP services in any editing environment, such as Dreamweaver or
Zend Studio. Flash Builder does not provide an editor optimized for editing PHP
files. However, if you open a PHP file in Flash Builder, Flash Builder launches
the application on your system that is associated with PHP files. For
convenience, Flash Builder also provides a plain text editor that you can use to
edit the PHP files.

## Using AMF to access services implemented in PHP

PHP data services are available using Action Message Format (AMF). AMF provides
messaging between a Flash client and a web server. Flash Builder uses the Zend
AMF framework to implement AMF messaging for PHP data services.

For information on Zend AMF, see
[Zend Framework Programmer's Reference](https://web.archive.org/web/20160904195453mp_/http://framework.zend.com/manual/en/zend.amf.html).

For information on installing Zend Framework, see
[Installing Zend Framework](../building-data-centric-applications-with-flash-builder/installing-zend-framework.md).

For information on using Zend with Flash Builder for PHP, see the
[Zend website](https://web.archive.org/web/20160904195453mp_/http://www.zend.com/en/products/studio/flash-builder-for-php/).

Note: Although Flash Builder uses the Zend AMF framework, you are not required
to use Zend components when creating PHP services. Although Zend components work
well with Flash Builder, you can also use any PHP development environment for
creating services.

## Example PHP services

You can implement a basic PHP service by creating a PHP class file that contains
functions for the service operations. The following example illustrates an
EmployeeService that implements two functions:

- `getAllIEmployees()`

  Retrieves all employee records in the database.

- `getEmployeeByID($itemID)`

  Returns a single employee record.

This example illustrates client-side typing. The service returns untyped data.
Flash Builder uses client-side typing to introspect the returned data and define
a data type.

Subsequent examples illustrate how to implement services for paging and data
management.

You can also use Flash Builder to access services that implement server-side
typing. See
[Client-side and server-side typing](./client-side-and-server-side-typing.md).

Examples illustrating server-side typing were not available at the time this
document was completed. For server-side typing examples, see
[Flash Builder server-side type examples](https://web.archive.org/web/20160904195453mp_/http://www.adobe.com/go/learn_flex4_serversidetype_en).

### PHP basic service example

This example shows how to implement a basic service in PHP. The example is based
on code that Flash Builder generates when accessing a database table. See
[Generating a sample PHP service from a database table](../building-data-centric-applications-with-flash-builder/connecting-to-data-services.md#generating-a-sample-php-service-from-a-database-table).

This example illustrates client-side typing. See
[Client-side and server-side typing](./client-side-and-server-side-typing.md).

Important: Example services are for prototyping only. Use the example service
only in a trusted development environment. Before deploying this service, be
sure to increase security and restrict access appropriately. For information on
writing secure services, see
[Deploying applications that access data services](../building-data-centric-applications-with-flash-builder/deploying-applications-that-access-data-services.md).

    <?php
    /**
     * This sample service contains functions that illustrate typical service operations.
     * This code is for prototyping only.
     *
     *  Authenticate users before allowing them to call these methods.
     */
    class EmployeeService {

        var $username = "root";
        var $password = "root";
        var $server = "localhost";
        var $port = "3306";
        var $databasename = "employees";
        var $tablename = "employees";

        var $connection;

        /**
         * The constructor initializes the connection to database. Everytime a request is
         * received by Zend AMF, an instance of the service class is created and then the
         * requested method is called.
         */
        public function __construct() {
              $this->connection = mysqli_connect(
                                      $this->server,
                                      $this->username,
                                      $this->password,
                                      $this->databasename,
                                      $this->port
                                  );

            $this->throwExceptionOnError($this->connection);
        }

        /**
         * Returns all the rows from the table.
         *
         * Add authroization or any logical checks for secure access to your data
         *
         * @return array
         */
        public function getAllEmployees() {

            $stmt = mysqli_prepare($this->connection, "SELECT * FROM $this->tablename");
            $this->throwExceptionOnError();

            mysqli_stmt_execute($stmt);
            $this->throwExceptionOnError();

            $rows = array();

            mysqli_stmt_bind_result($stmt, $row->emp_no, $row->birth_date,
                $row->first_name, $row->last_name, $row->gender, $row->hire_date);

            while (mysqli_stmt_fetch($stmt)) {
              $rows[] = $row;
              $row = new stdClass();
              mysqli_stmt_bind_result($stmt, $row->emp_no, $row->birth_date,
                    $row->first_name, $row->last_name, $row->gender, $row->hire_date);
            }

            mysqli_stmt_free_result($stmt);
            mysqli_close($this->connection);

            return $rows;
        }

        /**
         * Returns the item corresponding to the value specified for the primary key.
         *
         * Add authroization or any logical checks for secure access to your data
         *
         *
         * @return stdClass
         */
        public function getEmployeesByID($itemID) {

            $stmt = mysqli_prepare($this->connection, "SELECT * FROM $this->tablename where emp_no=?");
            $this->throwExceptionOnError();

            mysqli_bind_param($stmt, 'i', $itemID);
            $this->throwExceptionOnError();

            mysqli_stmt_execute($stmt);
            $this->throwExceptionOnError();

            mysqli_stmt_bind_result($stmt, $row->emp_no, $row->birth_date,
                    $row->first_name, $row->last_name, $row->gender, $row->hire_date);

            if(mysqli_stmt_fetch($stmt)) {
                return $row;
            } else {
                return null;
            }
        }

        /**
         * Utitity function to throw an exception if an error occurs
         * while running a mysql command.
         */
        private function throwExceptionOnError($link = null) {
            if($link == null) {
                $link = $this->connection;
            }
            if(mysqli_error($link)) {
                $msg = mysqli_errno($link) . ": " . mysqli_error($link);
                throw new Exception('MySQL Error - '. $msg);
            }
        }
    }


    ?>

Highlights of EmployeeService:

- Connects to the employees database, which it accesses on port 3306 of
  localhost. Accesses the employees table in the database.

- Provides class variables for connecting to the service and accessing the
  tables in the database.

  These variables can be used in functions in the class.

  Replace the values for these variables with values for your system.

- Returns the array of objects to the client application.

  When programming using the Flex framework, services return data only. The
  client application handles the formatting and presentation of the data.

  This model differs from traditional PHP services, that return data formatted
  in an HTML template.

- `getEmployeesByID($itemID)` function binds the input parameter to data types.

  The number of variables and length of string types must match the parameters
  in the statement. The '`?`' in the prepare statement is a placeholder for the
  parameter.

  mysqli recognizes the following types:

  - integer (`i`)

  - double (`d`)

  - string (`s`)

  - blob (`b`)

- Binds the results, creating an array of objects (`$row[]`).

  Flex handles recordsets as an array of objects. Each object represents a
  record retrieved from the database. Each column of the database record becomes
  a property of the returned object. The client application can now access the
  returned data as objects with a set of properties.

  Because the server does not define a data type for the returned data, you
  configure the data type for the returned object. See
  [Client-side and server-side typing](./client-side-and-server-side-typing.md).

- Provides a constructor function for initializing the connection to the
  database.

- Uses mysqli prepare statements for constructing database queries.

  Using prepare statements is a defense against SQL injection statements in
  calls to the server. Only after preparing the statement is the statement
  executed on the server.

- Authenticate users before providing access to the functions in this service.

  The sample code does not illustrate how to authenticate users. See the
  ColdFusion documentation,
  [About User Security](https://web.archive.org/web/20160904195453mp_/http://www.adobe.com/go/cf9_usersecurity).
  The security principles on user authentication and authorization in this
  ColdFusion documentation apply to PHP services.

- Throws an exception on error.

  Information that you provide in exceptions is useful when debugging the
  service implementation. The Flash Builder Test Operation interface displays
  information returned by exceptions.

  See [Debugging remote services](./debugging-remote-services.md) for more
  information on testing services.

- The filename, `EmployeeService.php`, matches the PHP class name for the
  service.

  If the filename and the class name do not match, you encounter errors when
  accessing the service.

### PHP example implementing paging

Flash Builder tools allow you to implement paging of data retrieved from a
remote service. Paging is the incremental retrieval of large data sets.

Flash Builder requires specific function signatures to implement paging. The
following code example provides an example of one way to implement a PHP service
for paged data.

This example is based on the code generated by Flash Builder when accessing a
database table. See
[Generating a sample PHP service from a database table](../building-data-centric-applications-with-flash-builder/connecting-to-data-services.md#generating-a-sample-php-service-from-a-database-table).

Important: Example services are for prototyping only. Use the example service
only in a trusted development environment. Before deploying this service, be
sure to increase security and restrict access appropriately. For information on
writing secure services, see
[Deploying applications that access data services](../building-data-centric-applications-with-flash-builder/deploying-applications-that-access-data-services.md).

    <?php

    /**
     *  This sample service contains functions that illustrate typical service operations.
     *  This code is for prototyping only.
     *
     *  Authenticate the user prior to allowing them to call these methods.
     *
     */
    class EmployeeServicePaged {

        var $username = "root";
        var $password = "root";
        var $server = "localhost";
        var $port = "3306";
        var $databasename = "employees";
        var $tablename = "employees";

        var $connection;

        /**
         * The constructor initializes the connection to database. Everytime a request is
         * received by Zend AMF, an instance of the service class is created and then the
         * requested method is invoked.
         */
        public function __construct() {
              $this->connection = mysqli_connect(
                                      $this->server,
                                      $this->username,
                                      $this->password,
                                      $this->databasename,
                                      $this->port
                                  );

            $this->throwExceptionOnError($this->connection);
        }


        /**
         * Returns the number of rows in the table.
         *
         * Add authroization or any logical checks for secure access to your data
         *
         *
         */
        public function count() {
            $stmt = mysqli_prepare($this->connection, "SELECT COUNT(*) AS COUNT
                                                        FROM $this->tablename");
            $this->throwExceptionOnError();

            mysqli_stmt_execute($stmt);
            $this->throwExceptionOnError();

            mysqli_stmt_bind_result($stmt, $rec_count);
            $this->throwExceptionOnError();

            mysqli_stmt_fetch($stmt);
            $this->throwExceptionOnError();

            mysqli_stmt_free_result($stmt);
            mysqli_close($this->connection);

            return $rec_count;
        }


        /**
         * Returns $numItems rows starting from the $startIndex row from the
         * table.
         *
         * Add authroization or any logical checks for secure access to your data
         *
         * @return array
         */
        public function getEmployees_paged($startIndex, $numItems) {

            $stmt = mysqli_prepare($this->connection, "SELECT * FROM
                                        $this->tablename LIMIT ?, ?");
            $this->throwExceptionOnError();

            mysqli_bind_param($stmt, 'ii', $startIndex, $numItems);
            mysqli_stmt_execute($stmt);
            $this->throwExceptionOnError();

            $rows = array();

            mysqli_stmt_bind_result($stmt, $row->emp_no, $row->birth_date,
                                    $row->first_name, $row->last_name,
                                    $row->gender, $row->hire_date);

            while (mysqli_stmt_fetch($stmt)) {
              $rows[] = $row;
              $row = new stdClass();
              mysqli_stmt_bind_result($stmt, $row->emp_no, $row->birth_date,
                                        $row->first_name, $row->last_name,
                                        $row->gender, $row->hire_date);
            }

            mysqli_stmt_free_result($stmt);
            mysqli_close($this->connection);

            return $rows;
        }


        /**
         * Utitity function to throw an exception if an error occurs
         * while running a mysql command.
         */
        private function throwExceptionOnError($link = null) {
            if($link == null) {
                $link = $this->connection;
            }
            if(mysqli_error($link)) {
                $msg = mysqli_errno($link) . ": " . mysqli_error($link);
                throw new Exception('MySQL Error - '. $msg);
            }
        }
    }
    ?>

The EmployeeServicePaged service returns untyped data. Use the Flash Builder
tools to configure the return type for `getEmployees_Paged()`. After configuring
the return type, enable paging on the `getEmployees_Paged()` operation.

### PHP example implementing data management

Flash Builder tools allow you to implement data management functionality for
remote services. Data management is the synchronization of updates to data on a
server from the client application.

Flash Builder requires a combination of specific function signatures to
implement data management. The following code example provides an example of one
way to implement a PHP service for data management.

This example is based on the code generated by Flash Builder when accessing a
database table. See
[Generating a sample PHP service from a database table](../building-data-centric-applications-with-flash-builder/connecting-to-data-services.md#generating-a-sample-php-service-from-a-database-table).

Important: Example services are for prototyping only. Use the example service
only in a trusted development environment. Before deploying this service, be
sure to increase security and restrict access appropriately. For information on
writing secure services, see
[Deploying applications that access data services](../building-data-centric-applications-with-flash-builder/deploying-applications-that-access-data-services.md).

    <?php

    /**
     *  This sample service contains functions that illustrate typical service operations.
     *  This code is for prototyping only.
     *
     *  Authenticate the user prior to allowing them to call these methods.
     */
    class EmployeeServiceDM {

        var $username = "root";
        var $password = "root";
        var $server = "localhost";
        var $port = "3306";
        var $databasename = "employees";
        var $tablename = "employees";

        var $connection;

        /**
         * The constructor initializes the connection to database. Everytime a request is
         * received by Zend AMF, an instance of the service class is created and then the
         * requested method is invoked.
         */
        public function __construct() {
              $this->connection = mysqli_connect(
                                      $this->server,
                                      $this->username,
                                      $this->password,
                                      $this->databasename,
                                      $this->port
                                  );

            $this->throwExceptionOnError($this->connection);
        }

        /**
         * Returns all the rows from the table.
         *
         * Add authroization or any logical checks for secure access to your data
         *
         * @return array
         */
        public function getAllEmployees() {

            $stmt = mysqli_prepare($this->connection, "SELECT * FROM $this->tablename");
            $this->throwExceptionOnError();

            mysqli_stmt_execute($stmt);
            $this->throwExceptionOnError();

            $rows = array();

            mysqli_stmt_bind_result($stmt, $row->emp_no, $row->birth_date,
                                    $row->first_name, $row->last_name,
                                    $row->gender, $row->hire_date);

            while (mysqli_stmt_fetch($stmt)) {
              $rows[] = $row;
              $row = new stdClass();
              mysqli_stmt_bind_result($stmt, $row->emp_no, $row->birth_date,
                                        $row->first_name, $row->last_name,
                                        $row->gender, $row->hire_date);
            }

            mysqli_stmt_free_result($stmt);
            mysqli_close($this->connection);

            return $rows;
        }

        /**
         * Returns the item corresponding to the value specified for the primary key.
         *
         * Add authroization or any logical checks for secure access to your data
         *
         *
         * @return stdClass
         */
        public function getEmployeesByID($itemID) {

            $stmt = mysqli_prepare($this->connection, "SELECT * FROM
                                            $this->tablename where emp_no=?");
            $this->throwExceptionOnError();

            mysqli_bind_param($stmt, 'i', $itemID);
            $this->throwExceptionOnError();

            mysqli_stmt_execute($stmt);
            $this->throwExceptionOnError();

            mysqli_stmt_bind_result($stmt, $row->emp_no, $row->birth_date,
                                    $row->first_name, $row->last_name,
                                    $row->gender, $row->hire_date);

            if(mysqli_stmt_fetch($stmt)) {
                return $row;
            } else {
                return null;
            }
        }

        /**
         * Returns the item corresponding to the value specified for the primary key.
         *
         * Add authroization or any logical checks for secure access to your data
         *
         *
         * @return stdClass
         */
        public function createEmployees($item) {

            $stmt = mysqli_prepare($this->connection, "INSERT INTO $this->tablename
                                    (emp_no, birth_date, first_name, last_name,
                                    gender, hire_date) VALUES (?, ?, ?, ?, ?, ?)");
            $this->throwExceptionOnError();

            mysqli_bind_param($stmt, 'isssss', $item->emp_no, $item->birth_date
                                        $item->first_name, $item->last_name,
                                        $item->gender, $item->hire_date);
            $this->throwExceptionOnError();

            mysqli_stmt_execute($stmt);
            $this->throwExceptionOnError();

            $autoid = mysqli_stmt_insert_id($stmt);

            mysqli_stmt_free_result($stmt);
            mysqli_close($this->connection);

            return $autoid;
        }

        /**
         * Updates the passed item in the table.
         *
         * Add authroization or any logical checks for secure access to your data
         *
         * @param stdClass $item
         * @return void
         */
        public function updateEmployees($item) {

            $stmt = mysqli_prepare($this->connection, "UPDATE $this->tablename
                                    SET emp_no=?, birth_date=?, first_name=?,
                                    last_name=?, gender=?, hire_date=?
                                    WHERE emp_no=?");
            $this->throwExceptionOnError();

            mysqli_bind_param($stmt, 'isssssi', $item->emp_no, $item->birth_date,
                                $item->first_name, $item->last_name, $item->gender,
                                $item->hire_date, $item->emp_no);
            $this->throwExceptionOnError();

            mysqli_stmt_execute($stmt);
            $this->throwExceptionOnError();

            mysqli_stmt_free_result($stmt);
            mysqli_close($this->connection);
        }

        /**
         * Deletes the item corresponding to the passed primary key value from
         * the table.
         *
         * Add authroization or any logical checks for secure access to your data
         *
         *
         * @return void
         */
        public function deleteEmployees($itemID) {

            $stmt = mysqli_prepare($this->connection, "DELETE FROM $this->tablename
                                                                WHERE emp_no = ?");
            $this->throwExceptionOnError();

            mysqli_bind_param($stmt, 'i', $itemID);
            mysqli_stmt_execute($stmt);
            $this->throwExceptionOnError();

            mysqli_stmt_free_result($stmt);
            mysqli_close($this->connection);
        }

        /**
         * Utitity function to throw an exception if an error occurs
         * while running a mysql command.
         */
        private function throwExceptionOnError($link = null) {
            if($link == null) {
                $link = $this->connection;
            }
            if(mysqli_error($link)) {
                $msg = mysqli_errno($link) . ": " . mysqli_error($link);
                throw new Exception('MySQL Error - '. $msg);
            }
        }
    }
    ?>

The EmployeeServiceDM service returns untyped data. Use the Flash Builder tools
to configure the return type for `getAllEmployeess()` and `getEmployeesByID()`.
Use Employee for the custom data type returned by these operations.

After configuring the return type, enable data management on the Employee data
type.

More Help topics

[Configuring data types for data service operations](../building-data-centric-applications-with-flash-builder/configuring-data-types-for-data-service-operations.md)

[Accessing PHP services](../building-data-centric-applications-with-flash-builder/connecting-to-data-services.md#accessing-php-services)

[Generating a sample PHP service from a database table](../building-data-centric-applications-with-flash-builder/connecting-to-data-services.md#generating-a-sample-php-service-from-a-database-table)

[Managing the access of data from the server](../building-data-centric-applications-with-flash-builder/managing-the-access-of-data-from-the-server.md)
