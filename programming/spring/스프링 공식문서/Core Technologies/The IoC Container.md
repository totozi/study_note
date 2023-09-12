## Introduction to the Spring IoC Container and Beans
This chapter covers the Spring Framework implementation of the Inversion of Control (IoC) principle. IoC is also known as dependency injection (DI). It is a process whereby objects define their dependencies (that is, the other objects they work with) only through constructor arguments, arguments to a factory method, or properties that are set on the object instance after it is constructed or returned from a factory method. The container then injects those dependencies when it creates the bean. This process is fundamentally the inverse (hence the name, Inversion of Control) of the bean itself controlling the instantiation or location of its dependencies by using direct construction of classes or a mechanism such as the Service Locator pattern.

- DI라고도 알려진 IoC는 객체들이 그들의 의존성을 스스로 정의하는 프로세스이다.
- 즉, 자신과 같이 작동하는 다른 객체들(의존성)을 스스로 정의한다는 것.
- 생성자 인자를 통하거나 인자를 팩토리 메서드에 전달하거나 오브젝트에 대한 프로퍼티 설정을 미리하여 팩토리메서드로부터 생성되거나 리턴되어지는 방법을 통해 의존성을 정의한다.
- 그런 다음 컨테이너는 빈을 생성할 떄 오브젝트들의 의존성을 주입한다.
- 이 과정은 근본적으로 빈의 역전이라고 할 수 있는데, 빈 그자체가 그것의 의존성을 실체화하거나 특정하는 것을 컨트롤하기 때문이다. 빈은 직접적인 클래스의 생성이나 서비스 로케이터(중개자) 패턴같은 메커니즘을 이용하여 의존성을 인스턴스화하거나 특정한다.


The ```org.springframework.beans``` and ```org.springframework.context``` packages are the basis for Spring Framework’s IoC container. The ```BeanFactory``` interface provides an advanced configuration mechanism capable of managing any type of object. ```ApplicationContext``` is a sub-interface of ```BeanFactory```. It adds:
- Easier integration with Spring’s AOP features
- Message resource handling (for use in internationalization)
- Event publication
- Application-layer specific contexts such as the ```WebApplicationContext``` for use in web applications.

```org.springframework.beans``` 와 ```org.springframework.context``` 스프링 IoC 컨테이너의 기초가 되는 패키지이다. ```BeanFactory``` 인터페이스는 발전된 설정 메커니즘을 제공한다.
이 메커니즘은 어떤 형태의 타입이든 다룰 수 있게 해준다.
```ApplicationContext```는 ```BeanFactory```의 하위 인터페이스이다.
아래의 추가 기능이 있다.
- 스프링 AOP와의 더 쉬운 통합
- 메시지 자원 핸들링
- 이벤트 발행기능
- 응용 계층 특화 컨텐스트 제공(웹 어플리케이션을 위한 ```WebApplicationContext```가 있다.)

In short, the ```BeanFactory``` provides the configuration framework and basic functionality, and the ```ApplicationContext``` adds more enterprise-specific functionality. The ```ApplicationContext``` is a complete superset of the ```BeanFactory``` and is used exclusively in this chapter in descriptions of Spring’s IoC container. For more information on using the ```BeanFactory``` instead of the ```ApplicationContext```, see the section covering the BeanFactory API.
- 다시 말해, ```BeanFactory```는 설정 프레임워크를 제공하고 기본 기능을 제공한다. ```ApplicationContext```는 더 기업 특화형 기능을 게공한다. ```ApplicationContext```는 ```BeanFactory```의 완전한 상위집합이다. 

In Spring, the objects that form the backbone of your application and that are managed by the Spring IoC container are called beans. A bean is an object that is instantiated, assembled, and managed by a Spring IoC container. Otherwise, a bean is simply one of many objects in your application. Beans, and the dependencies among them, are reflected in the configuration metadata used by a container.
- 스프링에서 어플리케이션의 중추가 되는 오브젝트들은 스프링 IoC에 의해 관리되며 Bean이라고 불린다. Bean은 스프링 IoC컨테이너에 의해 인스턴스화되고 조립되고 관리되는 객체이다. 빈들과 그들사이의 의존성들을 컨테이너에 의해 사용되는 설정 메타데이터 안 에 투영되어진다. (메타데이터를 바탕으로 컨테이너에 의해 생성, 관리, 조립 등등이 되어진다는 뜻)

---

## Container Overview
The ```org.springframework.context.ApplicationContext``` interface represents the Spring IoC container and is responsible for instantiating, configuring, and assembling the beans. The container gets its instructions on what objects to instantiate, configure, and assemble by reading configuration metadata. The configuration metadata is represented in XML, Java annotations, or Java code. It lets you express the objects that compose your application and the rich interdependencies between those objects.
- ```org.springframework.context.ApplicationContext``` 인터페이스는 스프링 IoC 컨테이너를 대표하는 인터페이스이다. bean을 인스턴스화하고 구성하고 조립하는 역할을 한다.
- 컨테이너는 구성 메타데이터를 읽어 어떤 오브젝트들을 인스턴스화하고 구성하고 조립할 지에 대한 지시사항을 얻는다.
- 그런 구성 메타데이터는 XML이나 Java 어노테이션 혹은 Java 코드 자체로 나타내어질 수 있다. 이 메타데이터는 어플리케이션을 구성하는 오브젝트들과 그 오브젝트들 사이의 의존관계들을 개발자 스스로 표현할 수 있도록 한다.

Several implementations of the ```ApplicationContext``` interface are supplied with Spring. In stand-alone applications, it is common to create an instance of ClassPathXmlApplicationContext or FileSystemXmlApplicationContext. While XML has been the traditional format for defining configuration metadata, you can instruct the container to use Java annotations or code as the metadata format by providing a small amount of XML configuration to declaratively enable support for these additional metadata formats.
- 몇몇 ```ApplicationContext``` 인터페이스를 구현한 클래스들이 제공된다. Stand-alone 어플리케이션에서는 ```ClassPathXmlApplicationContext``` 나 ```FileSystemXmlApplicationContext```의 인스턴스를 생성하는 게 보통이다.
- XML이 구성 메타데이터를 정의하는 전통적인 포맷이지만 자바 어노테이션이나 코드와 약간의 XML을 조합하여 메타데이터를 구성할 수 있다.

In most application scenarios, explicit user code is not required to instantiate one or more instances of a Spring IoC container. For example, in a web application scenario, a simple eight (or so) lines of boilerplate web descriptor XML in the web.xml file of the application typically suffices (see Convenient ApplicationContext Instantiation for Web Applications). If you use the Spring Tools for Eclipse (an Eclipse-powered development environment), you can easily create this boilerplate configuration with a few mouse clicks or keystrokes.
- 대부분의 어플리케이션 시나리오에서, 스프링 IoC 컨테이너를 인스턴스화하기 위해 명시적인 유저 코드가 필요하지 않다.
- web.xml의 몇 줄만 있으면 충분하다. boilerplate(변경없이 재사용 가능한)한 설정이다.

The following diagram shows a high-level view of how Spring works. Your application classes are combined with configuration metadata so that, after the ```ApplicationContext``` is created and initialized, you have a fully configured and executable system or application.
- 어플리케이션의 클래스들은 ```ApplicationContext```가 생성되고 인스턴스화된 후 구성 메타데이터와 조합되어진다. 그러면 완전히 구성되고 실행가능한 시스템이나 어플리케이션의 상태가 되는 것이다. 

---

## Configuration Metadata

As the preceding diagram shows, the Spring IoC container consumes a form of configuration metadata. This configuration metadata represents how you, as an application developer, tell the Spring container to instantiate, configure, and assemble the objects in your application.
- 스프링 IoC 컨테이너는 구성 메타데이터를 이용한다. 구성 메타데이터는 개발자가 스프링 컨테이너에게 어떤 방식으로 오브젝트들은 인스턴스화하고 구성하고 조립하는 지에 대해 표현 것을 나타낸다.

Configuration metadata is traditionally supplied in a simple and intuitive XML format, which is what most of this chapter uses to convey key concepts and features of the Spring IoC container.
- 구성 메타데이터는 전통적으로 단순하고 직관적인 XML 포맷으로 작성되어 왔다.
  
<blockquote>
XML-based metadata is not the only allowed form of configuration metadata. The Spring IoC container itself is totally decoupled from the format in which this configuration metadata is actually written. These days, many developers choose Java-based configuration for their Spring applications.
- XML 기반의 메타데이터만 허용되는 것은 아니다. 스프링 IoC 컨테이너는 메타데이터 포맷과 상관없도록 되어 있다. 요즘은 자바 기반의 구성이 많이 쓰여지는 편이다.
</blockquote>

For information about using other forms of metadata with the Spring container, see:
- **Annotation-based configuration**: define beans using annotation-based configuration metadata.
  
- **Java-based configuration**: define beans external to your application classes by using Java rather than XML files. To use these features, see the @Configuration, @Bean, @Import, and @DependsOn annotations.

Spring configuration consists of at least one and typically more than one bean definition that the container must manage. XML-based configuration metadata configures these beans as <bean/> elements inside a top-level <beans/> element. Java configuration typically uses @Bean-annotated methods within a @Configuration class.
- 스프링 구성은 적어도 하나 이상의 bean 정의로 이루어져 있고 컨테이너가 반드시 그 빈을 관리해야 한다. 
- 
These bean definitions correspond to the actual objects that make up your application. Typically, you define service layer objects, persistence layer objects such as repositories or data access objects (DAOs), presentation objects such as Web controllers, infrastructure objects such as a JPA EntityManagerFactory, JMS queues, and so forth. Typically, one does not configure fine-grained domain objects in the container, because it is usually the responsibility of repositories and business logic to create and load domain objects.
- 이러한 bean 정의들은 실제 어플리케이션을 구성하는 오브젝트들과 일치한다. 전형적으로, 개발자는 서비스 레이어, 퍼시스턴스 레이어(DAO), 표현 레이어(Controller), 인프라(JPA EntityManagerFactory, JMS queues 같은) 오브젝트들을 정의한다. 세부적인 도메인 오브젝트 까지는 컨테이너 안 에 구성하지 않는다. 보통 도메인 오브젝트들을 생성하고 로드하는 것은 리포지토리나 비즈니스 로직의 역할이기 때문이다.(VO, DTO를 Bean으로 등록하지 않는 이유인듯)

The following example shows the basic structure of XML-based configuration metadata:
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="..." class="...">  
		<!-- collaborators and configuration for this bean go here -->
	</bean>

	<bean id="..." class="...">
		<!-- collaborators and configuration for this bean go here -->
	</bean>

	<!-- more bean definitions go here -->

</beans>
```
</code>

- The id attribute is a string that identifies the individual bean definition.
- id 속성은 개별 bean정의를 식별하게 한다.
- The class attribute defines the type of the bean and uses the fully qualified class name.
- 클래스 속성은 bean의 타입을 정의하고 완전한 형태의 클래스 이름(패키지명을 포함한)을 사용한다.
   
   
The value of the id attribute can be used to refer to collaborating objects. The XML for referring to collaborating objects is not shown in this example. See Dependencies for more information.
- id 속성의 값을 집합적인 오브젝트들을 나타내는 데 사용될 수 있다.

## Instantiating a Container
The location path or paths supplied to an **ApplicationContext constructor** are resource strings that let the container load configuration metadata from a variety of external resources, such as the local file system, the Java CLASSPATH, and so on.
- ApplicationContext 생성자에 주어지는 위치 경로는 리소스 문자열들인데, 그것들이 컨테이너(ApplicationContext)가 구성 메타데이터를 로드하도록 해준다. 이런 구성 메타데이터들은 자바 클래스패스나 로컬 파일 시스템 등의 다양한 외부 리소스가 될 수 있다.

```java
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");
```

After you learn about Spring’s IoC container, you may want to know more about Spring’s Resource abstraction (as described in Resources), which provides a convenient mechanism for reading an InputStream from locations defined in a URI syntax. In particular, Resource paths are used to construct applications contexts, as described in Application Contexts and Resource Paths.
- 스프링 IoC 컨테이너를 배우고 나면 스프링의 리소스 추상화를 궁금해 할 지도 모르겠다. 이런 리소스 추상화는 URI에 적힌 InputStream을 읽어어내는 편리한 메커니즘을 제공한다. 리소스 경로가 어플리케이션 컨텍스트를 생성하는 데 사용된다.

The following example shows the service layer objects (services.xml) configuration file:
- 아래는 서비스 레이어의 오브젝트들의 구성파일이다.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd">

	<!-- services -->

	<bean id="petStore" class="org.springframework.samples.jpetstore.services.PetStoreServiceImpl">
		<property name="accountDao" ref="accountDao"/>
		<property name="itemDao" ref="itemDao"/>
		<!-- additional collaborators and configuration for this bean go here -->
	</bean>

	<!-- more bean definitions for services go here -->

</beans>
```

The following example shows the data access objects daos.xml file:
- 아래는 DAO 오브젝트들의 구성파일이다.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="accountDao"
		class="org.springframework.samples.jpetstore.dao.jpa.JpaAccountDao">
		<!-- additional collaborators and configuration for this bean go here -->
	</bean>

	<bean id="itemDao" class="org.springframework.samples.jpetstore.dao.jpa.JpaItemDao">
		<!-- additional collaborators and configuration for this bean go here -->
	</bean>

	<!-- more bean definitions for data access objects go here -->

</beans>
```

In the preceding example, the service layer consists of the PetStoreServiceImpl class and two data access objects of the types JpaAccountDao and JpaItemDao (based on the JPA Object-Relational Mapping standard). The property name element refers to the name of the JavaBean property, and the ref element refers to the name of another bean definition. This linkage between id and ref elements expresses the dependency between collaborating objects. For details of configuring an object’s dependencies, see Dependencies.
- 앞선 예시를 살펴보면, 서비스 레이어는 PetStoreServiceImpl와 2개의 DAO 오브젝트들(JpaAccountDao and JpaItemDao)로 구성되어 있다. 프로퍼티 이름 요소는 java bean 프로퍼티의 이름을 뜻하고 ref 요소는 다른 java bean 정의의 이름을 나타낸다. (즉 daos.xml에 있는 bean id를 뜻한다.)
- 이 id와 ref 요소 사이의 연결이 서로 협조하는 오브젝트들의 연결을 나타내는 것이다.(service class와 dao class)
  
---

## Composing XML-based Configuration Metadata
It can be useful to have bean definitions span multiple XML files. Often, each individual XML configuration file represents a logical layer or module in your architecture.
- 정의들이 여러 XML 파일로 확장되도록 하는 것이 유용했었다고 할 수 있다. 종종 각각의 XML 구성 파일이 논리적 계층이나 모듈을 나타내기도 한다.
You can use the application context constructor to load bean definitions from all these XML fragments. This constructor takes multiple Resource locations, as was shown in the previous section. Alternatively, use one or more occurrences of the ```<import/>``` element to load bean definitions from another file or files. The following example shows how to do so:
- 어플리케이션 컨텍스트 생성자를 이용해 모든 XML들로부터 bean 정의를 로드할 수 있다. 이 생성자는 여러 리소스 로케이션을 갖게 된다.(서비스 xml이 dao xml을 참조하므로 서비스 오브젝트를 생성하면 daos.xml의 로케이션을 자동적으로 갖게 된다.) 이에 대한 대안으로  하나이상의 ```<import/>``` 요소를 선언하여 다른 파일로부터 bean 정의를 가져올 수 있다.  

```xml
<beans>
	<import resource="services.xml"/>
	<import resource="resources/messageSource.xml"/>
	<import resource="/resources/themeSource.xml"/>

	<bean id="bean1" class="..."/>
	<bean id="bean2" class="..."/>
</beans>
```

In the preceding example, external bean definitions are loaded from three files: services.xml, messageSource.xml, and themeSource.xml. All location paths are relative to the definition file doing the importing, so services.xml must be in the same directory or classpath location as the file doing the importing, while messageSource.xml and themeSource.xml must be in a resources location below the location of the importing file. As you can see, a leading slash is ignored. However, given that these paths are relative, it is better form not to use the slash at all. The contents of the files being imported, including the top level <beans/> element, must be valid XML bean definitions, according to the Spring Schema.
- 앞의 예제에서, 외부 빈 정의가 다른 3개의 파일로부터 로드되었다. 모든 로케이션 경로는 import를 하는 파일에 대해 상대적인 경로를 가진다.
<blockquote>
It is possible, but not recommended, to reference files in parent directories using a relative "../" path. Doing so creates a dependency on a file that is outside the current application. In particular, this reference is not recommended for classpath: URLs (for example, classpath:../services.xml), where the runtime resolution process chooses the “nearest” classpath root and then looks into its parent directory. Classpath configuration changes may lead to the choice of a different, incorrect directory.
- ../ 같은 걸로 상위 폴더 가서 참조하는 건 좋지 않다.

You can always use fully qualified resource locations instead of relative paths: for example, file:C:/config/services.xml or classpath:/config/services.xml. However, be aware that you are coupling your application’s configuration to specific absolute locations. It is generally preferable to keep an indirection for such absolute locations — for example, through "${…​}" placeholders that are resolved against JVM system properties at runtime.
- 절대 경로를 사용해도 된다. 하지만 커플링이 안되어있을 수 있으니 조심해라
</blockquote>
The namespace itself provides the import directive feature. Further configuration features beyond plain bean definitions are available in a selection of XML namespaces provided by Spring — for example, the context and util namespaces.
- 네임스페이스 그 자체가 임포트 구문의 특징을 가진다.(그 자체가 임포트 구문). 더 나아가서 평범한 bean 정의 이상의 특징들이 스프링에서 제공하는 일련의 XML 네임스페이스를 이용할 수 있다. 예를 들면 context와 util 네임스페이스가 있다.
