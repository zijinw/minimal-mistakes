---
layout: single
title:  "SpringMVC Note (2)"
date:   2017-07-24 20:00:00
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


![spring2-1](images\spring2-1.png)

In the com.springtutorial, create a HelloWorld.java and a MainApp.java.


