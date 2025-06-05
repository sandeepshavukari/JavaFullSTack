# Java Frameworks Guide: Hibernate, Spring, Spring Boot, and Spring Cloud

## CO-1: Hibernate (ORM Tool/Framework)

### 1. Introduction
Hibernate is a powerful Java framework developed by Gavin King in 2003, designed to simplify database interactions in Java applications. It is an open-source, lightweight Object-Relational Mapping (ORM) tool that facilitates the conversion of data between Java objects and relational database tables.

- **ORM Concept**: ORM (Object-Relational Mapping) is a technique that maps Java objects to database tables, allowing developers to work with objects instead of writing complex SQL queries.
- **Functionality**: Hibernate handles the mapping of Java objects to database tables, enabling seamless interaction between the application and the database.
- **Popular ORM Tools**: Besides Hibernate, other widely used ORM tools include iBatis, MyBatis, and TopLink.

**Interaction Flow**:
```
Java Application --> Hibernate (ORM) --> Database
```

### 2. Why Hibernate?
Hibernate is preferred for the following reasons:
- **Simplified Database Interaction**: Reduces the complexity of database operations by allowing developers to work with Java objects.
- **Cross-Database Portability**: Supports multiple databases (e.g., Oracle, MySQL) without requiring significant code changes.

### 3. Advantages of Hibernate
- **Open Source and Lightweight**: Freely available and resource-efficient.
- **Fast Performance**: Optimized for high-performance database operations.
- **Database-Independent Queries**: Uses Hibernate Query Language (HQL), which is database-agnostic.
- **Automatic Table Creation**: Automatically generates database tables based on Java classes.
- **No Exception Handling Required**: Simplifies error handling compared to traditional JDBC.

### 4. Architecture
Hibernate's architecture consists of three main layers:
1. **Application Layer**: Contains the Java application logic.
2. **Hibernate Layer**: Manages mapping and configuration between Java objects and the database.
3. **Database Layer**: The actual database (e.g., Oracle, MySQL).

Key components include:
- **SessionFactory**: A thread-safe, immutable object that provides session objects for database operations.
- **Configuration**: Manages Hibernate configuration settings.
- **Transaction**: Controls database transactions.

### 5. POJO Class
A POJO (Plain Old Java Object) is a simple Java class that adheres to JavaBean conventions, containing private fields with public getters and setters. Hibernate uses POJOs to map Java objects to database tables.

**Example**:
```java
public class Employee {
    private int eid;
    private String ename;

    public int getEid() {
        return eid;
    }

    public void setEid(int eid) {
        this.eid = eid;
    }

    public String getEname() {
        return ename;
    }

    public void setEname(String ename) {
        this.ename = ename;
    }
}
```

### 6. Mapping File (XML)
The mapping file defines how a POJO class maps to a database table and its properties to table columns. It is typically an XML file (e.g., `employee.hbm.xml`) but can also use annotations.

**Syntax**:
```xml
<hibernate-mapping>
    <class name="POJO_class_name" table="table_name">
        <id name="class_property" column="column_name"/>
        <property name="class_property" column="column_name"/>
    </class>
</hibernate-mapping>
```

**Example** (employee.hbm.xml):
```xml
<hibernate-mapping>
    <class name="Employee" table="emp">
        <id name="eid" column="tid"/>
        <property name="ename" column="tname"/>
    </class>
</hibernate-mapping>
```

**Automatic Table Creation**:
If the table name and column names are not specified, Hibernate creates them automatically based on the POJO class and its properties.

**Example** (Automatic Table Creation):
```xml
<hibernate-mapping>
    <class name="Employee">
        <id name="eid"/>
        <property name="ename"/>
    </class>
</hibernate-mapping>
```
This creates a table named `Employee` with columns `eid` and `ename`.

### 7. Configuration File (XML)
The configuration file (`hibernate.cfg.xml`) defines database connection properties, Hibernate settings, and references to mapping files. One configuration file is required per database.

**Syntax**:
```xml
<hibernate-configuration>
    <session-factory>
        <!-- Connection Properties -->
        <property name="connection.driver_class">DriverClass</property>
        <property name="connection.url">JDBC_URL</property>
        <property name="connection.username">Username</property>
        <property name="connection.password">Password</property>
        <!-- Hibernate Properties -->
        <property name="show_sql">true</property>
        <property name="dialect">DatabaseDialect</property>
        <property name="hbm2ddl.auto">create</property>
        <!-- Mapping Files -->
        <mapping resource="mapping_file_name"/>
    </session-factory>
</hibernate-configuration>
```

**Example (Oracle)**:
```xml
<hibernate-configuration>
    <session-factory>
        <property name="connection.driver_class">oracle.jdbc.driver.OracleDriver</property>
        <property name="connection.url">jdbc:oracle:thin:@localhost:1521:xe</property>
        <property name="connection.username">system</property>
        <property name="connection.password">manager</property>
        <property name="show_sql">true</property>
        <property name="dialect">org.hibernate.dialect.Oracle10gDialect</property>
        <property name="hbm2ddl.auto">create</property>
        <mapping resource="employee.hbm.xml"/>
    </session-factory>
</hibernate-configuration>
```

**Example (MySQL)**:
```xml
<hibernate-configuration>
    <session-factory>
        <property name="connection.driver_class">com.mysql.cj.jdbc.Driver</property>
        <property name="connection.url">jdbc:mysql://localhost:3306/db</property>
        <property name="connection.username">root</property>
        <property name="connection.password">Sandeep@2004</property>
        <property name="show_sql">true</property>
        <property name="dialect">org.hibernate.dialect.MySQL5Dialect</property>
        <property name="hbm2ddl.auto">create</property>
        <mapping resource="employee.hbm.xml"/>
    </session-factory>
</hibernate-configuration>
```

### 8. Hibernate Examples (CRUD Operations)
A Hibernate application typically requires four files:
1. **POJO Class**: Represents the entity (e.g., `Employee.java`).
2. **Configuration File**: `hibernate.cfg.xml`.
3. **Mapping File**: `employee.hbm.xml`.
4. **Logic File**: Contains the main logic for CRUD operations.

**Steps to Create a Hibernate Application**:
1. Create a Maven project (`maven-archetype-quickstart`).
2. Update `pom.xml` with dependencies:
   - Hibernate Core
   - Hibernate Entity Manager
   - Database driver (e.g., MySQL, Oracle)
3. Create a `resources` folder under `src/main` for configuration and mapping files.
4. Implement:
   - POJO class (`src/main/java`)
   - Configuration file (`src/main/resources/hibernate.cfg.xml`)
   - Mapping file (`src/main/resources/employee.hbm.xml`)
   - Logic file (`src/main/java`)
5. Run the logic file as a Java application.

**Example (CRUD Logic File)**:
```java
import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;

public class HibernateCRUD {
    public static void main(String[] args) {
        SessionFactory sf = new Configuration().configure("hibernate.cfg.xml").buildSessionFactory();
        Session s = sf.openSession();
        Transaction t = s.beginTransaction();

        // Create
        Employee e = new Employee();
        e.setEid(101);
        e.setEname("John Doe");
        s.save(e);

        // Read
        Employee emp = s.get(Employee.class, 101);
        System.out.println("Employee: " + emp.getEname());

        // Update
        emp.setEname("Jane Doe");
        s.update(emp);

        // Delete
        s.delete(emp);

        t.commit();
        s.close();
        sf.close();
    }
}
```

### 9. Hibernate Query Language (HQL)
HQL is a database-independent query language similar to SQL but operates on POJO classes and their properties instead of database tables.

- **Query Interface**: Used to execute HQL queries. Obtained via `session.createQuery()`.
- **Key Methods**:
  - `executeUpdate()`: For DML operations (INSERT, UPDATE, DELETE).
  - `list()`: Retrieves all records.
  - `setFirstResult()`: Sets the starting record for pagination.
  - `setMaxResults()`: Limits the number of records retrieved.
  - `setParameter()`: Binds parameters to queries.

**Examples**:
1. **Retrieve All Records (Using For-Each Loop)**:
```java
Query q = session.createQuery("FROM Employee");
List<Employee> employees = q.list();
for (Employee e : employees) {
    System.out.println(e.getEid() + " " + e.getEname());
}
```

2. **Retrieve Specific Records (Pagination)**:
```java
Query q = session.createQuery("FROM Employee");
q.setFirstResult(5);
q.setMaxResults(15);
List<Employee> employees = q.list();
```

3. **Update Records**:
```java
Query q = session.createQuery("UPDATE Employee SET ename = :n WHERE eid = :i");
q.setParameter("n", "XYZ");
q.setParameter("i", 111);
q.executeUpdate();
```

4. **Delete Records**:
```java
Query q = session.createQuery("DELETE FROM Employee WHERE eid = :i");
q.setParameter("i", 111);
q.executeUpdate();
```

5. **Insert Records**:
```java
Query q = session.createQuery("INSERT INTO Employee(eid, ename, esal) VALUES(:id, :name, :sal)");
q.setParameter("id", 111);
q.setParameter("name", "Qwerty");
q.setParameter("sal", 70000.0);
q.executeUpdate();
```

### 10. Hibernate Criteria Query Language (HCQL)
HCQL allows querying the database using a programmatic, object-oriented approach via the `Criteria` interface.

- **Criteria Interface**: Obtained via `session.createCriteria()`.
- **Key Methods**:
  - `add()`: Adds restrictions (conditions).
  - `addOrder()`: Specifies sorting order.
  - `list()`: Retrieves results.
  - `setFirstResult()`: Pagination start.
  - `setMaxResults()`: Pagination limit.

- **Restriction Class**: Provides methods for query conditions:
  - `lt()`: Less than
  - `le()`: Less than or equal
  - `gt()`: Greater than
  - `ge()`: Greater than or equal
  - `eq()`: Equal
  - `ne()`: Not equal

- **Order Class**: Supports sorting:
  - `asc()`: Ascending order
  - `desc()`: Descending order

**Example**:
```java
Criteria cr = session.createCriteria(Employee.class);
cr.add(Restrictions.eq("ename", "John Doe"));
cr.addOrder(Order.asc("eid"));
List<Employee> employees = cr.list();
```

### 11. Inheritance Mapping
Inheritance mapping in Hibernate allows mapping of class hierarchies to database tables. Hibernate supports three strategies:
1. **Table Per Class**: A single table for the entire class hierarchy.
2. **Table Per Subclass**: Separate tables for each subclass, linked to the parent table.
3. **Table Per Concrete Class**: Separate tables for each concrete class.

**Example (Table Per Class)**:
- **POJO Classes**:
```java
public class Payment {
    private int pid;
    private double pamount;
    // Getters and setters
}

public class Card extends Payment {
    private String cardType;
    // Getters and setters
}

public class Cheque extends Payment {
    private String chequeType;
    // Getters and setters
}
```

- **Mapping File** (payment.hbm.xml):
```xml
<hibernate-mapping>
    <class name="Payment" table="payment">
        <id name="pid" column="pid"/>
        <property name="pamount" column="pamount"/>
        <subclass name="Card" discriminator-value="c">
            <property name="cardType" column="card_type"/>
        </subclass>
        <subclass name="Cheque" discriminator-value="ch">
            <property name="chequeType" column="cheque_type"/>
        </subclass>
    </class>
</hibernate-mapping>
```

- **Logic File**:
```java
Session s = sf.openSession();
Transaction t = s.beginTransaction();

Card c = new Card();
c.setPidEdge (101);
c.setPamount(145000);
c.setCardType("Credit Card");

Cheque cq = new Cheque();
cq.setPid(201);
cq.setPamount(150000);
cq.setChequeType("RTGS");

s.save(c);
s.save(cq);
t.commit();
s.close();
sf.close();
```

## CO-2: Spring

### 12. Introduction
Spring is a comprehensive Java framework developed by Rod Johnson in 2003, designed for building enterprise-level applications, including console and web applications. It is lightweight and simplifies Java development by promoting good design practices.

- **Modules**: Includes IoC (Inversion of Control), DAO (Data Access Object), ORM, and Web-MVC.

### 13. Why Spring?
- **Versatility**: Unlike Hibernate, which is limited to console applications, Spring supports both console and web applications.
- **Comprehensive Framework**: Provides a wide array of features beyond data persistence, unlike Hibernate's focus on ORM.
- **Integration**: Works seamlessly with various ORM frameworks, including Hibernate.

### 14. Advantages of Spring
- **Lightweight**: Minimal resource usage.
- **Fast Performance**: Optimized for quick execution.
- **Easy to Test and Maintain**: Simplifies testing with dependency injection.
- **Loosely Coupled**: Reduces dependencies between components.
- **Predefined Templates**: Simplifies development with reusable templates.

### 15. Spring Architecture
Spring's modular architecture includes:
- **Core Container**: Manages beans and dependency injection.
- **DAO**: Simplifies database access.
- **ORM**: Integrates with ORM tools like Hibernate.
- **Web-MVC**: Supports web application development.
- **AOP**: Aspect-Oriented Programming for cross-cutting concerns.

### 16. Dependency Injection (DI)
DI is a design pattern where dependencies are injected externally rather than created within the class, promoting loosely coupled code.

- **Types**:
  - **Setter Injection**: Injects dependencies via setter methods.
  - **Constructor Injection**: Injects dependencies via constructors.

**Example (Setter Injection)**:
- **Bean Class**:
```java
public class Employee {
    private int eid;
    private String ename;
    private double esal;

    public void setEid(int eid) { this.eid = eid; }
    public int getEid() { return eid; }
    public void setEname(String ename) { this.ename = ename; }
    public String getEname() { return ename; }
    public void setEsal(double esal) { this.esal = esal; }
    public double getEsal() { return esal; }
}
```

- **Configuration File** (spconfig.xml):
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="empbean" class="com.example.Employee">
        <property name="eid" value="111"/>
        <property name="ename" value="ABC"/>
        <property name="esal" value="25000"/>
    </bean>
</beans>
```

- **Logic File**:
```java
import org.springframework.beans.factory.xml.XmlBeanFactory;
import org.springframework.core.io.ClassPathResource;

public class Test {
    public static void main(String[] args) {
        XmlBeanFactory bf = new XmlBeanFactory(new ClassPathResource("spconfig.xml"));
        Employee e = (Employee) bf.getBean("empbean");
        System.out.println(e.getEid() + " " + e.getEname() + " " + e.getEsal());
    }
}
```

### 17. Inversion of Control (IoC)
IoC is a design pattern where the control of object creation and management is delegated to a container. Spring’s IoC container manages beans and their dependencies.

- **IoC Containers**:
  - **BeanFactory**: Basic container for managing beans.
  - **ApplicationContext**: Advanced container with additional features like event propagation.

**Example (Constructor Injection with ApplicationContext)**:
- **Bean Class**:
```java
public class Employee {
    private int eid;
    private String ename;
    private double esal;

    public Employee(int eid, String ename, double esal) {
        this.eid = eid;
        this.ename = ename;
        this.esal = esal;
    }

    public int getEid() { return eid; }
    public String getEname() { return ename; }
    public double getEsal() { return esal; }
}
```

- **Configuration File** (spconfig.xml):
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="empbean" class="com.example.Employee">
        <constructor-arg value="111" type="int"/>
        <constructor-arg value="ABC" type="java.lang.String"/>
        <constructor-arg value="25000" type="double"/>
    </bean>
</beans>
```

- **Logic File**:
```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Test {
    public static void main(String[] args) {
        ApplicationContext ac = new ClassPathXmlApplicationContext("spconfig.xml");
        Employee e = (Employee) ac.getBean("empbean");
        System.out.println(e.getEid() + " " + e.getEname() + " " + e.getEsal());
    }
}
```

### 18. Primitive and Non-Primitive Data Types
Spring supports injecting both primitive and non-primitive data types into beans.

- **Primitive Data Types**: Basic types like `int`, `double`, `String`.
- **Non-Primitive Data Types**: Collections (`List`, `Set`, `Map`) or objects.

**Example (List Injection)**:
- **Bean Class**:
```java
import java.util.List;

public class Question {
    private int qid;
    private String qname;
    private List<String> answers;

    public Question(int qid, String qname, List<String> answers) {
        this.qid = qid;
        this.qname = qname;
        this.answers = answers;
    }

    public void display() {
        System.out.println("Question: " + qname);
        System.out.println("Answers:");
        for (String answer : answers) {
            System.out.println(answer);
        }
    }
}
```

- **Configuration File** (spconfig.xml):
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="qbean" class="com.example.Question">
        <constructor-arg value="111" type="int"/>
        <constructor-arg value="What is your favorite color?" type="java.lang.String"/>
        <constructor-arg>
            <list>
                <value>Blue</value>
                <value>Green</value>
                <value>Red</value>
                <value>Yellow</value>
            </list>
        </constructor-arg>
    </bean>
</beans>
```

- **Logic File**:
```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Test {
    public static void main(String[] args) {
        ApplicationContext ac = new ClassPathXmlApplicationContext("spconfig.xml");
        Question q = (Question) ac.getBean("qbean");
        q.display();
    }
}
```

### 19. Spring DAO (Data Access Objects) using JDBC
Spring DAO simplifies database access by providing a consistent framework for data operations.

**Example** (Spring DAO with JDBC):
- **Bean Class**:
```java
public class Employee {
    private int eid;
    private String ename;
    private double esal;
    // Getters and setters
}
```

- **DAO Interface**:
```java
public interface EmployeeDAO {
    void insertEmployee(Employee emp);
    Employee findEmployee(int eid);
}
```

- **DAO Implementation**:
```java
import org.springframework.jdbc.core.JdbcTemplate;

public class EmployeeDAOImpl implements EmployeeDAO {
    private JdbcTemplate jdbcTemplate;

    public void setJdbcTemplate(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    @Override
    public void insertEmployee(Employee emp) {
        String sql = "INSERT INTO employee (eid, ename, esal) VALUES (?, ?, ?)";
        jdbcTemplate.update(sql, emp.getEid(), emp.getEname(), emp.getEsal());
    }

    @Override
    public Employee findEmployee(int eid) {
        String sql = "SELECT * FROM employee WHERE eid = ?";
        return jdbcTemplate.queryForObject(sql, new Object[]{eid}, (rs, rowNum) -> {
            Employee emp = new Employee();
            emp.setEid(rs.getInt("eid"));
            emp.setEname(rs.getString("ename"));
            emp.setEsal(rs.getDouble("esal"));
            return emp;
        });
    }
}
```

- **Configuration File** (applicationContext.xml):
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/db"/>
        <property name="username" value="root"/>
        <property name="password" value="Sandeep@2004"/>
    </bean>
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <bean id="employeeDAO" class="com.example.EmployeeDAOImpl">
        <property name="jdbcTemplate" ref="jdbcTemplate"/>
    </bean>
</beans>
```

- **Logic File**:
```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Test {
    public static void main(String[] args) {
        ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
        EmployeeDAO dao = (EmployeeDAO) ac.getBean("employeeDAO");
        Employee e = new Employee();
        e.setEid(111);
        e.setEname("ABC");
        e.setEsal(50000);
        dao.insertEmployee(e);
    }
}
```

### 20. Spring DAO using Hibernate
Spring integrates with Hibernate through the `HibernateTemplate` class, which simplifies database interactions.

**Example**:
- **Bean Class** (Employee.java):
```java
public class Employee {
    private int eid;
    private String ename;
    private double esal;
    // Getters and setters
}
```

- **Mapping File** (employee.hbm.xml):
```xml
<hibernate-mapping>
    <class name="com.example.Employee" table="employee">
        <id name="eid" column="eid"/>
        <property name="ename" column="ename"/>
        <property name="esal" column="esal"/>
    </class>
</hibernate-mapping>
```

- **DAO Interface** (EmployeeDAO.java):
```java
public interface EmployeeDAO {
    void insertEmployee(Employee emp);
    Employee findEmployee(int eid);
    List<Employee> getEmployees();
}
```

- **DAO Implementation** (EmployeeDAOImpl.java):
```java
import org.springframework.orm.hibernate5.HibernateTemplate;
import java.util.List;

public class EmployeeDAOImpl implements EmployeeDAO {
    private HibernateTemplate ht;

    public void setHt(HibernateTemplate ht) {
        this.ht = ht;
    }

    @Override
    public void insertEmployee(Employee emp) {
        ht.save(emp);
    }

    @Override
    public Employee findEmployee(int eid) {
        return ht.load(Employee.class, eid);
    }

    @Override
    public List<Employee> getEmployees() {
        return ht.loadAll(Employee.class);
    }
}
```

- **Configuration File** (applicationContext.xml):
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/db"/>
        <property name="username" value="root"/>
        <property name="password" value="Sandeep@2004"/>
    </bean>
    <bean id="sessionFactory" class="org.springframework.orm.hibernate5.LocalSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <property name="mappingResources">
            <list>
                <value>employee.hbm.xml</value>
            </list>
        </property>
        <property name="hibernateProperties">
            <props>
                <prop key="hibernate.dialect">org.hibernate.dialect.MySQL5Dialect</prop>
                <prop key="hibernate.show_sql">true</prop>
                <prop key="hibernate.hbm2ddl.auto">update</prop>
            </props>
        </property>
    </bean>
    <bean id="hibernateTemplate" class="org.springframework.orm.hibernate5.HibernateTemplate">
        <property name="sessionFactory" ref="sessionFactory"/>
    </bean>
    <bean id="employeeDAO" class="com.example.EmployeeDAOImpl">
        <property name="ht" ref="hibernateTemplate"/>
    </bean>
</beans>
```

- **Logic File** (Test.java):
```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Test {
    public static void main(String[] args) {
        ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
        EmployeeDAO dao = (EmployeeDAO) ac.getBean("employeeDAO");
        Employee e = new Employee();
        e.setEid(111);
        e.setEname("ABC");
        e.setEsal(50000);
        dao.insertEmployee(e);
    }
}
```

### 21. Spring MVC
Spring MVC is a framework for building web applications using the Model-View-Controller (MVC) design pattern.

- **Model**: Represents business logic and data (backend).
- **View**: Handles the user interface (frontend).
- **Controller**: Acts as an intermediary between Model and View.

**Architecture**:
1. **Http Request**: Sent to the DispatcherServlet.
2. **DispatcherServlet**: Routes the request to the appropriate controller via HandlerMapping.
3. **Controller**: Processes the request and returns a ModelAndView object.
4. **ViewResolver**: Maps the view name to the actual view (e.g., JSP, HTML).
5. **View**: Renders the response to the user.

**Advantages**:
- Lightweight and rapid development.
- Clear separation of roles.
- Reusable code and easy testing.

**Example**:
- **Steps**:
  1. Create a Maven web project (`maven-archetype-webapp`).
  2. Add dependencies: Spring Web MVC, Servlet API.
  3. Configure `web.xml` for DispatcherServlet.
  4. Create a Spring configuration file (e.g., `spring-servlet.xml`).
  5. Implement controllers and views.

- **web.xml**:
```xml
<web-app>
    <servlet>
        <servlet-name>spring</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>spring</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

- **Spring Configuration** (spring-servlet.xml):
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">
    <context:component-scan base-package="com.example"/>
    <mvc:annotation-driven/>
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/views/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
</beans>
