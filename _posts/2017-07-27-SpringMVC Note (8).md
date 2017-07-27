---
layout: single
title:  "SpringMVC Note (8)"
date:   2017-07-27 18:18:00
categories: SpringMVC
---
# Annotation-based configuration

Instead of using XML to perform injection, we can also use annotations to express dependency injection. Annotation injection is performed before XML injection. Thus, the latter configuration will override the former for properties wired through both approaches.

In order to turn annotation injection on (by default it is off), we should mention this in the XML:
    
    <?xml version = "1.0" encoding = "UTF-8"?>
    
    <beans xmlns = "http://www.springframework.org/schema/beans"
       xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context = "http://www.springframework.org/schema/context"
       xsi:schemaLocation = "http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context-3.0.xsd">
    
       <context:annotation-config/>
    
    </beans>

After this, we can use annotation injection to wire the classes together. Next we will learn some important annotations.

## @Required

The @Required annotation applies to bean property setter methods and it indicates that the affected bean property must be populated in XML configuration file at configuration time. Otherwise, the container throws a BeanInitializationException exception.

For example, for a student.java:

	import org.springframework.beans.factory.annotation.Required;
	
	public class Student {
		private Integer age;
		private String name;
	
		@Required
		public void setAge(Integer age) {
      		this.age = age;
		}
		public Integer getAge() {
			return age;
		}
	
		@Required
		public void setName(String name) {
			this.name = name;
		}
		public String getName() {
			return name;
		}
	}

We put @Required before the two setter methods, which means that age and name must be injected in XML at configuration, otherwise there will be exception.

In the Beans.xml, we write:

	<context:annotation-config/>

	<!-- Definition for student bean -->
	<bean id = "student" class = "com.springtutorial.Student">
      <property name = "name" value = "Zara" />

      <!-- try without passing age and check the result -->
      <!-- property name = "age"  value = "11"-->
	
	</bean>

We can see that here we just specify the "name" to be "Zara", but don't give the age setter anything. If we run the application now, it will throw BeanInitializationException.

## @Autowired

This annotation helps us to control where and how autowiring should be accomplished.

### @Autowired on Setter Methods

You can use @Autowired annotation on setter methods to get rid of the <property> element in XML configuration file. When Spring finds an @Autowired annotation used with setter methods, it tries to perform byType autowiring on the method.

Back to the TextEditor example. If in this class we write:

	@Autowired
	public void setSpellChecker( SpellChecker spellChecker ){
		this.spellChecker = spellChecker;
	}

And in the XML file we write:

	<context:annotation-config/>

	<bean id = "textEditor" class = "com.springtutorial.TextEditor"></bean>

	<bean id = "spellChecker" class = "com.springtutorial.SpellChecker"></bean>

Note that we did not give textEditor bean a autowire = "byType". But because we add a @Autowired before its setter method, it still perform a byType autowire.

### @Autowired on Properties

You can use @Autowired annotation on properties to get rid of the setter methods. When you pass the values of autowired properties using <property>, Spring will automatically assign those properties with the passed values or references.

    public class TextEditor {
       @Autowired
       private SpellChecker spellChecker;
    
       public TextEditor() {
      		System.out.println("Inside TextEditor constructor." );
       }
       public SpellChecker getSpellChecker( ){
      		return spellChecker;
       }
       public void spellCheck(){
      		spellChecker.checkSpelling();
       }
    }

The field spellChecker is marked as @Autowired, so Spring will automatically use a unseen setter method to set spellChecker for TextEditor.

In the XML: 

	<bean id = "textEditor" class = "com.springtutorial.TextEditor"></bean>

	<bean id = "spellChecker" class = "com.springtutorial.SpellChecker"></bean>

Again, we don't need to specify anything.

### @Autowired on Constructors

You can apply @Autowired to constructors as well. A constructor @Autowired annotation indicates that the constructor should be autowired when creating the bean, even if no <constructor-arg> elements are used while configuring the bean in XML file.

In the TextEditor.java, write:

     @Autowired
     public TextEditor(SpellChecker spellChecker){
      	System.out.println("Inside TextEditor constructor." );
      	this.spellChecker = spellChecker;
     }

And in the XML, write:

     <bean id = "textEditor" class = "com.springtutorial.TextEditor"></bean>
    
     <bean id = "spellChecker" class = "com.springtutorial.SpellChecker"></bean>

Note that here we don't give constructor-arg to textEditor bean. But because we add @Autowired to its constructor, the constructor will automatically try to find the parameter that is needed to construct an instance.

### @Autowired with (required=false) option

By default, the @Autowired annotation implies the dependency is required similar to @Required annotation, however, you can turn off the default behavior by using the (required=false) option with @Autowired.

The following example will work even if you do not pass any value for age property but still it will demand for the name property.

	import org.springframework.beans.factory.annotation.Autowired;
	
	public class Student {
		private Integer age;
		private String name;
	
		@Autowired(required=false)
		public void setAge(Integer age) {
			this.age = age;
		}
		public Integer getAge() {
      		return age;
		}
	
		@Autowired
		public void setName(String name) {
			this.name = name;
		}
		public String getName() {
			return name;
		}
	}

## @Qualifier

There may be a situation when you create more than one bean of the same type and want to wire only one of them with a property. In such cases, you can use the @Qualifier annotation along with @Autowired to remove the confusion by specifying which exact bean will be wired.

Let's say we have a Student.java:

	public class Student {
		private Integer age;
		private String name;
		
		public void setAge(Integer age) {
      		this.age = age;
		}
		public Integer getAge() {
			return age;
		}
		public void setName(String name) {
			this.name = name;
		}
		public String getName() {
			return name;
		}
	}

And a Profile.java:

	import org.springframework.beans.factory.annotation.Autowired;
	import org.springframework.beans.factory.annotation.Qualifier;
	
	public class Profile {
    	@Autowired
    	@Qualifier("student1")
    	private Student student;
	
    	public Profile(){
    	    System.out.println("Inside Profile constructor." );
    	}
    	public void printAge() {
    	    System.out.println("Age : " + student.getAge() );
    	}
    	public void printName() {
    	    System.out.println("Name : " + student.getName() );
    	}
	}

So Profile.java has a field student, and its methods are print this student's age and name.

In the XML file:

    <context:annotation-config/>

    <!-- Definition for profile bean -->
    <bean id = "profile" class = "com.tutorialspoint.Profile"></bean>

    <!-- Definition for student1 bean -->
    <bean id = "student1" class = "com.springtutorial.Student">
        <property name = "name" value = "Zara" />
        <property name = "age" value = "11"/>
    </bean>

    <!-- Definition for student2 bean -->
    <bean id = "student2" class = "com.springtutorial.Student">
        <property name = "name" value = "Nuha" />
        <property name = "age" value = "2"/>
    </bean>

We can see that for student class, we create two beans with different name and age, so which one will be used by @Autowired to be set as a field in the Profile? Because we write @Qualifier("student1"), the student1 will be used.

## Others

There are other annotations like JSR-250 annotations, but will not be talked here now. We can usually find alternatives for these annotations.

There is also a type of configuration called Java based configuration, but since we have learned a lot about configuration, this one can also be alternated by the ones that we have known.


















