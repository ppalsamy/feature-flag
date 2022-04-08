# Feature Flag Implementation

This application is to demonstrate how to implement Feature Flag with spring cloud config server

![feature-flag-architecture](img/feature-flag-architecture.png)
## Dependencies
Application need to fetch the configuration from config server, so `build.gradle` should have the below dependency
* Spring cloud config `implementation 'org.springframework.cloud:spring-cloud-starter-config:3.1.0'`
## Configuration
`application.properties` or `application.yml`

* `spring.application.name` is the name of application, feature flag's will be stored with this name.
* `spring.config.import` - This property need to be specified as `configserver:`
* `spring.cloud.config.uri` provide the config server uri e.g `http://localhost:8188/`

below is the complete configuration
~~~
spring:
  application:
    name: feature-flag-client
  config:
    import: "configserver:"
  cloud:
    config:
      uri: http://localhost:8188/
~~~
**Note:**  Config Server needs to be running. Use [config-server](/config-server) repo and start in local. 

## Runtime change
Now, what if some feature needs to be activated while application is running? Is it possible without restart?
These configurations are not enough to get the changes at runtime. So, actuator will help.

- `build.gradle` should be added with actuator dependency `implementation 'org.springframework.boot:spring-boot-starter-actuator:2.6.1'`
- bean needs to be annotated with `@RefereshScope`

Auto-configuration of the Actuator’s refresh endpoint will be used via JMX, but it is possible to use it via HTTP:
~~~
management.endpoints.web.exposure.include: "refresh"
~~~


## Refresh the change

Call POST method of `/actuator/refresh` which refreshes the config's from config server.
either use `curl` or `postman`
~~~ 
curl -X POST http://localhost:8080/actuator/refresh
~~~
This is mandatory and manual process to get our config change at runtime. 
