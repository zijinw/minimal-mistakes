---
layout: single
title:  "SpringMVC Note (11)"
date:   2017-07-28 17:24:00
categories: SpringMVC
---

# JDBC in Spring

Spring JDBC Framework takes care of all the low-level details starting from opening the connection, prepare and execute the SQL statement, process exceptions, handle transactions and finally close the connection. So what you have to do is just define the connection parameters and specify the SQL statement to be executed and do the required work for each iteration while fetching data from the database.

Spring JDBC provides several approaches and correspondingly different classes to interface with the database, and we will learn the **JdbcTemplate** class of Spring. This is the central framework class that manages all the database communication and exception handling.

This is also the class that is used in the project I am looking at.

## JdbcTemplate Class

The JDBC Template class executes SQL queries, updates statements, stores procedure calls, performs iteration over ResultSets, and extracts returned parameter values. It also catches JDBC exceptions and translates them to the generic, more informative, exception hierarchy defined in the org.springframework.dao package.


Instances of the JdbcTemplate class are threadsafe once configured. So you can configure a single instance of a JdbcTemplate and then safely inject this shared reference into multiple DAOs.

A common practice when using the JDBC Template class is to configure a DataSource in your Spring configuration file, and then dependency-inject that shared DataSource bean into your DAO classes, and the JdbcTemplate is created in the setter for the DataSource.

## Configuring Data Source

We create a **Student** table in the MySQL database **TEST**. It looks like this:

    CREATE TABLE Student(
       ID   INT NOT NULL AUTO_INCREMENT,
       NAME VARCHAR(20) NOT NULL,
       AGE  INT NOT NULL,
       PRIMARY KEY (ID)
    );


Now we need to supply a DataSource to the JDBC Template so it can configure itself to get database access. You can configure the DataSource in the XML file with:

    <bean id = "dataSource" 
       class = "org.springframework.jdbc.datasource.DriverManagerDataSource">
       <property name = "driverClassName" value = "com.mysql.jdbc.Driver"/>
       <property name = "url" value = "jdbc:mysql://localhost:3306/TEST"/>
       <property name = "username" value = "root"/>
       <property name = "password" value = "password"/>
    </bean>

## Data Access Object (DAO)

DAO stands for Data Access Object, which is commonly used for database interaction. DAOs exist to provide a means to read and write data to the database and they should expose this functionality through an interface by which the rest of the application will access them.

## Executing SQL statements

Let us see how we can perform CRUD (Create, Read, Update and Delete) operation on database tables using SQL and JDBC Template object.

### Querying for an integer

	String SQL = "select count(*) from Student";
	int rowCount = jdbcTemplateObject.queryForInt( SQL );

### Querying for a long

	String SQL = "select count(*) from Student";
	long rowCount = jdbcTemplateObject.queryForLong( SQL );

### A simple query using a bind variable

	String SQL = "select age from Student where id = ?";
	int age = jdbcTemplateObject.queryForInt(SQL, new Object[]{10});

Here the new Object[] {10} is the id which is needed to be passed.

### Querying for a String

	String SQL = "select name from Student where id = ?";
	String name = jdbcTemplateObject.queryForObject(SQL, new Object[]{10}, String.class)

Here the return type is declared by "String.class".

### Querying and returning an object

	String SQL = "select * from Student where id = ?";
	Student student = jdbcTemplateObject.queryForObject(SQL, new Object[]{10}, new StudentMapper());
	
	public class StudentMapper implements RowMapper<Student> {
		public Student mapRow(ResultSet rs, int rowNum) throws SQLException {
      		Student student = new Student();
      		student.setID(rs.getInt("id"));
      		student.setName(rs.getString("name"));
      		student.setAge(rs.getInt("age"));
      		
      		return student;
   		}
	}

### Querying and returning multiple objects

	String SQL = "select * from Student";
	List<Student> students = jdbcTemplateObject.query(SQL, new StudentMapper());
	
	public class StudentMapper implements RowMapper<Student> {
		public Student mapRow(ResultSet rs, int rowNum) throws SQLException {
      		Student student = new Student();
      		student.setID(rs.getInt("id"));
      		student.setName(rs.getString("name"));
      		student.setAge(rs.getInt("age"));
      	
      		return student;
		}
	}

### Inserting a row into the table

	String SQL = "insert into Student (name, age) values (?, ?)";
	jdbcTemplateObject.update( SQL, new Object[]{"Zara", 11} );

### Updating a row into the table

	String SQL = "update Student set name = ? where id = ?";
	jdbcTemplateObject.update( SQL, new Object[]{"Zara", 10} );

### Deleting a row from the table

	String SQL = "delete Student where id = ?";
	jdbcTemplateObject.update( SQL, new Object[]{20} );

## Executing DDL Statements

You can use the execute(..) method from jdbcTemplate to execute any SQL statements or DDL statements. Following is an example to use CREATE statement to create a table −

	String SQL = "CREATE TABLE Student( " +
		"ID   INT NOT NULL AUTO_INCREMENT, " +
		"NAME VARCHAR(20) NOT NULL, " +
		"AGE  INT NOT NULL, " +
		"PRIMARY KEY (ID));"

	jdbcTemplateObject.execute( SQL );

## Spring JDBC Framework Examples

























