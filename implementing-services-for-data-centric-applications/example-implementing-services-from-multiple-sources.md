# Example implementing services from multiple sources

Typically applications access data from different sources, displaying the
results of the data association in an application. This example shows how to
associate data from the following three tables in an employees database:

- Departments

  Each record contains the following fields: department number and department
  name.

- Dept_emp

  Each record contains the following fields: emp_no, dept_no, from_date, to_date

- Employees

  Each record contains the following fields: emp_no, birth_date, first_name,
  last_name, gender, hire_date

The sample application has two DataGrids, one for Departments and one for
Employees.

The Departments lists all the departments. When you select a department, the
Employees DataGrid lists all the employees in that department.

When you select an employee in the Employees DataGrid, a form is populated,
allowing you to update the employee record.

## Create the services

For this example, create a single service. The service contains the following
operations:

- getAllDepartments()

- getEmployeesByDept()

- `getEmployeeByID()`

- `updateEmployee()`

### EmployeeService (PHP)

`EmployeeService.php` implements a service that contains a single function.
`GetEmployeesByID()` accepts the department ID as an argument, returning all the
employees in the given department. The function also returns the dates an
employee started and left the department. `GetEmployeesByDept()` executes the
following SQL query:

    SELECT
        employees.emp_no,
        employees.birth_date,
        employees.first_name,
        employees.last_name,
        employees.gender,
        employees.hire_date,
        dept_emp.from_date,
        dept_emp.to_date
    FROM employees, dept_emp
    WHERE dept_emp.emp_no = employees.emp_no and
        dept_emp.dept_no = departments.dept_no

Important: Example services are for prototyping only. Use the example service
only in a trusted development environment. Before deploying this service, be
sure to increase security and restrict access appropriately. For information on
writing secure services, see
[Deploying applications that access data services](../building-data-centric-applications-with-flash-builder/deploying-applications-that-access-data-services.md).

    <?php

    /**
     * EmployeeService.php
     *
     * This sample service contains functions that illustrate typical service operations.
     * Use these functions as a starting point for creating your own service implementation.
     *
     *  This code is for prototyping only.
     *
     *  Authenticate the user before allowing them to call these methods.
     */
    class EmployeeService {

        var $username = "admin2";
        var $password = "Cosmo49";
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
        public function getAllDepartments() {

            $stmt = mysqli_prepare($this->connection, "SELECT * FROM departments");
            $this->throwExceptionOnError();

            mysqli_stmt_execute($stmt);
            $this->throwExceptionOnError();

            $rows = array();

            mysqli_stmt_bind_result($stmt, $row->dept_no, $row->dept_name);

            while (mysqli_stmt_fetch($stmt)) {
              $rows[] = $row;
              $row = new stdClass();
              mysqli_stmt_bind_result($stmt, $row->dept_no, $row->dept_name);
            }

            mysqli_stmt_free_result($stmt);
            mysqli_close($this->connection);

            return $rows;
        }

        public function getEmployeesByDept($deptId) {
            $stmt = mysqli_prepare($this->connection, "select employees.emp_no,
                                                         employees.first_name,
                                                         employees.last_name,
                                                         employees.gender,
                                                    dept_emp.dept_no
                                             from employees, dept_emp
                                    where dept_emp.emp_no = employees.emp_no
                                        and dept_emp.dept_no = ?
                                    limit 0,30;");
            $this->throwExceptionOnError();

            mysqli_bind_param($stmt, 's', $deptId);
            $this->throwExceptionOnError();

            mysqli_stmt_execute($stmt);
            $this->throwExceptionOnError();

            $rows = array();

            mysqli_stmt_bind_result($stmt, $row->emp_no, $row->first_name,
                                $row->last_name, $row->gender, $row->dept_no);

            while (mysqli_stmt_fetch($stmt)) {
              $rows[] = $row;
              $row = new stdClass();

              mysqli_stmt_bind_result($stmt, $row->emp_no, $row->first_name,
                                $row->last_name, $row->gender, $row->dept_no);
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

            $stmt = mysqli_prepare($this->connection, "SELECT * FROM employees
                                                            where emp_no=?");
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
         * Updates the passed item in the table.
         *
         * Add authroization or any logical checks for secure access to your data
         *
         * @param stdClass $item
         * @return void
         */
        public function updateEmployees($item) {

            $stmt = mysqli_prepare($this->connection, "UPDATE employees
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
    >?>

### EmployeeService (ColdFusion)

`EmployeeService.cfc` implements a service that contains a single function.
`GetEmployeesByID()` accepts the department ID as an argument, returning all the
employees in the given department. The function also returns the dates an
employee started and left the department. `GetEmployeesByDept()` executes the
following SQL query:

    SELECT
        employees.emp_no,
            employees.birth_date,
            employees.first_name,
            employees.last_name,
            employees.gender,
            employees.hire_date,
            dept_emp.from_date,
        dept_emp.to_date
    FROM employees, dept_emp
    WHERE dept_emp.emp_no = employees.emp_no and dept_emp.dept_no = departments.dept_no

Important: Example services are for prototyping only. Use the example service
only in a trusted development environment. Before deploying this service, be
sure to increase security and restrict access appropriately. For information on
writing secure ColdFusion services, see the ColdFusion documentation
[About User Security](https://web.archive.org/web/20120101051121mp_/http://www.adobe.com/go/cf9_usersecurity).

    <cfcomponent output="false">

    <!---
        This sample service contains functions that illustrate typical service operations.
        Use these functions as a starting point for creating your own service implementation.

        This code is for prototyping only.

        Authenticate the user before allowing them to call these methods. You can find more
        information at http://www.adobe.com/go/cf9_usersecurity
    --->
        <cffunction name="getEmployeesByDept" output="false" access="remote"  returntype="any" >
            <cfargument name="dept_no" type="string" required="true" />

            <cfset var qItem="">
            <cfquery name="qItem" datasource="employees">
            SELECT     employees.emp_no,
                       employees.birth_date,
                       employees.first_name,
                       employees.last_name,
                       employees.gender,
                       employees.hire_date,
                       dept_emp.from_date,
                       dept_emp.to_date
            FROM employees, dept_emp
            WHERE     dept_emp.emp_no = employees.emp_no and
                    dept_emp.dept_no = <CFQUERYPARAM CFSQLTYPE="CF_SQL_VARCHAR" VALUE="#ARGUMENTS.dept_no#">
            </cfquery>

            <cfreturn qItem>

        </cffunction>

    </cfcomponent>?>

## Import the services into a server project.

1.  In Flash Builder, create a Flex Project that is named Associations:

    (PHP) When creating the project, specify PHP for the Application Server
    Type.

    (PHP) After creating the project, Flash Builder creates an output folder in
    the folder that is the web root of your PHP configuration. The default name
    for the PHP_Associations project is `PHP_Associations-debug`.

    (ColdFusion) When creating the project, specify ColdFusion for the
    Application Server Type. Then specify ColdFusion Flash Remoting.

2.  (PHP) Within `PHP_Associations-debug`, create a folder named `services`.
    Copy `EmployeeService.php` into the `services` folder.

3.  (ColdFusion) Create a folder named `Associations` in the web root of your
    ColdFusion configuration. Copy `EmployeeService.chc` into the `Associations`
    folder.

4.  Import the EmployeeService into the project:

    Make sure PHP_Associations is the active project in Flash Builder.

    Select Data \> Connect to PHP. To specify the PHP Class, browse to the
    `services` folder and select `EmployeeService.php`. Click Finish.

    For more information, see
    [Connecting to PHP data services](../building-data-centric-applications-with-flash-builder/connecting-to-data-services.md#connecting-to-php-data-services).

5.  Configure the return type for the operations in EmployeeService.

    - DepartmentService

      From the context menu for `getAllDepartments()`, select Configure Return
      Type.

      Click Next to auto-detect the return type.

      Specify <span class="kbd">Department</span> for the custom return type.
      Click Finish.

    - EmployeeService

      For g`etEmployeesByDept()`, select Configure Return Type.

      Click Next to auto-detect the return type.

      For the value of the parameter, specify <span class="kbd">d007</span>.
      Click Next.

      Specify <span class="kbd">Employee</span> for the custom return type.
      Click Finish.

    For more information, see
    [Configuring data types for data service operations](../building-data-centric-applications-with-flash-builder/configuring-data-types-for-data-service-operations.md).

## Build the user interface and bind the returned data to DataGrid components

1.  In Design mode of the MXML editor, add two DataGrid components to the
    editing area.

    The DataGrid component is available from the Controls folder in the
    Components view. Drag the DataGrid components to the editing area.

    Specify <span class="kbd">deptDG</span> for the Departments DataGrid.
    Specify <span class="kbd">empDeptDG</span> as the IDs for the Employees
    DataGrids.

2.  In the Data/Services view, drag the getEmployeesByDept() operation onto the
    Employees DataGrid.

    The editor switches to Source mode, with the parameter to
    getEmployeesByDept() highlighted. However, delete the generated event
    handler.

    Navigate to the Employees DataGrid. Delete the reference to the attribute
    for the creationComplete handler for the empDeptDG DataGrid.

    The first line of code for the DataGrid looks something like the following
    after you delete the reference to the event handler:

        <mx:DataGrid x="361" y="27" id="empDeptDG" dataProvider="{getEmployeesByDeptResult.lastResult}">

    > Note: The creationComplete handler is not needed for the Employees
    > DataGrid. The Employees DataGrid becomes populated when a department is
    > selected in the Departments DataGrid.

3.  Switch to Design mode of the editor. In the Data/Services view, drag the
    `getAllDepartments()` operation onto the Department DataGrid.

4.  Generate an event handler for changes to the Departments DataGrid:

    Make sure the Departments DataGrid is selected. In Properties view, select
    the On Change icon and select Generate Event Handler.

    The editor switches to Source mode with body of the event handler selected.
    Specify the following for the event handler:

        protected function deptDG_changeHandler(event:ListEvent):void {
            getEmployeesByDeptResult.token = employeeService.getEmployeesByDept(deptDG.selectedItem.dept_no);
        }

5.  Save and run the application.

    When you click a department in the Departments DataGrid, the Employees
    DataGrid displays all the employees in the department.

    Close the application.

6.  In Design mode of the MXML editor, select the Employees DataGrid. From the
    context menu, select Generate Details Form and do the following:

    1.  For Detail Call, select Call a Service...

    2.  For Service, select EmployeeService.

    3.  For Operation, select `getEmployeesByID()`.

    4.  Click Finish.

7.  In the generated event handler, specify the following for the argument to
    `getEmployeesById()`:

        protected function empDeptDG_changeHandler(event:ListEvent):void
        {
            getEmployeesByIDResult.token = employeeService.getEmployeesByID(empDeptDG.selectedItem.emp_no);
        }

8.  In Design mode, drag the form below the DataGrids.

9.  Add a Button next to the Form and make the following edits in Properties
    view:

    - For ID, specify updateButton.

    - For Label, specify <span class="kbd">Update Employee</span>.

    - Click the On click and icon and specify Generate Service Call:

      For service, select EmployeeService. For Operation, specify
      `updateEmployees()`. The editor switches to Source mode.

    - Modify the service call to updateEmployees() as shown below:

          protected function updateButton_clickHandler(event:MouseEvent):void
          {
              var e:Employee = new Employee();
              e.birth_date = birth_dateTextInput.text;
              e.first_name = first_nameTextInput.text;
              e.last_name = last_nameTextInput.text;
              e.hire_date = hire_dateTextInput.text;
              e.gender = genderTextInput.text;
              e.emp_no = employee.emp_no;

              updateEmployeesResult.token = employeeService.updateEmployees(e);
              getEmployeesByDeptResult.token =
                  employeeService.getEmployeesByDept(deptDG.selectedItem.dept_no);
          }
