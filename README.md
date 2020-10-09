Implementing PHP CRUD using OOPS is the easiest implementation part in any PHP web application. Previously, we have seen Object-oriented CRUD system with PDO.

Also, we have seen many PHP CRUD examples in previous articles. If you want to create PHP CRUD using ajax the AJAX inline CRUD example will be helpful.
PHP CRUD using OOPS – Overview

In this article, we are going to create a PHP CRUD using OOPS to create and manage students attendance. In this example, I have used MySQLi prepared statement for executing database queries. 

In the student CRUD, the student records are added to the database using MySQLi by collecting data via an HTML form. The form data will be sent to the PHP side with the CRUD action parameter.

The action parameter signifies the action performed by the user so that the operation can be identified at the controller level and dispatch to the respective business logic. This is the control flow followed in the MVC design pattern.

For each action, the model class will contain functions to perform PHP CRUD using OOPS. The student attendance statistics are created and managed in the database with the reference of the student id.

Then, the student attendance statistics will be read and rendered in a grid view. In a previous tutorial, we have seen how to render an attendance graph with dynamic data using Google Charts.
Example Project File Structure

This example code is dealt with the Student and Attendance functionalities with separate CRUD. The file structure of this example is shown below.

File Structure to Create PHP CRUD Example 
PHP CRUD Engine using Model View Controller (MVC)

In this example, the landing page index.php will be the CRUD action controller. In this controller, the student and the attendance CRUD action cases are stacked by using PHP switch control structure.

The actions parameter is passed with the URL based on which the controller case will be executed. The model classes Student.php and Attendance.php are consisting of CRUD functions which will be called from the appropriate CRUD cases.

The model class functions are preparing the query and query parameters array. The query and its parameters are sent on invoking the appropriate database functions.

In the DBController class, the query statements are executed after binding the query parameters using MySQLi.

The control flow through the landing controller -> model -> DBController will be used to get the dynamic results. The dynamic results are rendered on the presentation layer which is created in the template files.

This essentially summarizes the MVC architecture and the flow of control between the different layers involved in the implementation.
Create PHP CRUD for Student Functionality

In this section, we will see the code to create PHP CRUD using OOPS features for managing student data using MySQLi. Also, we will see how to create the HTML view for providing a web interface for the users to perform CRUD actions.

The student add/edit forms are created to the capture student details. The student list contains the edit/delete action controls to trigger the PHP CRUD action.

Student Add Edit Form UI
Create New Student

Above student list view the Add Student button is shown to the user. By clicking this button the add form will be displayed with the name, roll number, class and the date of birth fields.

On submitting this form, basic client-side form validation is handled before submitting the data to PHP.

This is the HTML code with the student add-form and the validation script.

<?php require_once "web/header.php"; ?>

<form name="frmAdd" method="post" action="" id="frmAdd"
    onSubmit="return validate();">
    <div id="mail-status"></div>
    <div>
        <label style="padding-top: 20px;">Name</label> <span
            id="name-info" class="info"></span><br /> <input type="text"
            name="name" id="name" class="demoInputBox">
    </div>
    <div>
        <label>Roll Number</label> <span id="roll-number-info"
            class="info"></span><br /> <input type="text"
            name="roll_number" id="roll_number" class="demoInputBox">
    </div>
    <div>
        <label>Date of Birth</label> <span id="dob-info" class="info"></span><br />
        <input type="date" name="dob" id="dob" class="demoInputBox">
    </div>
    <div>
        <label>Class</label> <span id="class-info" class="info"></span><br />
        <input type="text" name="class" id="class" class="demoInputBox">
    </div>
    <div>
        <input type="submit" name="add" id="btnSubmit" value="Add" />
    </div>
    </div>
</form>
<script src="https://code.jquery.com/jquery-2.1.1.min.js"
    type="text/javascript"></script>
<script>
function validate() {
    var valid = true;   
    $(".demoInputBox").css('background-color','');
    $(".info").html('');
    
    if(!$("#name").val()) {
        $("#name-info").html("(required)");
        $("#name").css('background-color','#FFFFDF');
        valid = false;
    }
    if(!$("#roll_number").val()) {
        $("#roll-number-info").html("(required)");
        $("#roll_number").css('background-color','#FFFFDF');
        valid = false;
    }
    if(!$("#dob").val()) {
        $("#dob-info").html("(required)");
        $("#dob").css('background-color','#FFFFDF');
        valid = false;
    }
    if(!$("#class").val()) {
        $("#class-info").html("(required)");
        $("#class").css('background-color','#FFFFDF');
        valid = false;
    }   
    return valid;
}
</script>
</body>
</html>

The form data will be received in the server-side script created for the PHP CRUD using OOPS. From the student-add case, the form data are passed as a parameter to the addStudent function.

In the addStudent() function the INSERT query and the query parameters are generated. These query details will be sent to the MVC controller layer to perform the student insert action using MySQLi.

This is the code from the index.php which shows the student CRUD action cases.

<?php
require_once ("class/Student.php");

...

// $action = "";
if (! empty($_GET["action"])) {
    $action = $_GET["action"];
}
switch ($action) {
    
    ...
    
    case "student-add":
        if (isset($_POST['add'])) {
            $name = $_POST['name'];
            $roll_number = $_POST['roll_number'];
            $dob = "";
            if ($_POST["dob"]) {
                $dob_timestamp = strtotime($_POST["dob"]);
                $dob = date("Y-m-d", $dob_timestamp);
            }
            $class = $_POST['class'];
            
            $student = new Student();
            $insertId = $student->addStudent($name, $roll_number, $dob, $class);
            if (empty($insertId)) {
                $response = array(
                    "message" => "Problem in Adding New Record",
                    "type" => "error"
                );
            } else {
                header("Location: index.php");
            }
        }
        require_once "web/student-add.php";
        break;
    
    case "student-edit":
        $student_id = $_GET["id"];
        $student = new Student();
        
        if (isset($_POST['add'])) {
            $name = $_POST['name'];
            $roll_number = $_POST['roll_number'];
            $dob = "";
            if ($_POST["dob"]) {
                $dob_timestamp = strtotime($_POST["dob"]);
                $dob = date("Y-m-d", $dob_timestamp);
            }
            $class = $_POST['class'];
            
            $student->editStudent($name, $roll_number, $dob, $class, $student_id);
            
            header("Location: index.php");
        }
        
        $result = $student->getStudentById($student_id);
        require_once "web/student-edit.php";
        break;
    
    case "student-delete":
        $student_id = $_GET["id"];
        $student = new Student();
        
        $student->deleteStudent($student_id);
        
        $result = $student->getAllStudent();
        require_once "web/student.php";
        break;
    
    default:
        $student = new Student();
        $result = $student->getAllStudent();
        require_once "web/student.php";
        break;
}
?>

Read Student Data to List with Update Delete Action Controls

In the above code, the student data from the database can be read in two ways. For the list view, it reads all student record regardless of any condition.

But, in the edit case, the student result is fetched based on the id to populate the existing data on the edit form. The create and update flow will mostly be similar to each other. 

Create PHP Crud for Student

This is the Student.php code containing the functions to execute PHP CRUD using OOPS and reflect the changes in the student database using MySQLi.

<?php 
require_once ("class/DBController.php");
class Student
{
    private $db_handle;
    
    function __construct() {
        $this->db_handle = new DBController();
    }
    
    function addStudent($name, $roll_number, $dob, $class) {
        $query = "INSERT INTO tbl_student (name,roll_number,dob,class) VALUES (?, ?, ?, ?)";
        $paramType = "siss";
        $paramValue = array(
            $name,
            $roll_number,
            $dob,
            $class
        );
        
        $insertId = $this->db_handle->insert($query, $paramType, $paramValue);
        return $insertId;
    }
    
    function editStudent($name, $roll_number, $dob, $class, $student_id) {
        $query = "UPDATE tbl_student SET name = ?,roll_number = ?,dob = ?,class = ? WHERE id = ?";
        $paramType = "sissi";
        $paramValue = array(
            $name,
            $roll_number,
            $dob,
            $class,
            $student_id
        );
        
        $this->db_handle->update($query, $paramType, $paramValue);
    }
    
    function deleteStudent($student_id) {
        $query = "DELETE FROM tbl_student WHERE id = ?";
        $paramType = "i";
        $paramValue = array(
            $student_id
        );
        $this->db_handle->update($query, $paramType, $paramValue);
    }
    
    function getStudentById($student_id) {
        $query = "SELECT * FROM tbl_student WHERE id = ?";
        $paramType = "i";
        $paramValue = array(
            $student_id
        );
        
        $result = $this->db_handle->runQuery($query, $paramType, $paramValue);
        return $result;
    }
    
    function getAllStudent() {
        $sql = "SELECT * FROM tbl_student";
        $result = $this->db_handle->runBaseQuery($sql);
        return $result;
    }
}
?>

Student Attendance CRUD in PHP

To create PHP CRUD using OOPS for the student attendance functionality, we need to consider both the student and attendance database tables.

For example, when we want to read the attendance data for the edit page, the existing data has to be populated in the form fields. So the student and attendance tables are joined based on the student id.

In this example, we are using left join in the SELECT query to retrieve the data from the database using MySQLi.

Previously, we have seen several types of MySQL join Left Join, Right Join and more. If you are a beginner and want to refer how to use MySQL Joins then the linked article will be helpful.
Attendance Functionality Controller and Class Functions

In the attendance functionality, the way the CRUD actions performed is varied compared to the student CRUD. In this CRUD, the bulk add, modify, delete is performed based on the attendance date.

To create PHP CRUD for student attendance with bulk action feature, the list of student attendance data will be listed in a tabular format. The table is enclosed with an HTML form to post the attendance data to the PHP multi-record grid layout.

The following code shows the Attendance.php class which contains the PHP functions to execute the bulk CRUD action. Before add or edit student attendance to the database, there will be a condition checked for the data duplication on the database.

If duplicate data exists for a particular date, then it will be cleared before adding new attendance records to the database.

<?php
require_once ("class/DBController.php");
class Attendance {
    private $db_handle;
    
    function __construct() {
        $this->db_handle = new DBController();
    }
    
    function addAttendance($attendance_date, $student_id, $present, $absent) {
        $query = "INSERT INTO tbl_attendance (attendance_date,student_id,present,absent) VALUES (?, ?, ?, ?)";
        $paramType = "siii";
        $paramValue = array(
            $attendance_date,
            $student_id,
            $present,
            $absent
        );
        
        $insertId = $this->db_handle->insert($query, $paramType, $paramValue);
        return $insertId;
    }
    
    function deleteAttendanceByDate($attendance_date) {
        $query = "DELETE FROM tbl_attendance WHERE attendance_date = ?";
        $paramType = "s";
        $paramValue = array(
            $attendance_date
        );
        $this->db_handle->update($query, $paramType, $paramValue);
    }
    
    function getAttendanceByDate($attendance_date) {
        $query = "SELECT * FROM tbl_attendance LEFT JOIN tbl_student ON tbl_attendance.student_id = tbl_student.id WHERE attendance_date = ? ORDER By student_id";
        $paramType = "s";
        $paramValue = array(
            $attendance_date
        );
        
        $result = $this->db_handle->runQuery($query, $paramType, $paramValue);
        return $result;
    }
    
    function getAttendance() {
        $sql = "SELECT id, attendance_date, sum(present) as present, sum(absent) as absent FROM tbl_attendance GROUP By attendance_date";
        $result = $this->db_handle->runBaseQuery($sql);
        return $result;
    }
}
?>

Add Student Attendance (Create)

For taking the student attendance data from the user, the list of all students will be displayed with radio options. These buttons will be used to select present/absent accordingly.

The following code is used to show the attendance add-form to the user. In this form, the attendance date is a required field.

By default, the radio options are selected as “present”. We can change it accordingly based on the student attendance status.

<?php require_once "web/header.php"; ?>

<form name="frmAdd" method="post" action="" id="frmAdd"
    onSubmit="return validate();">
    <div>
        <input type="date" name="attendance_date" id="attendance_date" class="demoInputBox"> <span id="attendance_date-info" class="info"></span>
    </div>
    <div id="toys-grid">
        <table cellpadding="10" cellspacing="1">
            <thead>
                <tr>
                    <th><strong>Student</strong></th>
                    <th><strong>Present</strong></th>
                    <th><strong>Absent</strong></th>

                </tr>
            </thead>
            <tbody>
                    <?php 
            if (! empty($studentResult)) {
                foreach ($studentResult as $k => $v) {
            ?>
          <tr>
                    <td><input type="hidden"
            name="student_id[]" id="student_id" value = "<?php echo $studentResult[$k]["id"]; ?>">
            <?php echo $studentResult[$k]["name"]; ?></td>
                    <td><input type="radio" name="attendance-<?php echo $studentResult[$k]["id"]; ?>" value="present" checked /></td>
                    <td><input type="radio" name="attendance-<?php echo $studentResult[$k]["id"]; ?>" value="absent" /></td>
                </tr>
                    <?php
                        }
                    }
                    ?>
            <tbody>
        
        </table>
        
    </div>
   <div>
        <input type="submit" name="add" id="btnSubmit" value="Add" />
    </div> 
</form>
<script src="https://code.jquery.com/jquery-2.1.1.min.js"
    type="text/javascript"></script>
<script>
function validate() {
    var valid = true;   
    $(".demoInputBox").css('background-color','');
    $(".info").html('');
    
    if(!$("#attendance_date").val()) {
        $("#attendance_date-info").html("(required)");
        $("#attendance_date").css('background-color','#FFFFDF');
        valid = false;
    } 
    return valid;
}
</script>
</body>
</html>

The following screenshot shows the student attendance add form with a selectable date and attendance radio options.

Create PHP Crud for Student Attendance
Read, Update and Delete Options for Student Attendance

In the student attendance list page, the date-wise attendance statistics will be displayed in a tabular format. The update and delete action controls are added to perform actions based on the attendance date.

When the user clicks on the delete icon from the attendance list page, the corresponding attendance date will be passed via URL. With the reference of the action send via the URL, the corresponding controller case will be executed.

case "attendance-add":
    if (isset($_POST['add'])) {
        $attendance = new Attendance();
        
        $attendance_timestamp = strtotime($_POST["attendance_date"]);
        $attendance_date = date("Y-m-d", $attendance_timestamp);
        
        if(!empty($_POST["student_id"])) {
            $attendance->deleteAttendanceByDate($attendance_date);
            foreach($_POST["student_id"] as $k=> $student_id) {
                $present = 0;
                $absent = 0;
                
                if($_POST["attendance-$student_id"] == "present") {
                    $present = 1;
                }
                else if($_POST["attendance-$student_id"] == "absent") {
                    $absent = 1;
                }
                
                $attendance->addAttendance($attendance_date, $student_id, $present, $absent);
            }
        }
        header("Location: index.php?action=attendance");
    }
    $student = new Student();
    $studentResult = $student->getAllStudent();
    require_once "web/attendance-add.php";
    break;

case "attendance-edit":
    $attendance_date = $_GET["date"];
    $attendance = new Attendance();
    if (isset($_POST['add'])) {
        $attendance->deleteAttendanceByDate($attendance_date);
        if(!empty($_POST["student_id"])) {
            foreach($_POST["student_id"] as $k=> $student_id) {
                $present = 0;
                $absent = 0;
                
                if($_POST["attendance-$student_id"] == "present") {
                    $present = 1;
                }
                else if($_POST["attendance-$student_id"] == "absent") {
                    $absent = 1;
                }
                
                $attendance->addAttendance($attendance_date, $student_id, $present, $absent);
            }
        }
        header("Location: index.php?action=attendance");
    }
    
    $result = $attendance->getAttendanceByDate($attendance_date);
    
    $student = new Student();
    $studentResult = $student->getAllStudent();
    require_once "web/attendance-edit.php";
    break;

case "attendance-delete":
    $attendance_date = $_GET["date"];
    $attendance = new Attendance();
    $attendance->deleteAttendanceByDate($attendance_date);
    
    $result = $attendance->getAttendance();
    require_once "web/attendance.php";
    break;

case "attendance":
    $attendance = new Attendance();
    $result = $attendance->getAttendance();
    require_once "web/attendance.php";
    break;

The complete source code download is available at the end of the article to set up this example in your machine.
Database Script

This SQL script should be imported to run this example in your machine.

CREATE TABLE `tbl_attendance` (
  `id` int(11) NOT NULL,
  `student_id` int(11) NOT NULL,
  `present` tinyint(4) NOT NULL,
  `absent` tinyint(4) NOT NULL,
  `attendance_date` date NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=latin1;

CREATE TABLE `tbl_student` (
  `id` int(11) NOT NULL,
  `name` varchar(55) NOT NULL,
  `roll_number` int(11) NOT NULL,
  `dob` date NOT NULL,
  `class` varchar(55) NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=latin1;
