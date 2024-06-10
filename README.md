# Forta-Database-App
## Project Showcase: Three-Tier Architecture Application with Forta Database

### Objective:
Created an application using the sample database from the Forta textbook, implementing the data and business logic tiers while utilizing a pre-built user interface tier.

### Skills Learned:
- Database setup and management
- JDBC connection and SQL query execution
- Three-tier architecture implementation
- Java programming for database access

### Tools Used:
- NetBeans
- MySQL
- JDBC connector for MySQL
- Notepad++ (or another text editor)

### Steps and Observations:

#### Preparing the Project:

1. **Download and Modify Source Files:**
   - Downloaded source code files (DBAccess.java and FortaUI.java), database scripts (create.sql and populate.sql), and the JDBC connector (mysql-connector-java-5.1.7.bin.jar) from Canvas. Opened DBAccess.java and FortaUI.java in Notepad++, modified the package statements with my real name, and saved the files.
   - **Explanation:** These files are essential for setting up the project and database, and for implementing the data and business logic tiers. Customizing the package statements is necessary for proper organization and identification of the code.

2. **Start NetBeans:**
   - Launched NetBeans to create and manage the project.
   - **Explanation:** NetBeans IDE is used for developing and managing the Java project.

3. **Create Project and Package:**
   - Created a new project for HW4 in NetBeans and set up a package named faal.mohammad.forta.
   - **Explanation:** Organizing the code within a specific package helps maintain a clear project structure.

4. **Add Source Files:**
   - Copied DBAccess.java and FortaUI.java into the source code folder of the package (\src\faal\mohammad\forta).
   - **Explanation:** Adding these files to the correct directory ensures they are part of the project and can be compiled and executed.

5. **Set Up JDBC Connector:**
   - Created a "lib" folder in the project directory and copied the JDBC connector JAR file into it. Added the JAR to the project in NetBeans.
   - **Explanation:** The JDBC connector is required for database connectivity, and adding it to the project ensures the application can interact with the MySQL database.

#### Data Tier:

6. **Start MySQL and Create Database:**
   - Started MySQL, created a database named faalm, and used this database.
   - **Explanation:** Setting up the database is the first step in preparing the data tier for the application.

7. **Run Database Scripts:**
   - Executed create.sql and populate.sql to create and populate the Forta tables with data.
   - **Explanation:** Running these scripts sets up the necessary tables and data for the application to interact with.

8. **Verify Database Setup:**
    - Ran commands to show database tables and queried records to ensure the database is working correctly.
    - **Explanation:** Verifying the database setup ensures that the tables are created and populated as expected, providing a reliable data tier for the application.
  

#### Business Logic Tier:

9. **Create Connection String in DBAccess:**
    - Created a connection string in DBAccess.java to connect to the MySQL server on localhost, communicating with the created database using the specified credentials.
    - **Explanation:** The connection string is crucial for establishing a connection to the database from the Java application.

10. **Implement retrieveData() Method:**
    - Implemented the retrieveData() method to query the database, retrieve data from the specified table and column, and return it as an ArrayList.
    - **Explanation:** This method handles database access and retrieves data based on user input from the UI tier.

11. **Add Exception Handling:**
    - Used try-catch-finally structure in retrieveData() to handle potential exceptions during database access.
    - **Explanation:** Proper exception handling ensures the application can gracefully handle errors and return useful information.

12. **Return Query Results:**
    - Ensured the retrieveData() method returns an ArrayList containing the queried data.
    - **Explanation:** Returning the data as an ArrayList allows the UI tier to display the query results to the user.


#### User Interface Tier and Testing:

13. **Update FortaUI with Real Name:**
    - Changed the String literal from "Student" to my real name in Line 30 of FortaUI.java.
    - **Explanation:** Personalizing the UI title helps verify that the correct version of the application is running.

14. **Run FortaUI Class:**
    - Tested the application by running the FortaUI class, selecting a table and column, and clicking the “Search” button to display the query results.
    - **Explanation:** Tested the application ensures that all components (data, business logic, and UI tiers) are working together correctly.


### Source Code for Portfolio:

#### DBAccess.java
```java
/* 
   This class specializes in data access
   Author: Mohammad Faal
*/

package faal.mohammad.forta;

import java.sql.*;
import java.util.ArrayList;

public class DBAccess {
    // Build connection string
    public static String buildConnectionString() {
        // Customize connection string based on your database details
        String connectionString = "jdbc:mysql://localhost:3306/faalm?user=root&password=bcis3680";
        return connectionString;
    }

    public static ArrayList<String> retrieveData(String table, String column) {
        ArrayList<String> resultList = new ArrayList<>();
        Connection connection = null;
        Statement statement = null;
        ResultSet resultSet = null;

        try {
            // Build connection string
            String connectionString = buildConnectionString();

            // Create connection object
            connection = DriverManager.getConnection(connectionString);

            // Create Statement object
            statement = connection.createStatement();

            // Create SQL statement
            String sqlQuery = "SELECT " + column + " FROM " + table;

            // Run SQL statement
            resultSet = statement.executeQuery(sqlQuery);

            // Store records into array list
            while (resultSet.next()) {
                String value = resultSet.getString(1); // Assuming only one column is retrieved
                resultList.add(value);
            }

        } catch (SQLException se) {
            // Handle SQL exceptions
            System.out.println("Error: " + se.getMessage());
        } finally {
            // Close ResultSet, Statement, and Connection in finally block to ensure they are always closed
            try {
                if (resultSet != null) {
                    resultSet.close();
                }
                if (statement != null) {
                    statement.close();
                }
                if (connection != null) {
                    connection.close();
                }
            } catch (SQLException e) {
                System.out.println("Error while closing database resources: " + e.getMessage());
            }
        }

        // Return ArrayList
        return resultList;
    }
}
```

#### FortaGUI.java
```java
/* BCIS 3680 Forta Database Application
   User Interface Tier
   Author: Mohammad Faal
*/

package faal.mohammad.forta;

import javafx.application.Application;
import javafx.collections.FXCollections;
import javafx.collections.ObservableList;
import javafx.stage.Stage;
import javafx.scene.Scene;
import javafx.scene.control.Label;
import javafx.scene.control.ComboBox;
import javafx.scene.control.RadioButton;
import javafx.scene.control.ToggleGroup;
import javafx.scene.control.ListView;
import javafx.scene.control.TextArea;
import javafx.scene.control.Button;
import javafx.scene.layout.HBox;
import javafx.scene.layout.VBox;
import javafx.geometry.Insets;
import javafx.geometry.Pos;

import java.util.ArrayList;

public class FortaGUI extends Application
{
    // Variables
    String developer = "Mohammad Faal";
    Label lblTitle;
    Label lblSelTab, lblSelCol, lblTable, lblColumns, lblResults;
    RadioButton rdoCustomers, rdoVendors, rdoProducts, rdoOrders;
    ToggleGroup tgTables;
    ComboBox<String> cboColumns;
    ObservableList<String> colNames, data;
    ListView<String> lsvData;
    TextArea taResults; // Unused for now
    Button btnSearch, btnClose;

    // Containers for three sections
    VBox vbTables, vbColumns, vbOutput;
    // Container for entire window
    HBox hbDisplay;

    Scene scnMain;

    // For ListView contents
    String[] lsv_initial = {"Query result to be", "displayed here."};

    // For column lists
    String[] cust_cols = {"cust_id", "cust_name", "cust_address", "cust_city",
        "cust_state", "cust_zip", "cust_country", "cust_contact", "cust_email" };
    String[] vend_cols = {"vend_id", "vend_name", "vend_address", "vend_city",
        "vend_state", "vend_zip", "vend_country"};
    String[] prod_cols = {"prod_id", "vend_id", "prod_name", "prod_price", 
        "prod
