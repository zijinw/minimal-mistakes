---
layout: single
title:  "SpringMVC Note (4)"
date:   2017-07-25 18:40:00
categories: SpringMVC
---

# Always hear about “bean”, but what is it? #

We have met with bean for several times before. A bean is an object that is instantiated, assembled, and managed by a Spring IoC container. These beans are created with the **configuration metadata** that you supply to the container, like in the Beans.xml file that we write before. The configuration metadata is the information that container needs to know, like how to create a bean, bean's life cycle, and bean's dependencies. The following are a set of properties that define a bean.

1. **Class**. This attribute is mandatory and specifies the bean class to be used to create the bean.
2. **Name**. This attribute specifies the bean identifier uniquely. In XMLbased configuration metadata, you use the id and/or name attributes to specify the bean identifier(s).
3. **Scope**. This attribute specifies the scope of the objects created from a particular bean definition. It will be learned later.
4. **Cnstructor-arg**. This is used to inject the dependencies.
5. **Properties**. This is used to inject the dependencies.
6. **Autowiring mode**. This is used to inject the dependencies.
7. **Lazy-initialization mode**. A lazy-initialized bean tells the IoC container to create a bean instance when it is first requested, rather than at the startup.
8. **Initialization method**. A callback to be called just after all necessary properties on the bean have been set by the container.
9. **Destruction method**. A callback to be used when the container containing the bean is destroyed. It will be learned later.

In the Spring, the container is decoupled from the way that the configuration is written in. There are three important methods to provide the configuration metadata:

- XML based configuration file (we have seen before)

- Annotation-based configuration

- Java-based configuration

Next is another sample for XML based configuration file.

	<?xml version = "1.0" encoding = "UTF-8"?>

	<beans xmlns = "http://www.springframework.org/schema/beans"
	xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation = "http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
	
	<!-- A simple bean definition -->
	<bean id = "..." class = "...">
      <!-- collaborators and configuration for this bean go here -->
	</bean>
	
	<!-- A bean definition with lazy init set on -->
	<bean id = "..." class = "..." lazy-init = "true">
      <!-- collaborators and configuration for this bean go here -->
	</bean>
	
	<!-- A bean definition with initialization method -->
	<bean id = "..." class = "..." init-method = "...">
      <!-- collaborators and configuration for this bean go here -->
	</bean>
	
	<!-- A bean definition with destruction method -->
	<bean id = "..." class = "..." destroy-method = "...">
      <!-- collaborators and configuration for this bean go here -->
	</bean>
	
	</beans>

We can see that these properties are all set in the first <>, just next to the "bean" keyword. So according to the knowledge that has been learned till now, I guess that

    <property name = "message" value = "Hello World!"/>

is not the "Properties" element above, because this line of code is written below to the bean element. (Just a kind of guess. Maybe wrong, and will be corrected later if I am wrong).

## Bean Scopes ##

