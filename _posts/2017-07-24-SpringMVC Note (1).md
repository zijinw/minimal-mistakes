---
layout: single
title:  "SpringMVC Note (1)"
date:   2017-07-24 18:00:00
categories: SpringMVC
---

# Overview

<p>This series of articles are my personal notes on learning Java SpringMVC with Maven. Most of these contents comes from <a href="https://spring.io/guides">Spring official guides</a>, <a href="https://en.wikipedia.org/wiki/Main_Page">Wikipedia</a> and <a href="https://www.tutorialspoint.com/spring/index.htm">TutorialsPoint</a>. I will not use any of these contents commercially, and the copyright belongs to their original authors. It is said that Spring is the most popular development framework used in enterprise Java, and I also come across it during my internship now. This is the main motivition that I started to learn it. So let's start.</p>

<p>The basic technology that Spring is defined with is <b>Dependency Injection</b>. <b>Dependency</b> refers to an object that can be used (like a service), and <b>injection</b> can be understood as "parameter passing". It passes the dependency to a dependent object (like a client) that would use this service. Dependency Injection is an example of <b>Inversion of Control</b>, and as the core of Spring, it will be introduced later.</p>

<p>Another key component of Spring is <b>Aspect Oriented Programming (AOP)</b>. We are very familiar with Object Oriented Programming (OOP), and AOP is another programming paradigm which increases modularity by allowing the separation of <b>cross-cutting concerns</b>. Cross-cutting concerns are concerns of a program that affect other concerns, and we can also call them "aspects". They are not the central business logic of our program, and we add these behavior without changing the code itself. For example, a logging of the history of changes in the database is a cross-cutting concern. Another example is security. AOP helps us decouple cross-cutting concerns from the objects that they affect. Spring AOP will be learnt in the future again.</p>

<h1>Architecture</h1>
There are quite a number of modules in Spring framework, and one can choose to use some of them accroding to his own needs in the project.

<b>I bold the modules which I have met in real work.</b>

<h3>Core Container</h3>
<ul>
	<li><b>Core</b>. The Core module provides the fundamental parts of the framework, including the IoC and Dependency Injection features.</li>
	<li><b>Bean</b>. The Bean module provides BeanFactory, which is a sophisticated implementation of the factory pattern.</li>
	<li><b>Context</b>. The Context module builds on the solid base provided by the Core and Beans modules and it is a medium to access any objects defined and configured. The ApplicationContext interface is the focal point of the Context module.</li>
	<li>SpEL. The SpEL module provides a powerful expression language for querying and manipulating an object graph at runtime.</li>
</ul>

<h3>Data Access/Integration</h3>
<ul>
	<li><b>JDBC(Java Database Connectivity)</b>. The JDBC module provides a JDBC-abstraction layer that removes the need for tedious JDBC related coding.</li>
	<li>ORM. The ORM module provides integration layers for popular object-relational mapping APIs, including JPA, JDO, Hibernate, and iBatis.</li>
	<li>OXM. The OXM module provides an abstraction layer that supports Object/XML mapping implementations for JAXB, Castor, XMLBeans, JiBX and XStream.</li>
	<li>JMS. The Java Messaging Service JMS module contains features for producing and consuming messages.</li>
	<li>Transaction. The Transaction module supports programmatic and declarative transaction management for classes that implement special interfaces and for all your POJOs.</li>
</ul>

<h3>Web</h3>
<ul>
	<li><b>Web</b>. The Web module provides basic web-oriented integration features such as multipart file-upload functionality and the initialization of the IoC container using servlet listeners and a web-oriented application context.</li>
	<li><b>Web-MVC</b>. The Web-MVC module contains Spring's Model-View-Controller (MVC) implementation for web applications.</li>
	<li>Web-Socket. The Web-Socket module provides support for WebSocket-based, two-way communication between the client and the server in web applications.</li>
	<li>Web-Portlet. The Web-Portlet module provides the MVC implementation to be used in a portlet environment and mirrors the functionality of Web-Servlet module.</li>
</ul>

<h3>Miscellaneous</h3>
<ul>
	<li>AOP. The AOP module provides an aspect-oriented programming implementation allowing you to define method-interceptors and pointcuts to cleanly decouple code that implements functionality that should be separated.</li>
	<li>Aspects. The Aspects module provides integration with AspectJ, which is again a powerful and mature AOP framework.</li>
	<li>Instrumentation. The Instrumentation module provides class instrumentation support and class loader implementations to be used in certain application servers.</li>
	<li>Messaging. The Messaging module provides support for STOMP as the WebSocket sub-protocol to use in applications. It also supports an annotation programming model for routing and processing STOMP messages from WebSocket clients.
	<li>Test. The Test module supports the testing of Spring components with JUnit or TestNG frameworks.</li>
</li>
</ul>

<p>Because we are using Maven right now, so it's not necessary to download Spring manually. Instead, as long as we configure the pom.xml of Maven correctly, Maven will download the Spring for us.</p>

<p>In the next note, we will start to use IntelliJ Idea and Maven to build a sample Spring project.</p>
