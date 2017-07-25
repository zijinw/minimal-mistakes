---
layout: single
title:  "SpringMVC Note (3)"
date:   2017-07-25 20:00:00
categories: SpringMVC
---

# What is Spring IoC Containers? #

Container is a new definition to me, maybe because I have never done any enterprise Java project before. In this note we will have a look at it and understand its role in Spring.

Container is the core of Spring framework. It can create objects and manage their whole life cycle. Actually the ApplicationContext we created in the last note is a container, and we have seen that it works as the configuration file tells it. The containers in Spring combine Java POJO classes and configuration metadata together to create an executable application.

There are two types of containers in Spring:

1. Spring BeanFactory Container;
2. 	Spring ApplicationContext Container.

## Spring BeanFactory Container ##

The BeanFactory Container is provided by *org.springframework.beans.factory.BeanFactory* interface. It is an old container, but still available for the sake of compatibility. The most commonly used implementation of this interface is **XmlBeanFactory** class. This container reads the configuration metadata from an XML file and uses it to create a fully configured system or application.

For example, as in the previous note, when we have a Beans.xml configuration file, in the main method we can write:

	public static void main(String[] args) { 
      XmlBeanFactory factory = new XmlBeanFactory (new ClassPathResource("Beans.xml")); 
      HelloWorld obj = (HelloWorld) factory.getBean("helloWorld");    
      obj.getMessage();    
	}
First a XmlBeanFactory instance is created through ClassPathResource, which reads the Beans.xml. XmlBeanFactory creates and initializes all the beans (objects) in the xml file. Then, it gets the required "helloWorld" bean and be used to create a new HelloWorld instance.

Due to the fact that BeanFactory is old and is only preferred where the resources are limited like mobile devices or applet-based applications, one should use ApplicationContext if he can. Of course, ApplicationContext has all the functionality of BeanFactory.

## Spring ApplicationContext Container ##
This container is defined by *org.springframework.context.ApplicationContext* interface. The three most commonly used implementations are:

1. **FileSystemXmlApplicationContext**: This container loads the definitions of the beans from an XML file. Here you need to provide the full path of the XML bean configuration file to the constructor.
2. **ClassPathXmlApplicationContext**: This container loads the definitions of the beans from an XML file. Here you do not need to provide the full path of the XML file but you need to set CLASSPATH properly because this container will look like bean configuration XML file in CLASSPATH.
3. **WebXmlApplicationContext**: This container loads the XML file with definitions of all beans from within a web application.

The implementation in the previous note is ClassPathXmlApplicationContext. If one wants to use FileSystemXmlApplicationContext, he just needs to use an absolute path.

From my understanding till now, container is a tool that reads the configuration file, then creates and initializes the objects in this file according to the property we set. For example, in the previous Hello World example, we never set the message through setMessage() manually. We just set the property in Beans.xml and used container to read it.