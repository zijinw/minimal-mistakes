
## Create a Hello World Spring Project Sample

Before reading this note, make sure you have installed:

1. Java Development Kit(JDK);
2. Java IDE. Here I use IntelliJ Idea;
3. Apache Maven.


First, create a new Maven project in Idea. There are a lot of online tutorials on this, like [here](https://javapointers.com/tutorial/creating-web-application-using-maven-in-intellij/), so here we just skip it. The pom.xml file I created is like this:
    
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

Then, in the newly created "pom.xml" file, add following 