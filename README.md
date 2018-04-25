# 06 Tutorial: Preparedstatements, mysql and spring
## Connect mysql and a Spring Boot Applications using Preparedstatements

> This is an alternative to using the Spring Frameworks [Default Solution which we cover in teachings](https://github.com/Dat17aSpring/06_Tutorial_MySql_JDBC).

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
    private static String CONN_STRING = "jdbc:mysql://den1.mysql2.gear.host/DATABASENAME";

    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(CONN_STRING, USERNAME, PASSWORD);
    }
  }
````    

### UserDbRepsitory
Create a new repository, call it **UserDbRepsitory**, and remember to implement a **IUserRepository** interface.

Add the following attributes and constructor to the class

````     
    private ArrayList<User> users = new ArrayList<>();
    private Connection conn = null;
    private PreparedStatement prepareStatement = null;
    private ResultSet result = null;
    
    public StudentInMemoryRepository() {

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
    public ArrayList<User> readAll() {
        try {
            prepareStatement = conn.prepareStatement("SELECT * FROM users");
            result = prepareStatement.executeQuery();

            while (result.next())
            {
                int id = result.getInt("users_id");
                String userName = result.getString("user_name");
                String email = result.getString("email");
                String password = result.getString("password");

                System.out.println(id + ", " + userName + ", " + email + ", " + password );

                userss.add(new User(id, userName, email, password));
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }
        return users;
    }

````    

_<div align="right">&copy; clbo@kea.dk, 2018</div>_




