---
layout: single
title:  "SpringMVC Note (7)"
date:   2017-07-27 16:46:00
categories: SpringMVC
---

# Beans Auto-Wiring: they set their own properties automatically! #
We have learned how to use <bean> elements to declare beans and inject bean using <property> or <constructor-arg> elements. Actually Spring can auto-wire the relationships between collaborating beans without using <property> and <constructor-arg> elements.

There are following ways to wire the beans.

## Autowiring 'byName'

This mode specifies autowiring by property name. Spring container looks at the beans on which auto-wire attribute is set to byName in the XML configuration file. It then tries to match and wire its properties with the beans defined by the same names in the configuration file. If matches are found, it will inject those beans. Otherwise, bean(s) will not be wired. We can learn the following example.

Still remember the TextEditor and SpellChecker example? Its XML file looks like this:

	<?xml version = "1.0" encoding = "UTF-8"?>
	
	<beans xmlns = "http://www.springframework.org/schema/beans"
	xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation = "http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
	
		<!-- Definition for textEditor bean -->
		<bean id = "textEditor" class = "com.springtutorial.TextEditor">
      		<property name = "spellChecker" ref = "spellChecker" />
		</bean>
	
		<!-- Definition for spellChecker bean -->
		<bean id = "spellChecker" class = "com.springtutorial.SpellChecker"></bean>
	
	</beans>

Here in the <property> of textEditor, we tell the container that it should refer to spellChecker bean. Now, we can use "byName" autowire.

	<?xml version = "1.0" encoding = "UTF-8"?>
	
	<beans xmlns = "http://www.springframework.org/schema/beans"
	xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation = "http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
	
		<!-- Definition for textEditor bean -->
		<bean id = "textEditor" class = "com.springtutorial.TextEditor" autowire = "byName">
		</bean>

		<!-- Definition for spellChecker bean -->
		<bean id = "spellChecker" class = "com.springtutorial.SpellChecker"></bean>
	
	</beans>

We can see that here we define the autowire = "byName" in the <bean> of textEditor. We have known that this class has a property called spellChecker(that is, it has a setSpellChecker(...)method), so Spring automatically looks for a bean definition named spellChecker, and uses it to set the property. So although we don't write a <property name = “spellChecker"...../> here, Spring has already gain spellChecker and pass it to the setter method.

If we have other properties in textEditor, we still can use <property> to set them.

## Autowiring 'byType'

This mode specifies autowiring by property type. Spring container looks at the beans on which autowire attribute is set to byType in the XML configuration file. It then tries to match and wire a property if its type matches with exactly one of the beans name in the configuration file. If matches are found, it will inject those beans. Otherwise, bean(s) will not be wired.

	<?xml version = "1.0" encoding = "UTF-8"?>
	
	<beans xmlns = "http://www.springframework.org/schema/beans"
	xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation = "http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
	
		<!-- Definition for textEditor bean -->
		<bean id = "textEditor" class = "com.springtutorial.TextEditor" autowire = "byType">
		</bean>

		<!-- Definition for spellChecker bean -->
		<bean id = "spellChecker" class = "com.springtutorial.SpellChecker"></bean>
	
	</beans>

We know that in the TextEditor.java there is a spellChecker field, whose property (class) is SpellChecker. So in the XML above, after we write autowire = "byType", the container will try to find a bean whose class is also SpellChecker, and use it to set the property. So it finds the com.springtutorial.SpellChecker class.

Again, if you have other properties that don't have corresponding beans, you can manually set them with <property>.

## Autowiring by Constructor

This mode is very similar to byType, but it applies to constructor arguments. Spring container looks at the beans on which autowire attribute is set toconstructor in the XML configuration file. It then tries to match and wire its constructor's argument with exactly one of the beans name in the configuration file. If matches are found, it will inject those beans. Otherwise, bean(s) will not be wired.

Assume the TextEditor doesn't use the default constructor. Instead, it has such a constructor:

	public TextEditor( SpellChecker spellChecker, String name ) {
      this.spellChecker = spellChecker;
      this.name = name;
	}

We can always use the traditional manual way to set the property in the XML:

	<bean id = "textEditor" class = "com.tutorialspoint.TextEditor">
      <constructor-arg  ref = "spellChecker" />
      <constructor-arg  value = "Generic Text Editor"/>
	</bean>

But we can also use autowire by constructor:

	<?xml version = "1.0" encoding = "UTF-8"?>
	
    <beans xmlns = "http://www.springframework.org/schema/beans"
       xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation = "http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

       <bean id = "textEditor" class = "com.springtutorial.TextEditor" autowire = "constructor">
      		<constructor-arg value = "Generic Text Editor"/>
       </bean>
    
       <bean id = "SpellChecker" class = "com.springtutorial.SpellChecker"></bean>
    
    </beans>

Here container knows that TextEditor.java needs a SpellChecker object and a String to construct it, but we only manually pass a string. Under autowire = constructor, the container tries to find a SpellChecker bean in the Beans.xml and pass it as the constructor argument of TextEditor.

## Autodetect

Spring first tries to wire using autowire by constructor, if it does not work, Spring tries to autowire by byType.

## Limitations 
There are some limitations on auto-wiring.

1.	Overriding possibility. If you specify a <property> or a <constructor-arg>, it will always override auto-wiring.

2.	Primitive data types. You cannot auto-wire primitive types, strings and classes.

3. Confusing nature. Auto-wiring is not very explicit and sometimes may confuse users.
