---
layout: single
title:  "SpringMVC Note (2)"
date:   2017-07-25 16:10:00
categories: SpringMVC
---

## Create a Hello World Spring Project Sample

Before reading this note, make sure you have installed:

1. Java Development Kit(JDK);
2. Java IDE. Here I use IntelliJ Idea;
3. Apache Maven.


First, create a new Maven project in Idea. There are a lot of online tutorials on this, like [this](https://javapointers.com/tutorial/creating-web-application-using-maven-in-intellij/), so here we just skip it. The pom.xml file of Maven that I created is like this:
    
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    	<modelVersion>4.0.0</modelVersion>
      		<groupId>springTutorial</groupId>
      		<artifactId>springTutorial</artifactId>
      		<packaging>war</packaging>
      		<version>1.0-SNAPSHOT</version>
      		<name>springTutorial Maven Webapp</name>
      		<url>http://maven.apache.org</url>
      		<dependencies>
    			<dependency>
      				<groupId>junit</groupId>
      				<artifactId>junit</artifactId>
      				<version>3.8.1</version>
      				<scope>test</scope>
    			</dependency>
      		</dependencies>
      		<build>
    			<finalName>springTutorial</finalName>
      		</build>
    </project>

The interpretation of these elements can be found [here](https://spring.io/guides/gs/maven/). Then, in the newly created "pom.xml" file, add external JARs from Spring framework. Like below, we add several Spring JARS into the "dependency" element, such as Spring-core, Spring-web, Spring-context and Spring-jdbc. The introduction of these modules can be found in the last note. The pom.xml file will then be

    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
      <modelVersion>4.0.0</modelVersion>
      <groupId>springTutorial</groupId>
      <artifactId>springTutorial</artifactId>
      <packaging>war</packaging>
      <version>1.0-SNAPSHOT</version>
      <name>springTutorial Maven Webapp</name>
      <url>http://maven.apache.org</url>
      <dependencies>
    	<dependency>
      		<groupId>junit</groupId>
      		<artifactId>junit</artifactId>
      		<version>3.8.1</version>
      		<scope>test</scope>
    	</dependency>
    	<dependency>
      		<groupId>org.springframework</groupId>
      		<artifactId>spring-core</artifactId>
      		<version>4.3.7.RELEASE</version>
    	</dependency>
    	<dependency>
      		<groupId>org.springframework</groupId>
      		<artifactId>spring-jdbc</artifactId>
      		<version>4.3.7.RELEASE</version>
    	</dependency>
    	<dependency>
      		<groupId>org.springframework</groupId>
      		<artifactId>spring-context</artifactId>
      		<version>4.3.7.RELEASE</version>
    	</dependency>
    	<dependency>
      		<groupId>org.springframework</groupId>
      		<artifactId>spring-aop</artifactId>
      		<version>4.3.7.RELEASE</version>
    	</dependency>
    	<dependency>
      		<groupId>org.springframework</groupId>
      		<artifactId>spring-beans</artifactId>
      		<version>4.3.7.RELEASE</version>
    	</dependency>
    	<dependency>
      		<groupId>org.springframework</groupId>
      		<artifactId>spring-webmvc</artifactId>
      		<version>4.3.7.RELEASE</version>
    	</dependency>
    	<dependency>
      		<groupId>org.springframework</groupId>
      		<artifactId>spring-web</artifactId>
      		<version>4.3.7.RELEASE</version>
    	</dependency>
    	<dependency>
      		<groupId>javax.servlet</groupId>
      		<artifactId>jstl</artifactId>
      		<version>1.2</version>
    	</dependency>
      </dependencies>
      <build>
    	<finalName>springTutorial</finalName>
    	<plugins>
      		<plugin>
    			<artifactId>maven-compiler-plugin</artifactId>
    			<version>3.1</version>
    			<configuration>
      				<source>1.8</source>
      				<target>1.8</target>
    			</configuration>
      		</plugin>
    	</plugins>
      </build>
    </project>
    
This is just a Hello World demo so maybe some of these JARs will not be used. Actually in real work the dependency of external JARs completely depends on your own needs in the project.

After configuring this pom file, we can write some codes now. First, note that Idea didn't create a package for us, and we need to do this by ourselves. Create a "java" directory under src-main, this name indicates that we will put our source codes here. In the idea, mark this directory as a Source Root. Then the color of this directory will turn blue. Under this "java" directory, create a package. Here I will call it "com.springtutorial", like below.

![spring2-1](https://raw.githubusercontent.com/zijinw/BlogPic/master/pictures/spring2-1.png)

In the com.springtutorial, create a HelloWorld.java and a MainApp.java.

The HelloWorld.java will be like this:

    public class HelloWorld {
    	private String message;
    	public void setMessage(String message) {
        	this.message = message;
    	}
    	public void getMessage() {
        	System.out.println("Your Message: " + message);
    	}
    }
This class is very easy to understand. We can set the message and print it out.

The MainApp.java will be like this:

    package com.springtutorial;

	/**
	 * Created by Zijin Wang on 7/25/2017.
 	*/
	import org.springframework.context.ApplicationContext;
	import org.springframework.context.support.ClassPathXmlApplicationContext;
	public class MainApp {
    	public static void main(String[] args) {
        	ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
        	HelloWorld obj = (HelloWorld) context.getBean("helloWorld");
        	obj.getMessage();
    	}
	}
Note that in this class we first time meet the spring framework. We use an API provided by Spring, **ClassPathXmlApplicationContext**, to create an an **application context**. This API loads **beans** configuration file and eventually based on the provided API, it takes care of creating and initializing all the objects, which are beans mentioned in the configuration file. Then, the created context get the required bean by getBean(). This method uses bean ID to return a generic object, which finally can be casted to the actual object.

So, what exactly is this **Beans.xml**? It is a configuration file that works as a cement to glues all the beans (the classes). Now we need to write something in it so that we can let our source code work. First, create a Beans.xml file (the name of this file is up to you, but it must match the name used in the main method). It's location is here:

![](https://raw.githubusercontent.com/zijinw/BlogPic/master/pictures/spring2-2.png)

The Beans.xml is used to assign unique IDs to different beans and to control the creation of objects with different values without impacting any of the Spring source files. For example, using the following file you can pass any value for "message" variable and you can print different values of message without impacting HelloWorld.java and MainApp.java files (very amazing to a rookie like me).

    <?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    	<bean id = "helloWorld" class = "com.springtutorial.HelloWorld">
        	<property name = "message" value = "Hello World!"/>
    	</bean>
	</beans>

**Note: here the bean id should be the same as the one you set above in the getBean() method.**

When Spring application gets loaded into the memory, Framework makes use of the above configuration file to create all the beans defined and assigns them a unique ID as defined in <bean> tag. You can use <property> tag to pass the values of different variables used at the time of object creation. Here we give the class "com.springtutorial.HelloWorld"" an ID called helloWorld, and set the value of "message" variable as "Hello World". Then, as we have seen, in the main method we use getBean() method to get this helloWorld bean, and return a object which we cast it to a HelloWorld instance. At last we use the getMessage() method to print the message. The output is like below.

![](https://raw.githubusercontent.com/zijinw/BlogPic/master/pictures/spring2-3.png)


    Your Message: Hello World!

Now we successfully create a sample Spring Application. It can be seen that it's very convenient for us to change the value of message without changing the source files.
