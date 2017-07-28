---
layout: single
title:  "SpringMVC Note (10)"
date:   2017-07-28 13:00:00
categories: SpringMVC
---

# Spring Aspect Oriented Programming

As we have learned in the first note, the Aspect Oriented Programming (AOP) is a key component of Spring. Aspect-Oriented Programming entails breaking down program logic into distinct parts called so-called concerns. The functions that span multiple points of an application are called cross-cutting concerns and these cross-cutting concerns are conceptually separate from the application's business logic. There are various common good examples of aspects like logging, auditing, declarative transactions, security, caching, etc.

The key unit of modularity in OOP is the class, whereas in AOP the unit of modularity is the aspect. Dependency Injection helps you decouple your application objects from each other and AOP helps you decouple cross-cutting concerns from the objects that they affect. AOP is like triggers in programming languages such as Perl, .NET, Java, and others.

Spring AOP module provides interceptors to intercept an application. For example, when a method is executed, you can add extra functionality before or after the method execution.

## AOP terminologies

Next are some common terminologies in AOP.

1.	**Aspect**. This is a module which has a set of APIs providing cross-cutting requirements. For example, a logging module would be called AOP aspect for logging. An application can have any number of aspects depending on the requirement.

2.	**Join point**. This represents a point in your application where you can plug-in the AOP aspect. You can also say, it is the actual place in the application where an action will be taken using Spring AOP framework.

3.	**Advice**. This is the actual action to be taken either before or after the method execution. This is an actual piece of code that is invoked during the program execution by Spring AOP framework.

4.	**Pointcut**. This is a set of one or more join points where an advice should be executed. You can specify pointcuts using expressions or patterns as we will see in our AOP examples.

5.	**Introduction**. An introduction allows you to add new methods or attributes to the existing classes.

6.	**Target object**. The object being advised by one or more aspects. This object will always be a proxied object, also referred to as the advised object.

7. **Weaving**. Weaving is the process of linking aspects with other application types or objects to create an advised object. This can be done at compile time, load time, or at runtime.

We now know that AOP has a terminology called "advice". It has the following types:

1.	**before**. Run advice before the a method execution.

2.	**after**. Run advice after the method execution, regardless of its outcome.

3.	**after-returning**. Run advice after the a method execution only if method completes successfully.

4.	**after-throwing**. Run advice after the a method execution only if method exits by throwing an exception.

5.	**around**. Run advice before and after the advised method is invoked.

Spring has two different ways to implement aspects. Next are examples of these two.

## XML Schema based

In order to use XML Schema based AOP, we first need to import springAOP schema in the XML:

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns = "http://www.springframework.org/schema/beans"
       xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop = "http://www.springframework.org/schema/aop"
       xsi:schemaLocation = "http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop-3.0.xsd ">
	
    <!-- bean definition & AOP specific configuration -->
	    
	</beans>

You will also need the following AspectJ libraries on the CLASSPATH of your application. With maven, we can easily import them. Add the following codes into the <dependencies> element of pom.xml of your maven project.

    <!-- https://mvnrepository.com/artifact/org.aspectj/aspectjrt -->
    <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>aspectjrt</artifactId>
      <version>1.8.10</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
    <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>aspectjweaver</artifactId>
      <version>1.8.10</version>
    </dependency>
	
    <!-- https://mvnrepository.com/artifact/aopalliance/aopalliance -->
    <dependency>
      <groupId>aopalliance</groupId>
      <artifactId>aopalliance</artifactId>
      <version>1.0</version>
    </dependency>

### Declaring an aspect

An aspect is declared using the <aop:aspect> element, and the backing bean is referenced using the ref attribute as follows −

	<aop:config>
		<aop:aspect id = "myAspect" ref = "aBean">
    	  ...
		</aop:aspect>
	</aop:config>
	
	<bean id = "aBean" class = "...">
   		...
	</bean>

Here "aBean" will be configured and dependency injected just like any other Spring bean as you have seen in the previous chapters.

### Declaring a pointcut

A pointcut helps in determining the join points (ie methods) of interest to be executed with different advices. While working with XML Schema-based configuration, pointcut will be defined as follows −

	<aop:config>
		<aop:aspect id = "myAspect" ref = "aBean">
      		<aop:pointcut id = "businessService" 
			expression = "execution(*com.xyz.myapp.service.*.*(..))"/>
         		...
		</aop:aspect>
	</aop:config>

	<bean id = "aBean" class = "...">
		...
	</bean>

The following example defines a pointcut named 'businessService' that will match the execution of getName() method available in the Student class under the package com.tutorialspoint −

	<aop:config>
		<aop:aspect id = "myAspect" ref = "aBean">
      		<aop:pointcut id = "businessService" 
         	expression = "execution(*com.tutorialspoint.Student.getName(..))"/>
         	...
		</aop:aspect>
	</aop:config>
	
	<bean id = "aBean" class = "...">
		...
	</bean>

(Does this mean, when performing getName(), the aspect will be triggered?)

### Declaring advices

You can declare any of the five advices inside an <aop:aspect> using the <aop:{ADVICE NAME}> element as given below −

    <aop:config>
        <aop:aspect id = "myAspect" ref = "aBean">
            <aop:pointcut id = "businessService"
                          expression = "execution(* com.xyz.myapp.service.*.*(..))"/>

            <!-- a before advice definition -->
            <aop:before pointcut-ref = "businessService" method = "doRequiredTask"/>

            <!-- an after advice definition -->
            <aop:after pointcut-ref = "businessService" method = "doRequiredTask"/>

            <!-- an after-returning advice definition -->
            <!--The doRequiredTask method must have parameter named retVal -->
            <aop:after-returning pointcut-ref = "businessService"
                                 returning = "retVal" method = "doRequiredTask"/>

            <!-- an after-throwing advice definition -->
            <!--The doRequiredTask method must have parameter named ex -->
            <aop:after-throwing pointcut-ref = "businessService"
                                throwing = "ex" method = "doRequiredTask"/>

            <!-- an around advice definition -->
            <aop:around pointcut-ref = "businessService" method = "doRequiredTask"/>
            ...
        </aop:aspect>
    </aop:config>

    <bean id = "aBean" class = "...">
        ...
    </bean>

Actually now I am still confused about how to use AOP. Fortunately, here is an example.

## An Example

Here is the content of Logging.java file. This is actually a sample of aspect module which defines the methods to be called at various points.

	package com.springtutorial;

	public class Logging {
	    /**
	     * This is the method which I would like to execute
	     * before a selected method execution.
	     */
	    public void beforeAdvice(){
	        System.out.println("Going to setup student profile.");
	    }
	
	    /**
	     * This is the method which I would like to execute
	     * after a selected method execution.
	     */
	    public void afterAdvice(){
	        System.out.println("Student profile has been setup.");
	    }
	
	    /**
	     * This is the method which I would like to execute
	     * when any method returns.
	     */
	    public void afterReturningAdvice(Object retVal) {
	        System.out.println("Returning:" + retVal.toString() );
	    }
	
	    /**
	     * This is the method which I would like to execute
	     * if there is an exception raised.
	     */
	    public void AfterThrowingAdvice(IllegalArgumentException ex){
	        System.out.println("There has been an exception: " + 	ex.toString());
	    }
	}

Then we need another Student.java:

	package com.springtutorial;
	
	public class Student {
	    private Integer age;
	    private String name;
	
	    public void setAge(Integer age) {
	        this.age = age;
	    }
	    public Integer getAge() {
	        System.out.println("Age : " + age );
	        return age;
	    }
	    public void setName(String name) {
	        this.name = name;
	    }
	    public String getName() {
	        System.out.println("Name : " + name );
	        return name;
	    }
	    public void printThrowException(){
	        System.out.println("Exception raised");
	        throw new IllegalArgumentException();
	    }
	}

And in the main method:

	package com.springtutorial;
	
	import org.springframework.context.ApplicationContext;
	import org.springframework.context.support.ClassPathXmlApplicationContext;
	public class MainApp {
    	public static void main(String[] args) {
    	    ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
	
    	    Student student = (Student) context.getBean("student");
    	    student.getName();
    	    student.getAge();
    	    student.printThrowException();
    	}
	}

Note that in the main method we never mention the methods defined in the Logging.

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns = "http://www.springframework.org/schema/beans"
    	   xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
    	   xmlns:aop = "http://www.springframework.org/schema/aop"
    	   xsi:schemaLocation = "http://www.springframework.org/schema/beans
    	   http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    	   http://www.springframework.org/schema/aop
    	   http://www.springframework.org/schema/aop/spring-aop-3.0.xsd ">
	
    	<!-- bean definition & AOP specific configuration -->
    	<aop:config>
    	    <aop:aspect id = "log" ref = "logging">
    	        <aop:pointcut id = "selectAll"
    	                      expression = "execution(* com.springtutorial.*.*(..))"/>
	
    	        <aop:before pointcut-ref = "selectAll" method = "beforeAdvice"/>
            	<aop:after pointcut-ref = "selectAll" method = "afterAdvice"/>
            	<aop:after-returning pointcut-ref = "selectAll"
                                 returning = "retVal" method = "afterReturningAdvice"/>
	
          	  <aop:after-throwing pointcut-ref = "selectAll"
                                throwing = "ex" method = "AfterThrowingAdvice"/>
        	</aop:aspect>
    	</aop:config>
	
    	<!-- Definition for student bean -->
    	<bean id = "student" class = "com.springtutorial.Student">
        	<property name = "name" value = "Zara" />
        	<property name = "age" value = "11"/>
    	</bean>
	
    	<!-- Definition for logging aspect -->
    	<bean id = "logging" class = "com.springtutorial.Logging"/>
	
	</beans>

We can see that,

1.	in the `<aop:aspect>`, it declares an aspect, gives this aspect an id called "log" and refers to the bean called "logging".

2.	in the `<aop:pointcut>`, it defines a pointcut called "selectAll", which matches the execution of all the methods available under the package com.springtutorial.

3.	in the `<aop:before>`, `<aop:after>`, `<aop:after-returning>` and `<aop: after-throwing>`, all of these four kinds of advice will be executed on their position on the pointcut reference called "selectAll".

The output of main method:

    Going to setup student profile.
    Name : Zara
    Student profile has been setup.
    Returning:Zara 
    Going to setup student profile.
    Age : 11
    Student profile has been setup.
    Returning:11
    Going to setup student profile.
    Exception raised
    Student profile has been setup.
    There has been an exception: java.lang.IllegalArgumentException

Remember that our AOP will execute on all the methods available in the package com.springtutorial. The logic here is like this. The container scans the XML and find that we pass a Name = Zara and Age = 11 to the Student bean. When we use getName(), the AOP will print "Going to setup student profile." before this method. And then this method executes and print "Name : Zara". Then, after this execution, AOP print "Student profile has been setup". Then, because this method has a return value, AOP also print "Returning: Zara". The later two conditions are similar.

## @AspectJ Based AOP

In addition to the XML Schema based AOP learned above, we can also use @AspectJ Based AOP. @AspectJ refers to a style of declaring aspects as regular Java classes annotated with Java 5 annotations. The @AspectJ support is enabled by including the following element inside your XML Schema-based configuration file.

    <aop:aspectj-autoproxy/>

### Declaring an aspect

Aspects classes are like any other normal bean and may have methods and fields just like any other class, except that they will be annotated with @Aspect as follows −

	package org.xyz;
	
	import org.aspectj.lang.annotation.Aspect;
	
	@Aspect
	public class AspectModule {
	}

They will be configured in XML like any other bean as follows −

	<bean id = "myAspect" class = "org.xyz.AspectModule">
	   <!-- configure properties of aspect here as normal -->
	</bean>

### Declaring a pointcut
A pointcut helps in determining the join points (ie methods) of interest to be executed with different advices. While working with @AspectJ-based configuration, pointcut declaration has two parts −

1.	A pointcut expression that determines exactly which method executions we are interested in.

2.	A pointcut signature comprising a name and any number of parameters. The actual body of the method is irrelevant and in fact should be empty.

The following example defines a pointcut named 'businessService' that will match the execution of every method available in the classes under the package com.xyz.myapp.service −

	import org.aspectj.lang.annotation.Pointcut;

	@Pointcut("execution(* com.xyz.myapp.service.*.*(..))") // expression 
	private void businessService() {}  // signature

The following example defines a pointcut named 'getname' that will match the execution of getName() method available in the Student class under the package com.tutorialspoint −

	import org.aspectj.lang.annotation.Pointcut;
	
	@Pointcut("execution(* com.tutorialspoint.Student.getName(..))") // expression
	private void getname() {} // signature

**Note that the signature here is the signature of pointcut!!!**

### Declaring advices
	
You can declare any of the five advices using @{ADVICE-NAME} annotations as given in the code snippet. This assumes that you already have defined a pointcut signature method businessService() 

    @Before("businessService()")
    public void doBeforeTask(){
       ...
    }
    
    @After("businessService()")
    public void doAfterTask(){
       ...
    }
    
    @AfterReturning(pointcut = "businessService()", returning = "retVal")
    public void doAfterReturnningTask(Object retVal) {
       // you can intercept retVal here.
       ...
    }
    
    @AfterThrowing(pointcut = "businessService()", throwing = "ex")
    public void doAfterThrowingTask(Exception ex) {
      // you can intercept thrown exception here.
      ...
    }
    
    @Around("businessService()")
    public void doAroundTask(){
       ...
    }

For the first method, it means that doBeforeTask() will execute before the pointcuts defined in "businessService()" (which is all the methods available in the classes under the package com.xyz.myapp). The others are similar.

You can define a pointcut inline for any of the advices. Following is an example to define inline pointcut for before advice −

	@Before("execution(* com.xyz.myapp.service.*.*(..))")
	public doBeforeTask(){
		...
	}

## An Example

We can define the previous Logging.java like this:

    import org.aspectj.lang.annotation.Aspect;
    import org.aspectj.lang.annotation.Pointcut;
    import org.aspectj.lang.annotation.Before;
    import org.aspectj.lang.annotation.After;
    import org.aspectj.lang.annotation.AfterThrowing;
    import org.aspectj.lang.annotation.AfterReturning;
    import org.aspectj.lang.annotation.Around;
    
    @Aspect
    public class Logging {
       @Pointcut("execution(* com.tutorialspoint.*.*(..))")
       private void selectAll(){}
       
		@Before("selectAll()")
       	public void beforeAdvice(){
      		System.out.println("Going to setup student profile.");
       	}
	
       	@After("selectAll()")
       	public void afterAdvice(){
      		System.out.println("Student profile has been setup.");
       	}
       	@AfterReturning(pointcut = "selectAll()", returning = "retVal")
       	public void afterReturningAdvice(Object retVal){
      		System.out.println("Returning:" + retVal.toString() );
       	}
       
		@AfterThrowing(pointcut = "selectAll()", throwing = "ex")
       	public void AfterThrowingAdvice(IllegalArgumentException ex){
      		System.out.println("There has been an exception: " + ex.toString());   
       	}
    }	

It seems like this method is more convenient! In the XML file, we don't need to write any <aop:blabla>, just beans!
















