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
