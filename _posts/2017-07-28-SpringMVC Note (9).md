---
layout: single
title:  "SpringMVC Note (8)"
date:   2017-07-28 13:00:00
categories: SpringMVC
---

# Event Handling in Spring #

We have learned that in the Spring, the most basic and core element is **ApplicationContext**. It manages the whole life cycle of a bean. The ApplicationContext publishes certain types of events when loading the beans. Next are the standard events that Spring provides.

1.	**ContextRefreshedEvent**. This event is published when the ApplicationContext is either initialized or refreshed. This can also be raised using the refresh() method on the ConfigurableApplicationContext interface.

2.	**ContextStartedEvent**. This event is published when the ApplicationContext is started using the start() method on the ConfigurableApplicationContext interface. You can poll your database or you can restart any stopped application after receiving this event.

3.	**ContextStoppedEvent**. This event is published when the ApplicationContext is stopped using the stop() method on the ConfigurableApplicationContext interface. You can do required housekeep work after receiving this event.

4.	 **ContextClosedEvent**. This event is published when the ApplicationContext is closed using the close() method on the ConfigurableApplicationContext interface. A closed context reaches its end of life; it cannot be refreshed or restarted.

5.	**RequestHandledEvent**. This is a web-specific event telling all beans that an HTTP request has been serviced.

Spring's event handling is single-threaded so if an event is published, until and unless all the receivers get the message, the processes are blocked and the flow will not continue. Hence, care should be taken when designing your application if the event handling is to be used.

Event handling in the ApplicationContext is provided through the **ApplicationEvent** class and **ApplicationListener** interface. Hence, if a bean implements the ApplicationListener, then every time an ApplicationEvent gets published to the ApplicationContext, that bean is notified.

To listen to a context event, a bean should implement the **ApplicationListener** interface which has just one method **onApplicationEvent()**. We now learn an example to see how the events propagates and how you can put your code to do required task based on certain events.

Let's say we have a HelloWorld.java. We just want to print a message through this class. As usual, the message will be set through XML.

	package com.springtutorial;
	
	public class HelloWorld {
	    private String message;
	
	    public void setMessage(String message){
	        this.message  = message;
	    }
	    public void getMessage(){
	        System.out.println("Your Message : " + message);
	    }
	}

Now at this time we want a **ContextStartedEvent** and a **ContextStoppedEvent**, which means that we want the application to do something when the **ApplicationContext** is started and stopped. In order to do this, we write a CStartEventHandler.java:

	package com.springtutorial;
		
	import org.springframework.context.ApplicationListener;
	import org.springframework.context.event.ContextStartedEvent;
	
	public class CStartEventHandler implements ApplicationListener<ContextStartedEvent> {
	    public void onApplicationEvent(ContextStartedEvent event) {
	        System.out.println("Haha QQD, this is ContextStartedEvent");
	    }
	}

and a CStopEventHandler.java:

	package com.springtutorial;
	
	import org.springframework.context.ApplicationListener;
	import org.springframework.context.event.ContextStoppedEvent;
	
	public class CStopEventHandler implements ApplicationListener<ContextStoppedEvent> {
    	public void onApplicationEvent(ContextStoppedEvent event) {
    	    System.out.println("Haha QQD, this is ContextStoppedEventh");
    	}
	}
	
We can see that in these two classes, we implements **ApplicationListener** intreface, and override **onApplicationEvent** method in it, to define ContextStartedEvent and ContextStoppedEvent. From their definition, we know that if we use start() or stop() in ConfigurableApplicationContext interface, these two methods will perform.

So the main method is:

	package com.springtutorial;
	
	import org.springframework.context.ConfigurableApplicationContext;
	import org.springframework.context.support.ClassPathXmlApplicationContext;
	public class MainApp {
    	public static void main(String[] args) {
    	    ConfigurableApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
    	    context.start();
    	    HelloWorld te = (HelloWorld) context.getBean("helloWorld");
    	    te.getMessage();
    	    context.stop();
    	}
	}

We can see that in this mean method, we did not use the **ApplicationContext** as before. Instead, in order to use event handler, we use **ConfigurableApplicationContext**.

Finally, in the XML, we write:

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	
    	<bean id = "helloWorld" class = "com.springtutorial.HelloWorld">
    	    <property name = "message" value = "Hello World Hello QQD!" />
    	</bean>
	
    	<bean id = "cStartEventHandler" class = "com.springtutorial.CStartEventHandler"></bean>
    	<bean id = "cStopEventHandler" class = "com.springtutorial.CStopEventHandler"></bean>
	
	</beans>

When the container is created, is scans the XML and finds the last two beans. It then looks at these two classes and find that they implement **ApplicationListener** and have **onApplicationEvent()** method. Then when we call context.start() and context.stop(), it will go to find the definition of these two methods and perform them.

The final output will be:

	Haha QQD, this is ContextStartedEvent
	Your Message : Hello World Hello QQD!
	Haha QQD, this is ContextStoppedEventh

I feel that this event handling introduced here is similar to init-method and destroy-method we learnt before, but they are different. First is that event handling has other choices that beyond start and end. Second is that init-method and destroy-method means the start and end of a bean, and after destroy this bean will not restart, but in event handler we can use stop() and then it can restart again.

## Custom Event

In the last part we learned that Spring has 5 standard events, which can be used when the ApplicationContext starts, stops, refreshes, closes, and receives a HTTP request.

We can also custom our own events. Next we will learn how to do this.

First, create a event class called CustomEvent.java which extends ApplicationEvent. This class must define a default constructor which should inherit constructor from ApplicationEvent class.

	package com.springtutorial;
	
	import org.springframework.context.ApplicationEvent;
	
	public class CustomEvent extends ApplicationEvent{
    	public CustomEvent(Object source) {
    	    super(source);
    	}
    	public String toString() {
    	    return "QQD this is custom event";
    	}
	}

Here this class extends **ApplicationEvent**. Let's look at the source code of ApplicationEvent:

	package org.springframework.context;
	
	import java.util.EventObject;
	
	public abstract class ApplicationEvent extends EventObject {
    	private static final long serialVersionUID = 7099057708183571937L;
    	private final long timestamp = System.currentTimeMillis();
	
    	public ApplicationEvent(Object source) {
    	    super(source);
    	}
	
    	public final long getTimestamp() {
    	    return this.timestamp;
    	}
	}

We can find that this abstract class extends **EventObject** class, which is defined in java.util. Super() method calls the parent's constructor.

Anyway, we create a CustomEvent class, which is our event class. Now we can publish it from any class. For example, we write a **EventClassPublisher** class which implements **ApplicationEventPublisherAware**. We will also need to declare this class in XML file as a bean so that the container can identify the bean as an event publisher because it implements the ApplicationEventPublisherAware interface.

	package com.springtutorial;
	
	import org.springframework.context.ApplicationEventPublisher;
	import org.springframework.context.ApplicationEventPublisherAware;
	
	public class CustomEventPublisher implements ApplicationEventPublisherAware {
    	private ApplicationEventPublisher publisher;
		@Override
    	public void setApplicationEventPublisher (ApplicationEventPublisher publisher) {
    	    this.publisher = publisher;
    	}
    	public void publish() {
    	    CustomEvent ce = new CustomEvent(this);
    	    publisher.publishEvent(ce);
    	}
	}

So in this class we use a publisher to publish our event defined before.

Next we need an event handler which can listen to the event that is to be published.

	package com.springtutorial;
	
	import org.springframework.context.ApplicationListener;
	
	public class CustomEventHandler implements ApplicationListener<CustomEvent> {
    	@Override
    	public void onApplicationEvent(CustomEvent event) {
    	    System.out.println(event.toString());
    	}
	}

The XML should be like this:

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
    	   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    	   xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    	
    	<bean id = "customEventHandler" class = "com.springtutorial.CustomEventHandler"/>
    	<bean id = "customEventPublisher" class = "com.springtutorial.CustomEventPublisher"/>
    	
	</beans>

And finally, in the main method:

	package com.springtutorial;
	
	import org.springframework.context.ConfigurableApplicationContext;
	import org.springframework.context.support.ClassPathXmlApplicationContext;
	public class MainApp {
    	public static void main(String[] args) {
    	    ConfigurableApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
    	    CustomEventPublisher te = (CustomEventPublisher) context.getBean("customEventPublisher");
    	    te.publish();
    	}
	}

Based on my understanding, the logic is like this. When the container is created, it scans the XML file and initializes two beans. When initializing *customEventHandler*, it finds that this class implements *ApplicationListener*, and the type of event it can "listen" is "CustomEvent". And when initializing *CustomEventPublisher*, it finds that it implements *ApplicationEventPublisherAware*, so the container will treat is as a event publisher. Note that we don't have any fields in the CustomEvent.java, so in XML we don't need to configure it.

So in the main method, the container uses getBean() to initialize an instance of *CustomEventPublisher*. As long as this instance uses the **publish** method, the event handler will listen to this and print the message in the custom event.

## Summary

The similarity and difference between built-in standard event and custom event:

1.	If we want to use a built-in standard event, we just need to write a handler for it. The event itself has been defined by Spring, and we don't need an extra publisher to publish it. Instead, in the main method we can directly use *ConfigurableApplicationContext* to publish it. The start() and stop() method are used to publish the event.

2.	If we want to use a custom event, we need to write this event itself and write a custom publisher. In the publisher, define our own publish method. We also need to write a handler. Also, in the main method we cannot directly use *ConfigurableApplicationContext* to publish the event. Instead, we need to use it to create an instance of our custom publisher, and use this publisher to publish the event.

3.	Under both circumstances, in the handler, declare which event we are to use. When using standard event, declare what will happen (like what will be printed) when we call this event. When using custom event, we can call the method that we have defined in event class.








If we want to use a built-in standard event, we just need to write a handler for it, where we need to declare which standard event we are to use, and declare what will happen (like what will be printed) when we call this event. The start() and stop() method is the publish method of that event, and are called directly 