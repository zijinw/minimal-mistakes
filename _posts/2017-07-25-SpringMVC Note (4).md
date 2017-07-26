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

We have learned that there is a property called "scope" in the bean. It has 5 types, as below.

1.	**singleton**. This scopes the bean definition to a single instance per Spring IoC container (default). For example, if you want Spring to return the same bean instance each time one is needed, you should declare the bean's scope attribute to be singleton.
2.	**prototype**. This scopes a single bean definition to have any number of object instances. For example, to force Spring to produce a new bean instance each time one is needed, you should declare the bean's scope attribute to be prototype.
3.	**request**. This scopes a bean definition to an HTTP request. Only valid in the context of a web-aware Spring ApplicationContext.
4.	**session**. This scopes a bean definition to an HTTP session. Only valid in the context of a web-aware Spring ApplicationContext.
5.	**global-session**. This scopes a bean definition to a global HTTP session. Only valid in the context of a web-aware Spring ApplicationContext.

### Singleton ###
From its name, we can guess its meaning. Singleton means that in the whole application, there is one and only one instance for this bean (class). Once you create the instance of that class according to configuration file, each time you want to refer to it, you will always get the same object.

    <bean id = "..." class = "..." scope = "singleton">
       <!-- collaborators and configuration for this bean go here -->
    </bean>

For example, in the previous hello world example, if we define a singleton in the Beans.xml:

    <bean id = "helloworld" class = "com.springTutorial.HelloWorld" scope = "singleton">
    </bean>

And in the main method, we write:

	public static void main(String[] args) {
      ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
      HelloWorld objA = (HelloWorld) context.getBean("helloWorld");
		
      objA.setMessage("I'm object A");
      objA.getMessage();
	
      HelloWorld objB = (HelloWorld) context.getBean("helloWorld");
      objB.getMessage();
	}

At first, we create objA using getBean("helloWorld"), and set its message. Then, we create a new objB by using the same getBean("helloWorld"). Because in the Beans.xml, we define "helloWorld" bean as a singleton, so this time this objB will exactly be the objA that we have created. So the getMessage() method of objA and objB will return the same message.

### Prototype ###

When the scope is set to prototype, the container will create a new bean instance every time a request for that specific bean is made. **As a rule, use the prototype scope for all state-full beans and the singleton scope for stateless beans.**

In the previous example, if we define the scope of "helloWorld" bean as prototype, then objA.getMessage() will return us "I'm object A", but objB.getMessage() will return null, because it is a new object, and we never set its message.

## Bean Life Cycle ##

The life cycle of a bean means the process from its instantiation  to its destruction. Here we will learn this through the **initmethod** property and **destroy-method**. The former one is a method that is to be called just after the bean instantiation, and the later one is a method that will be called just before the bean is removed from the container.

### Initialization and Destruction Methods ###
In the xml file we can tell the container what to do after the bean has been initialized, and/or before the destruction. For example, when defining a bean, we can say

    <bean id="helloWorld" class="com.springTutorial.HelloWorld" init-method="init" destroy-method="destroy">
    	<property name = "message" value = "Hello World!"/>
    </bean>

And in that **HelloWorld** class, we define a init method and a destroy method:

    public void init() {
       	System.out.println("Bean has been started");
    }
	public void destroy() {
		System.out.println("Bean will destroy now");
	}

In the main method, we write:

	public static void main(String[] args) {
    	AbstractApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");

    	HelloWorld obj = (HelloWorld) context.getBean("helloWorld");
    	obj.getMessage();
    	context.registerShutdownHook();
	}

Note that here we need to register a shutdown hook registerShutdownHook() method that is declared on the AbstractApplicationContext class. This will ensure a graceful shutdown and call the relevant destroy methods.

Run the main method, the following will return:

    Bean has been started.
    Your Message : Hello World!
    Bean will destroy now.

We can see that the init method and destroy method do their own job, at the start and end.

If we have a lot of beans that have the same init and destroy method, we don't need to write them time by time. We can just write default-init-method and default-destroy-method in the beans element, like this:

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
       	   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd"
		   default-init-method = "init"
		   default-destroy-method = "destroy">

    	<bean id = "helloWorld" class = "com.springtutorial.HelloWorld">
        	<property name = "message" value = "Hello World!"/>
    	</bean>
	</beans>

