StartWithActiviti2
==================
Activiti In Action
===
Chapter4

## Outline

- [part1](#part1)
  + [Developing and testing with the Activiti Engine](#developing-and-testing-with-the-activiti-engine)
- [part2](#part2)
  + [Activiti Engine API](#activiti-engine-api)
    * [RuntimeService](#suntimeservice)
    * [TaskService](#taskservice)
    * [RepositoryService](#repositoryservice)
    * [IdentityService](#identityservice)
    * [HistoryService](#historyservice)
- [part3](#part3)
- [part4](#part4)

Chapter5

## An simple process to activiti
- [Add to activiti exploer](#add-to-activiti-exploer)

## part1
### Developing and testing with the Activiti Engine
- Running the Activiti Engine in the JVM with an in-memory database (H2)
- Running the Activiti Engine in the JVM with a standalone database (H2)
- Running the Activiti Engine on an application server (Apache Tomcat) with a
standalone database (H2)

## part2
### Activiti Engine API

- **RuntimeService**: The runtime service provides an interface to start and query process instances. In addition, process variables can be retrieved and set, and processes can be signaled to leave a wait state.
- **TaskService**: Exposes operations to manage human (standalone) Tasks, such as claiming, completing and assigning tasks.
- **RepositoryService**: The repository service provides functionality to deploy, query, delete, and retrieve process definitions. 
- **IdentityService**: Used for managing Users, Groups and the relations between them
- **ManagementService**: The management service can be used to query the Activiti tables and execute jobs.
- **HistoryService**: To retrieve information about completed process instances.
- **FormService**: To work with the user task forms generated by the Activiti form engine, the form service provides several methods.

#### RuntimeService

The primary usage of the RuntimeService is to start new process instances based on a
specific process definition.

*額外提到Junit運作基本機制*
![JUnitLifecycle_simple.png](img/JUnitLifecycle_simple.png)

#### TaskService

- add task
- The user or group assigned as a candidate
- Only one user can be assigned through task
- Claim and complete a task

**@Deployment**
With the @Deployment, the book order process definition is deployed to the Activiti engine.

**@Rule**
public ActivitiRule activitiRule = new ActivitiRule("activiti.cfg-mem.xml");

If no "activiti.cfg-mem.xml", then use the default "activiti.cfg.xml"

#### RepositoryService

It can also be used to query the Activiti engine for deployment artifacts and
process definitions. In this section(4.2.3), you’ll also use the delete functionality, which the
RepositoryService interface provides.

The RepositoryService interface provides two types of delete methods:
- The deleteDeployment method with a deployment identifier and a **false** input
parameter, which only deletes the deployment and not the corresponding process
instance data. When there are still running process instances, you’ll get an
exception when running this method.
- The deleteDeployment method with a deployment identifier and a **true** input
parameter, which deletes all information regarding the process definition, running
instances, and history. If you want all process data, including running process
instances, to be deleted, you should use a Boolean value of true for the
second input parameter.

#### IdentityService

新增成員或是一個 group 的方法，新增後必須存檔。
```java
User newUser = identityService
.newUser("John Doe");
identityService.saveUser(newUser);
```
#### HistoryService
在 Actitivi 上，ACT_HI 是歷史資料，
ACT_RU 是執行中的資料。RU中的資料在結束完成後會被刪除。

若要儲存想顯示的變數資料(history):
default is activiti.cfg.xml. And add the following property to the process engine configuration:
```xml
<property name="history" value="full" />
```
- None—No history information is archived.
- Activity—All process and activity instance information is archived.
- Audit (default)—All process, activity instance, and form properties information
is archived.
- Full—The highest level of archiving; all audit information is archived and, additionally,
the updates to process variables and user task form properties are
stored.

## part3

The Java service task can be used in four ways:
- Java service task class
  *分同步和非同步
- Java service task class with field extensions
- Java service task with method or value expressions
  * When you use a Java bean as a process variable, make sure the bean implements the Serializable interface
- A delegate expression that defines a variable that is resolved to a Java bean at
runtime(在4-4中介紹)

**可先研究dependency injection、bean、spring、JDBC**

## part4

An ActivitiRule Spring bean must be added to the Spring configuration:
```xml
<bean id="activitiRule" class="org.activiti.engine.test.ActivitiRule">
<property name="processEngine" ref="processEngine" />
</bean>
```
ref = "bean" (參考)、value = "123"(直接設定數值)
**搭配spring dependence injection**


- 另外 spring 版本需一致，在 pom.xml 裡，需更改為3.1.2RELEASE

The ActivitiRule Spring bean must also be injected into the Spring-enabled unit
test via the @Autowire annotation:
```java
@Autowired
@Rule
public ActivitiRule activitiSpringRule;
```

To be able to
use the @Deployment annotation, you have to inject the ActivitiRule ,
which provides a hook into the Activiti Engine to deploy and undeploy process definitions.

使用 deployment 測試時，會在每個 Test 產生 engine 然後再刪除，因此會浪費許多時間，此方法會較好。

## Chapter 5
## An simple process to activiti
### Add to activiti exploer
- 執行 build.xml
- put a JAR file containing the Java service task in the webapps/activiti-explorer/WEB-INF/lib
- a BAR file is created in the src/main/resources/chapter5/dist，將 bar 打包上傳至 ativiti exploer
- 在 activiti exploer 上執行

另外此範例會用到 e-mil ，所以需架設 mail server。Download Apache James from http://james.apache.org and unzip the file in a directory
