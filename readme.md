NOTE: This project was taken from https://github.com/spring-projects/spring-petclinic, and was modified to include Chaos Monkey.

Chaos Monkey for Spring Boot
---

**Setup**

Include Chaos Monkey in the dependencies in your pom.xml.

~~~
      <dependency>
          <groupId>de.codecentric</groupId>
          <artifactId>chaos-monkey-spring-boot</artifactId>
          <version>2.0.2</version>
      </dependency>
~~~

Turn on the Chaos Monkey endpoints in your application properties.

~~~
 management.endpoint.chaosmonkey.enabled=true
 management.endpoint.chaosmonkeyjmx.enabled=true

 management.endpoints.web.exposure.include=*

 management.endpoints.web.exposure.include=health,info,chaosmonkey
~~~

Chaos Monkey for Spring Boot uses watchers to "assault" various types of classes. It can be used to assault classes annotated with @Service, @RestController, @Controller, @Repository, and @Component.
You will have to indicate which watchers you would like to use prior to compilation and runtime.

Include the following in either your application.properties or an application.yml. It is encouraged to create a separate application-chaos-monkey.yml. Be sure to indicate which watchers you would like with "true" or "false":

YOU CANNOT CHANGE THIS DURING RUNTIME. If no watchers are enabled, no assaults will be performed.

application.properties
~~~
chaos.monkey.watcher.component=false
chaos.monkey.watcher.controller=false
chaos.monkey.watcher.restController=true
chaos.monkey.watcher.service=true
chaos.monkey.watcher.repository=false
~~~

application.yml
~~~
chaos:
  monkey:
    enabled: true
    watcher:
      component: false
      controller: false
      repository: false
      rest-controller: true
      service: true
~~~

**Running your application with Chaos Monkey enabled**

Make sure you are in the project directory. Run:

~~~
./mvnw package
java -jar <YOURAPP>.jar --spring.profiles.active=chaos-monkey
~~~

**Available Requests**

We have set up our properties to allow us to send Chaos Monkey requests during runtime.

The first request you will want to send is one to verify that Chaos Monkey is enabled. All requests are listed below.
You will also want to send a POST request to "/chaosmonkey/assaults" with the assault configuration desired. Further information regarding Assaults is in the next section.

| endpoint  | Description  | Method |
|---|---|---|
| /chaosmonkey  | Get the running Chaos Monkey configuration  | GET  |
| /chaosmonkey/status  |  Is Chaos Monkey enabled or disabled? | GET  |
| /chaosmonkey/enable  |  Enable Chaos Monkey | POST  |
| /chaosmonkey/disable  | Disable Chaos Monkey  |  POST |
| /chaosmonkey/watcher  | Get the running Watcher configuration. NOTE: Watcher cannot be changed at runtime, they are Spring AOP components that have to be created when the application starts.  |  GET |
| /chaosmonkey/assaults  |  Get the running Assaults configuration | GET  |
| /chaosmonkey/assaults  |  Change the Assaults configuration | POST  |

See https://codecentric.github.io/chaos-monkey-spring-boot/2.0.2/#_examples for examples of request responses and request bodies.

**Assaults**

The following is an example of an assault configuration.

The important properties to note are:

| Property | Description |
|---|---|
| level | How frequent the "assaults" are. level 1 = each request is attacked, level 5 = every 5th request is attacked, level 10 = every 10th request is attacked. Available values are 1 - 10|
| latencyRangeStart | minimum latency during assault |
|latencyRangeEnd | maximum latency during assault |
| latencyActive | Are latency assaults on or off |
| exceptionsActive | Are exception assaults on or off |
| exception | the exception type and message |
| killApplicationActive| Are assaults that will kill the application on or off|
| watchedCustomServices| You may specify a list of particular methods/services you want Chaos Monkey to assault. If you provide an empty list ([]) or null, all requests (satisfying the watcher configuration and assault level) will be assaulted|

~~~
{
    "level": 5,
    "latencyRangeStart": 1000,
    "latencyRangeEnd": 3000,
    "latencyActive": true,
    "exceptionsActive": false,
    "exception": {
        "type": null,
        "arguments": null
    },
    "killApplicationActive": false,
    "watchedCustomServices": null,
    "frozen": false,
    "targetSource": {
        "target": {
            "level": 5,
            "latencyRangeStart": 1000,
            "latencyRangeEnd": 3000,
            "latencyActive": true,
            "exceptionsActive": false,
            "exception": {
                "type": null,
                "arguments": null
            },
            "killApplicationActive": false,
            "watchedCustomServices": null
        },
        "static": true,
        "targetClass": "de.codecentric.spring.boot.chaos.monkey.configuration.AssaultProperties"
    },
    "targetClass": "de.codecentric.spring.boot.chaos.monkey.configuration.AssaultProperties",
    "proxiedInterfaces": [],
    "advisors": [
        {
            "order": 2147483647,
            "advice": {},
            "pointcut": {
                "classFilter": {},
                "methodMatcher": {
                    "runtime": false
                }
            },
            "perInstance": true
        }
    ],
    "proxyTargetClass": true,
    "exposeProxy": false,
    "preFiltered": false
}
~~~
-------------------------------------

# Spring PetClinic Sample Application [![Build Status](https://travis-ci.org/spring-projects/spring-petclinic.png?branch=master)](https://travis-ci.org/spring-projects/spring-petclinic/)
Deploy this sample application to Pivotal Web Services:
<a href="https://push-to.cfapps.io?repo=https%3A%2F%2Fgithub.com%2Fspring-projects%2Fspring-petclinic.git">
    <img src="https://push-to.cfapps.io/ui/assets/images/Push-to-Pivotal-Dark.svg" width="130" alt="Push" align="top">
</a>

## Understanding the Spring Petclinic application with a few diagrams
<a href="https://speakerdeck.com/michaelisvy/spring-petclinic-sample-application">See the presentation here</a>

## Running petclinic locally
Petclinic is a [Spring Boot](https://spring.io/guides/gs/spring-boot) application built using [Maven](https://spring.io/guides/gs/maven/). You can build a jar file and run it from the command line:


```
git clone https://github.com/spring-projects/spring-petclinic.git
cd spring-petclinic
./mvnw package
java -jar target/*.jar
```

You can then access petclinic here: http://localhost:8080/

<img width="1042" alt="petclinic-screenshot" src="https://cloud.githubusercontent.com/assets/838318/19727082/2aee6d6c-9b8e-11e6-81fe-e889a5ddfded.png">

Or you can run it from Maven directly using the Spring Boot Maven plugin. If you do this it will pick up changes that you make in the project immediately (changes to Java source files require a compile as well - most people use an IDE for this):

```
./mvnw spring-boot:run
```

## In case you find a bug/suggested improvement for Spring Petclinic
Our issue tracker is available here: https://github.com/spring-projects/spring-petclinic/issues


## Database configuration

In its default configuration, Petclinic uses an in-memory database (HSQLDB) which
gets populated at startup with data. A similar setup is provided for MySql in case a persistent database configuration is needed.
Note that whenever the database type is changed, the app needs to be run with a different profile: `spring.profiles.active=mysql` for MySql.

You could start MySql locally with whatever installer works for your OS, or with docker:

```
docker run -e MYSQL_ROOT_PASSWORD=petclinic -e MYSQL_DATABASE=petclinic -p 3306:3306 mysql:5.7.8
```

Further documentation is provided [here](https://github.com/spring-projects/spring-petclinic/blob/master/src/main/resources/db/mysql/petclinic_db_setup_mysql.txt).

## Working with Petclinic in your IDE

### Prerequisites
The following items should be installed in your system:
* Java 8 or newer.
* git command line tool (https://help.github.com/articles/set-up-git)
* Your preferred IDE 
  * Eclipse with the m2e plugin. Note: when m2e is available, there is an m2 icon in `Help -> About` dialog. If m2e is
  not there, just follow the install process here: https://www.eclipse.org/m2e/
  * [Spring Tools Suite](https://spring.io/tools) (STS)
  * IntelliJ IDEA
  * [VS Code](https://code.visualstudio.com)

### Steps:

1) On the command line
```
git clone https://github.com/spring-projects/spring-petclinic.git
```
2) Inside Eclipse or STS
```
File -> Import -> Maven -> Existing Maven project
```

Then either build on the command line `./mvnw generate-resources` or using the Eclipse launcher (right click on project and `Run As -> Maven install`) to generate the css. Run the application main method by right clicking on it and choosing `Run As -> Java Application`.

3) Inside IntelliJ IDEA

In the main menu, choose `File -> Open` and select the Petclinic [pom.xml](pom.xml). Click on the `Open` button.

CSS files are generated from the Maven build. You can either build them on the command line `./mvnw generate-resources`
or right click on the `spring-petclinic` project then `Maven -> Generates sources and Update Folders`.

A run configuration named `PetClinicApplication` should have been created for you if you're using a recent Ultimate
version. Otherwise, run the application by right clicking on the `PetClinicApplication` main class and choosing
`Run 'PetClinicApplication'`.

4) Navigate to Petclinic

Visit [http://localhost:8080](http://localhost:8080) in your browser.


## Looking for something in particular?

|Spring Boot Configuration | Class or Java property files  |
|--------------------------|---|
|The Main Class | [PetClinicApplication](https://github.com/spring-projects/spring-petclinic/blob/master/src/main/java/org/springframework/samples/petclinic/PetClinicApplication.java) |
|Properties Files | [application.properties](https://github.com/spring-projects/spring-petclinic/blob/master/src/main/resources) |
|Caching | [CacheConfiguration](https://github.com/spring-projects/spring-petclinic/blob/master/src/main/java/org/springframework/samples/petclinic/system/CacheConfiguration.java) |

## Interesting Spring Petclinic branches and forks

The Spring Petclinic master branch in the main [spring-projects](https://github.com/spring-projects/spring-petclinic)
GitHub org is the "canonical" implementation, currently based on Spring Boot and Thymeleaf. There are
[quite a few forks](https://spring-petclinic.github.io/docs/forks.html) in a special GitHub org
[spring-petclinic](https://github.com/spring-petclinic). If you have a special interest in a different technology stack
that could be used to implement the Pet Clinic then please join the community there.


## Interaction with other open source projects

One of the best parts about working on the Spring Petclinic application is that we have the opportunity to work in direct contact with many Open Source projects. We found some bugs/suggested improvements on various topics such as Spring, Spring Data, Bean Validation and even Eclipse! In many cases, they've been fixed/implemented in just a few days.
Here is a list of them:

| Name | Issue |
|------|-------|
| Spring JDBC: simplify usage of NamedParameterJdbcTemplate | [SPR-10256](https://jira.springsource.org/browse/SPR-10256) and [SPR-10257](https://jira.springsource.org/browse/SPR-10257) |
| Bean Validation / Hibernate Validator: simplify Maven dependencies and backward compatibility |[HV-790](https://hibernate.atlassian.net/browse/HV-790) and [HV-792](https://hibernate.atlassian.net/browse/HV-792) |
| Spring Data: provide more flexibility when working with JPQL queries | [DATAJPA-292](https://jira.springsource.org/browse/DATAJPA-292) |


# Contributing

The [issue tracker](https://github.com/spring-projects/spring-petclinic/issues) is the preferred channel for bug reports, features requests and submitting pull requests.

For pull requests, editor preferences are available in the [editor config](.editorconfig) for easy use in common text editors. Read more and download plugins at <https://editorconfig.org>. If you have not previously done so, please fill out and submit the [Contributor License Agreement](https://cla.pivotal.io/sign/spring).

# License

The Spring PetClinic sample application is released under version 2.0 of the [Apache License](https://www.apache.org/licenses/LICENSE-2.0).

[spring-petclinic]: https://github.com/spring-projects/spring-petclinic
[spring-framework-petclinic]: https://github.com/spring-petclinic/spring-framework-petclinic
[spring-petclinic-angularjs]: https://github.com/spring-petclinic/spring-petclinic-angularjs 
[javaconfig branch]: https://github.com/spring-petclinic/spring-framework-petclinic/tree/javaconfig
[spring-petclinic-angular]: https://github.com/spring-petclinic/spring-petclinic-angular
[spring-petclinic-microservices]: https://github.com/spring-petclinic/spring-petclinic-microservices
[spring-petclinic-reactjs]: https://github.com/spring-petclinic/spring-petclinic-reactjs
[spring-petclinic-graphql]: https://github.com/spring-petclinic/spring-petclinic-graphql
[spring-petclinic-kotlin]: https://github.com/spring-petclinic/spring-petclinic-kotlin
[spring-petclinic-rest]: https://github.com/spring-petclinic/spring-petclinic-rest
