# SpringCloudConfig
Refreshing the config changes with Spring Cloud Bus

In this project, I will create a Spring Clud Configuration Server, two clients (department-service and employee-service). 
Refresh configs on github, refresh one of the clients, and broadcast the /actuator/bus-refresh event to others. 

# What is the proble we are trying to solve?
To avoid calling /actuator/refresh for each service, we can just trigger the refresh event on one end point, and Spring Cloud Bus will prpogate the changes to other end points.

# How Beans will be notified about the new changes during runtime?
We will be using annotation @RefreshScope that will take care of doing that during runtime.

  >Convenience annotation to put a <code>@Bean</code> definition in
  >{@link org.springframework.cloud.context.scope.refresh.RefreshScope refresh scope}.
  >Beans annotated this way can be refreshed at runtime and any components that are using
  >them will get a new instance on the next method call, fully initialized and injected
  >with all dependencies.

# What is Spring Cloud Bus?
Spring Cloud Bus links the nodes of a distributed system with a lightweight message broker (such as RabbitMQ or Kafk). ... A key idea is that the bus is like a distributed actuator for a Spring Boot application that is scaled out. However, it can also be used as a communication channel between apps.

# How can we hookup Spring Cloud Bus with a message broker?
There are two things we added in order to do that:
  * Added a dependency to pom file
  
  > spring-cloud-starter-bus-amqp
  > spring-boot-starter-amqp
  
  * Added some configurations into application.properties file 
  
   > #RabbitMQ connection details
   > management.endpoints.web.exposure.include=*
   
   > spring.rabbitmq.host=localhost
   
   > spring.rabbitmq.port=5672
   
   > spring.rabbitmq.username=guest
   
   > spring.rabbitmq.password=guest
   

 * Introduced bootstrap.properties file
 
  > spring.application.name = department-service
  
  > spring.cloud.config.uri = http://localhost:8080
  
  
 #### 1) How do we handle transactions in MongoDB (if they fail)? 
 #### 2) How do we change/update configuration on the fly ? 
 #### 3) How do micro-services communicate with each others ? 
 #### 4) What are the different scopes of beans in Spring? 
 #### 5) What do you do when you have multiple services running, and some of them starts slowing down ?
 #### 6) Based on what criteria you package/create a micro-service? 
 #### 7) Where do you use local storage? where do you use cookies? 
 #### 8)How does Spring handle/track  session scope? request scope?


 #### 1) https://hackernoon.com/mongodb-transactions-5654cdb8fd24 (see Why use Transactions)

Mastering MongoDB - Introducing multi-document transactions  in v4.0

• When a transaction is committed, all data changes made in the transaction are saved.
• If any operation in the transaction fails, the transaction aborts
• When a transaction aborts/aborted, all data changes made in the transaction are discarded.
• Until a transaction is committed, no write operations in the transaction are visible outside the transaction.
￼
code example here: https://docs.mongodb.com/master/core/transactions/


Transactions — MongoDB Manual
For situations that require atomicity of reads and writes to multiple documents (in a single or multiple collections), MongoDB supports multi-document transactions.(4 kB)
http://s3.amazonaws.com/info-mongodb-com/_com_assets/cms/mongodb-for-giant-ideas-bbab5c3cf8.png
￼
shows how to handle the exception
￼
 #### 2 needs more clarification, what technology are they referring to? If it was spring, Spring Boot Actuator has an endpoint that can be invoked to trigger a refresh (/actuator/refresh) https://springbootdev.com/2018/07/17/spring-cloud-config-refreshing-the-config-changes-with-spring-cloud-bus-part-2/


www.SpringBootDev.com | Chathuranga Tennakoon
Spring Cloud Config : Refreshing the config changes with Spring Cloud Bus (Part 2)
You can refer the part 1 of this article as follows.
Click here for Part 1 
 
The Problem
The previous article (click here to visit it) has described how to use Spring Cloud Config Server as a centralized location for keeping the configuration properties related to the application services (microservices).  The application services will act as Config Clients who will communicate with Config Server to retrieve the properties related to them.

￼
####  3 in general it seems that having microservices communicate between each other is an anti-pattern. all the loose coupling creating by splitting out the services goes away because now there are dependencies between services
￼
#### 4 prototype, request, session, singleton, global-session
￼
#### 5 without appropriate monitoring tools (i.e. New Relic) not much. about the only option there would be to scour the logs (if logging was even done) looking for calls that take a long time to narrow it down to a component. if no monitoring and logging, then restart 
￼
####  6 it's about partitioning functionality (bounded contexts in domain driven design). Think about the various approaches to packaging:
• One approach organizes things by type/role: controllers, repositories, services, etc
• Another approach organizes things by domain: customers, accounts, authentication, authorization, etc

One thing i've noticed is that organizing by type/role works for small projects, but once they grow past a certain point cohesion starts to break down and you have a grab-bag of stuff in there.

At that point organizing by domain produces greater coherence. The draw back is that enough of the domain needs to be understood/defined to organize that way. So for greenfield projects the structure will emerge over time; in a legacy application, it should all be there it just needs to be broken out.

####  7 taken literally (where are they used):
• local storage is used in the browser
• cookies are used both in the browser and in server-side logic

####  8 spring uses proxies for session and request scopes. internally the scoped instance (non-proxy) would be stuffed into the thread handling the request and the proxy would pull the real instance from the thread. a combination of  proxy (either java's dynamic proxy or a cglib generated proxy) + thread locals



