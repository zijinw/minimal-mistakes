---
layout: single
title:  "SpringMVC Note (6)"
date:   2017-07-27 15:00:00
categories: SpringMVC
---

# Injecting inner beans, collections, empty and null values

## Inner beans

Inner beans in Spring is similar to inner classes in java. Next is an example that help us to understand it.

Just like the TextEditor example we have learned before. The TextEditor.java is:

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

And the SpellChecker.java is:

	package com.springtutorial;
	
	public class SpellChecker {
	    public SpellChecker() {
	        System.out.println("Inside SpellChecker constructor." );
	    }
	    public void checkSpelling() {
	        System.out.println("Inside checkSpelling." );
	    }
	}

The Main method is:

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

Next is the Bean.xml with a inner bean.

	<?xml version = "1.0" encoding = "UTF-8"?>
	
    <beans xmlns = "http://www.springframework.org/schema/beans"
    	   xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation = "http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
	  
		   <bean id = "textEditor" class = "com.tutorialspoint.TextEditor">
      			<property name = "spellChecker">
           			<bean id = "spellChecker" class = "com.tutorialspoint.SpellChecker"/>
      			</property>
       	   </bean>
	</beans>

Here we use a inner bean to pass the dependency. Explicitly, under the <property>, we directly write another <bean>, which means that this <property> will use this <bean> as a ref. Compare this with the one we learned last time, which is a normal one:

    <bean id = "textEditor" class = "com.springtutorial.TextEditor">
        <property name = "spellChecker" ref = "spellChecker"/>
    </bean>

    <bean id = "spellChecker" class = "com.springtutorial.SpellChecker"></bean>

Believe that you can see the difference and the relationship.

## Injecting collections

Previously we have seen if we want to inject a primitive type value or a reference to some bean, we use 
    
    <property name = "blabla" value = "123"/>
    <property name = "blablabla" ref = "anotherBean"/>

Now we learn how to pass Java Collection types, like Set, Map, List and Properties, to it. Spring offers four types of configuration types:

1.	**<list>**. This helps in injecting a list of values, allowing duplicates.

2.	**<set>**. This helps in injecting a set of values but without any duplicates.

3. **<map>**. This can be used to inject a collection of name-value pairs where name and value can be of any type.

4.	**<props>**. This can be used to inject a collection of name-value pairs where the name and value are both Strings.

Next we will learn an example.

In IDE write a JavaCollection.java:

	package com.springtutorial;
	
	import java.util.*;
	
	public class JavaCollection {
	    List addressList;
	    Set  addressSet;
	    Map  addressMap;
	    Properties addressProp;
	
	    // a setter method to set List
	    public void setAddressList(List addressList) {
	        this.addressList = addressList;
	    }
	
	    // prints and returns all the elements of the list.
	    public List getAddressList() {
	        System.out.println("List Elements :"  + addressList);
	        return addressList;
	    }
	
	    // a setter method to set Set
	    public void setAddressSet(Set addressSet) {
	        this.addressSet = addressSet;
	    }
	
	    // prints and returns all the elements of the Set.
	    public Set getAddressSet() {
	        System.out.println("Set Elements :"  + addressSet);
	        return addressSet;
	    }
	
	    // a setter method to set Map
	    public void setAddressMap(Map addressMap) {
	        this.addressMap = addressMap;
	    }
	
	    // prints and returns all the elements of the Map.
	    public Map getAddressMap() {
	        System.out.println("Map Elements :"  + addressMap);
	        return addressMap;
	    }
	
	    // a setter method to set Property
	    public void setAddressProp(Properties addressProp) {
	        this.addressProp = addressProp;
	    }
	
		    // prints and returns all the elements of the Property.
	    public Properties getAddressProp() {
	        System.out.println("Property Elements :"  + addressProp);
	        return addressProp;
	    }
	}

Then, in the main method, write:

	package com.springtutorial;
	
	import org.springframework.context.ApplicationContext;
	import org.springframework.context.support.ClassPathXmlApplicationContext;
	public class MainApp {
    	public static void main(String[] args) {
    	    ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
    	    JavaCollection jc=(JavaCollection)context.getBean("javaCollection");
	
        	jc.getAddressList();
        	jc.getAddressSet();
        	jc.getAddressMap();
        	jc.getAddressProp();
    	}
	}

We can see that in the main method the container read the "javaCollection" bean to initialize a JavaCollection object, then use its four methods. So what's in that Beans.xml?

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

		<bean id = "javaCollection" class = "com.springtutorial.JavaCollection">

        <!-- results in a setAddressList(java.util.List) call -->
        	<property name = "addressList">
        	    <list>
        	        <value>INDIA</value>
        	        <value>Pakistan</value>
        	        <value>USA</value>
        	        <value>USA</value>
        	    </list>
        	</property>
	
        	<!-- results in a setAddressSet(java.util.Set) call -->
        	<property name = "addressSet">
        	    <set>
        	        <value>INDIA</value>
        	        <value>Pakistan</value>
        	        <value>USA</value>
        	        <value>USA</value>
        	    </set>
        	</property>
	
        	<!-- results in a setAddressMap(java.util.Map) call -->
        	<property name = "addressMap">
        	    <map>
        	        <entry key = "1" value = "INDIA"/>
        	        <entry key = "2" value = "Pakistan"/>
        	        <entry key = "3" value = "USA"/>
        	        <entry key = "4" value = "USA"/>
        	    </map>
        	</property>
	
        	<!-- results in a setAddressProp(java.util.Properties) call 	-->
        	<property name = "addressProp">
        	    <props>
        	        <prop key = "one">INDIA</prop>
        	        <prop key = "one">INDIA</prop>
        	        <prop key = "two">Pakistan</prop>
        	        <prop key = "three">USA</prop>
        	        <prop key = "four">USA</prop>
        	    </props>
        	</property>
    </bean>

We should notice that in the JavaCollection.java, there is no constructor, so Java will use the default with no parameters. And the <property> here actually is using the setter method to pass the values. For example, 

	<property name = "addressMap">
      	   <map>
     	       <entry key = "1" value = "INDIA"/>
      	       <entry key = "2" value = "Pakistan"/>
      	       <entry key = "3" value = "USA"/>
               <entry key = "4" value = "USA"/>
      	   </map>
     </property>

In this part, the <property> mentions that this injection is for the field called "addressMap". And then it passes a map with four entries to it.

### Injecting Bean References

The next Beans.xml shows how to inject bean references together with injecting values.

    <?xml version = "1.0" encoding = "UTF-8"?>
    
    <beans xmlns = "http://www.springframework.org/schema/beans"
       xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation = "http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

	<bean id = "bla" class = "bla">

      <!-- Passing bean reference  for java.util.List -->
      <property name = "addressList">
         <list>
            <ref bean = "address1"/>
            <ref bean = "address2"/>
            <value>Pakistan</value>
         </list>
      </property>
      
      <!-- Passing bean reference  for java.util.Map -->
      <property name = "addressMap">
         <map>
            <entry key = "one" value = "INDIA"/>
            <entry key = "two" value-ref = "address1"/>
            <entry key = "three" value-ref = "address2"/>
         </map>
      </property>
	</bean>
	</beans>

In this Beans.xml, we can see that some of the elements in the list and the map are references, which point to other beans. If we want to use this way, we need to define our setter method so that they can receive references.

### Injecting null and empty

If we want to inject null or empty value to some field, we can write:

    <bean id = "..." class = "..." >
		<property name = "aa"><null/></property>
	</bean>
	
	<bean id = "..." class = "...">
		<property name = "bb" value = "" />
	</bean>