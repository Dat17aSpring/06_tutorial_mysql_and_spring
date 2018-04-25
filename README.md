# 06 Tutorial: Preparedstatements, mysql and spring
> This is an alternative to using the Spring Frameworks [Default Solution which we covered in teachings](https://github.com/Dat17aSpring/06_Tutorial_MySql_JDBC).

## Connect mysql and a Spring Boot Applications using Preparedstatements



1. Create a [new application](https://github.com/Dat17i/03_hello_spring/blob/master/README.md) using the Spring Boot Initializer.

When choosing the dependencies you should now also add the **SQL -> MySql** dependency.    
So you will now have these 3: 
 
* Web -> Web
* Template Engines -> Thymeleaf
* **SQL -> MySql**
  

This will add the folowing dependency to you pom.xml file

````   
  <dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<scope>runtime</scope>
  </dependency>
````
If you have an existing application without the mysql dependency added from the beginning, you can always write this directly into the pom.xml file. 

### Connect to the database
* Create a new package and call it **dao**
* In that create a new file and call it DbConnection.java

````     
  import java.sql.Connection;
  import java.sql.DriverManager;
  import java.sql.SQLException;
  
  public class DbConnection {
    private static String USERNAME = "USERNAME";
    private static String PASSWORD = "PASSWORD";
    private static String CONN_STRING = "jdbc:mysql://localhost:3306/KeaStudentsDb";

    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(CONN_STRING, USERNAME, PASSWORD);
    }
  }
````    

### StudentsDbRepsitory
Create a new repository, call it **StudentsDbRepsitory**, and remember to implement a **IStudentsRepository** interface.

Add the following attributes and constructor to the class

````     
    private ArrayList<Student> students = new ArrayList<>();
    private Connection conn = null;
    private PreparedStatement prepareStatement = null;
    private ResultSet result = null;
    
    public StudentDbRepository() {

        try {
           conn = DbConnection.getConnection();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
````    

In the readAll method add:

````     
    @Override
    public ArrayList<Student> readAll() {
        try {
            prepareStatement = conn.prepareStatement("SELECT * FROM students");
            result = prepareStatement.executeQuery();

            while (result.next())
            {
                students.add(new Student(result.getInt("students_id"),
                    result.getString("first_name"),
                    result.getString("last_name"),
                    result.getDate("enrollment_date").toLocalDate(),
                    result.getString("cpr"))
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }
        return students;
    }

````    

In the create method add:

````     
    @Override
    public void create(Student student) {

        try {

            preparedStatement = conn.prepareStatement("INSERT INTO students(first_name, last_name, enrollment_date, cpr)  VALUES (?,?,?, ?)");

            preparedStatement.setString(1, student.getFirstName());
            preparedStatement.setString(2, student.getLastName());
            preparedStatement.setDate(3, Date.valueOf(student.getEnrollmentDate()));
            preparedStatement.setString(4, student.getCpr());

            preparedStatement.execute();

        } catch (SQLException e) {
            e.printStackTrace();

        }
    }


````     

The important thing in the above is that you pass in values via ```` ? ````

````    
	INSERT INTO students(first_name, last_name, enrollment_date, cpr)  VALUES (?,?,?, ?)
	
	.....   
	
	preparedStatement.setString(1, student.getFirstName());
        preparedStatement.setString(2, student.getLastName());
        preparedStatement.setDate(3, Date.valueOf(student.getEnrollmentDate()));
        preparedStatement.setString(4, student.getCpr());

````


_<div align="right">&copy; clbo@kea.dk, 2018</div>_




