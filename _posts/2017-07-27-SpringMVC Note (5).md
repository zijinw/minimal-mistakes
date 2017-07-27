---
layout: single
title:  "SpringMVC Note (5)"
date:   2017-07-27 10:20:00
categories: SpringMVC
---

# Basic knowledge about dependency injection #

As the first note said, the core of Spring is **Dependency Injection (DI)**. This is an very important concept. Assuming that you are building a very complex Java application, you must want different classes to be as independent as they can, because by doing so you can increase your ability to reuse these classes. You can also perform tests on some of these classes while have no impact on the other classes.

There are mainly two kinds of DI.

## Constructor-based Dependency Injection ##

Constructor-based Dependency Injection is accomplished when the container invokes a class constructor with a number of arguments, each representing a dependency on the other class. Next we will learn a example which using such a kind of DI.

In the Spring project, create three classes:
1.	TextEditor.java
2.	SpellChecker.java
3.	MainApp.java

![](https://raw.githubusercontent.com/zijinw/BlogPic/master/pictures/spring5-1.png)

In the TextEditor.java:

	package com.springtutorial;
	
	public class TextEditor {
    	private SpellChecker spellChecker;
		
    	public TextEditor(SpellChecker spellChecker) {
    	    System.out.println("Inside TextEditor constructor." );
    	    this.spellChecker = spellChecker;
    	}
    	public void spellCheck() {
    	    spellChecker.checkSpelling();
    	}
	}

We can see that the TextEditor class has a field called spellChecker which is a SpellChecker object. The constructor of TextEditor needs us to pass a spellChecker to it.

In the SpellChecker.java:

	package com.springtutorial;
	
	public class SpellChecker {
    	public SpellChecker(){
    	    System.out.println("Inside SpellChecker constructor." );
    	}
    	public void checkSpelling() {
    	    System.out.println("Inside checkSpelling." );
    	}
	}

In the MainApp.java:

	package com.springtutorial;
	
	import org.springframework.context.ApplicationContext;
	import org.springframework.context.support.ClassPathXmlApplicationContext;
	public class MainApp {
    	public static void main(String[] args) {
    	    ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
    	    TextEditor obj = (TextEditor) context.getBean("textEditor");
    	    obj.spellCheck();
    	}
	}

Here as before, we use a container to read the Beans.xml, and use getBean() to create a instance of TextEditor.

In the Beans.xml:

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	
	
    	<bean id = "textEditor" class = "com.springtutorial.TextEditor">
    	    <constructor-arg ref = "spellChecker"/>
    	</bean>
	
    	<bean id = "spellChecker" class = "com.springtutorial.SpellChecker"></bean>
	</beans>

Here we did not give the field "spellChecker" in TextEditor any value to construct the TextEditor. Instead, we give a reference of its constructor argument, which points to spellChecker.

If we run the MainApp, the output is:

    Inside SpellChecker constructor.
    Inside TextEditor constructor.
    Inside checkSpelling.

Through the order of these three messages, we can know the order of execution inside this application. First, Java use the constructor of SpellChecker to create an instance of SpellChecker. Then, pass it to the constructor of TextEditor and create an instance of TextEditor. Finally, use the spellCheck() method of TextEditor instance.

### Constructor arguments resolution
When the constructor has more than 1 arguments, we need to notice that the order in which the constructor arguments are defined in a bean definition is the order in which those arguments are supplied to the appropriate constructor.

For example, if we have a class and a constructor:

	package x.y;
    public class Foo {
    	public Foo(Bar bar, Baz baz) {
    	// ...
    	}
    }

Then in the beans we need to follow this order: first Bar then Baz:

	<beans>
    	<bean id = "foo" class = "x.y.Foo">
      		<constructor-arg ref = "bar"/>
      		<constructor-arg ref = "baz"/>
       	</bean>
       	<bean id = "bar" class = "x.y.Bar"/>
       	<bean id = "baz" class = "x.y.Baz"/>
    </beans>

(Note: the container will first construct Bar and Baz through their constructors, and pass their instances to construct the instance of Foo. Also, it looks like the constructor of Bar and Baz don't need any parameters.)

Another example. If the constructor of Foo needs two parameters: **int** year and **String** name, we can write the bean like below. Here we can declare the type of parameters.

    <beans>
    
    	<bean id = "exampleBean" class = "examples.ExampleBean">
    		<constructor-arg type = "int" value = "2001"/>
    		<constructor-arg type = "java.lang.String" value = "Zara"/>
    	</bean>
    
    </beans>

Also, a more safe way to write this is using **index** of parameters instead of their type. Container can automatically read the type of parameter on that index.

    <beans>
    
    	<bean id = "exampleBean" class = "examples.ExampleBean">
    		<constructor-arg index = "0" value = "2001"/>
    		<constructor-arg index = "1" value = "Zara"/>
    	</bean>
    
    </beans>

## Setter-based Dependency Injection ##

Compared to constructor-based DI, setter-based DI first invokes a no-argument constructor or no-argument static factory method to instantiate your bean, and later calls setter methods on the beans. In the next example, the TextEditor class is dependency-injected only by pure setter-based injection.

	package com.springtutorial;
	
	public class TextEditor {
    	private SpellChecker spellChecker;
	
    	// a setter method to inject the dependency
    	public void setSpellChecker(SpellChecker spellChecker) {
    	    System.out.println("Inside setSpellChecker.");
    	    this.spellChecker = spellChecker;
    	}
    	// a getter method
    	public SpellChecker getSpellChecker() {
    	    return this.spellChecker;
    	}
    	
    	public void spellCheck() {
    	    spellChecker.checkSpelling();
    	}
	}

Note that here we don't define a constructor in this class, so it will use the default one (no argument).

The SpellChecker class is as before.

	package com.springtutorial;
	
	public class SpellChecker {
	    public SpellChecker() {
	        System.out.println("Inside SpellChecker constructor." );
	    }
	    public void checkSpelling() {
	        System.out.println("Inside checkSpelling." );
	    }
	}

The main method is also as before.

	package com.springtutorial;
	
	import org.springframework.context.ApplicationContext;
	import org.springframework.context.support.ClassPathXmlApplicationContext;
	public class MainApp {
    	public static void main(String[] args) {
    	    ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
    	    TextEditor obj = (TextEditor) context.getBean("textEditor");
    	    obj.spellCheck();
    	}
	}

In the Beans.xml, we write:

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	
    	<bean id = "textEditor" class = "com.springtutorial.TextEditor">
    	    <property name = "spellChecker" ref = "spellChecker"/>
    	</bean>

    	<bean id = "spellChecker" class = "com.springtutorial.SpellChecker">
		</bean>
	</beans>

The only difference between this .xml and constructor-based one is that here we use <property> to give a **ref** (not **value**!) to the spellChecker field, while in constructor-based one we use <constructor-arg>.

### A convenient way if you have a lot of setter methods ###

When we have a lot of setter methods, we can use **p-namespace** to simplify our .xml file.

For example, when we have such a .xml file:

	<?xml version = "1.0" encoding = "UTF-8"?>    
    	
    <beans xmlns = "http://www.springframework.org/schema/beans"
      xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation = "http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

    	<bean id = "john-classic" class = "com.example.Person">
      		<property name = "name" value = "John Doe"/>
      		<property name = "spouse" ref = "jane"/>
       </bean>
	
       <bean name = "jane" class = "com.example.Person">
      		<property name = "name" value = "John Doe"/>
       </bean>
    
    </beans>

We can find that if we have more classes and more setter methods, we need to write a lot of <property>. Instead, we can p-namespace.

    <?xml version = "1.0" encoding = "UTF-8"?>
	
    <beans xmlns = "http://www.springframework.org/schema/beans"
       xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p = "http://www.springframework.org/schema/p"
       xsi:schemaLocation = "http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
    
       <bean id = "john-classic" class = "com.example.Person"
      		p:name = "John Doe"
      		p:spouse-ref = "jane"/>
       </bean>
    
       <bean name =" jane" class = "com.example.Person"
      		p:name = "John Doe"/>
       </bean>
	
    </beans>
    
First it adds a  "xmlns:p = "http://www.springframework.org/schema/p" in the <beans>, then we can use the p-namespace. Comparing this with the previous .xml, we can find that we don't need to write separate <property> below <bean>. Instead, we just write p:NameOfParam in the <bean>. For example, <property name = "name" value = "John Doe"> becomes p:name = "John Doe". 

Also, it is obvious that if we are assigning a value to this parameter, we use p:name; but if we are assigning a reference, we use p:name-ref to tell the container that this is a reference.

## Rule of thumb ##
Although we can mix both methods, it is a good rule of thumb to use constructor arguments for mandatory dependencies and setters for optional dependencies.