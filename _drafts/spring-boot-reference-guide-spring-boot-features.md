---
title: spring boot reference guide-Spring Boot features
layout: post
tags: [Reference, Spring, Spring Boot, 정리중]
---

###### * 영문으로 작성된 레퍼런스를 구글 번역의 도움으로 내가 읽기 쉬운 형태로 보기 쉽게 하기 위해 작성

###### * 필요한 부분부터 작성하기 시작했으므로 앞 부분은 차후 **필요에 따라** 추가 할수도..

###### * (+ 내용) <- 레퍼런스에 없는 추가 설명 표시 

###### * 완료되지 않음. 아직 진행중 

> Spring Boot Reference Guide
>
> Version : 2.1.4.RELEASE
>
> Url : <https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#using-boot>

# Part IV. Spring Boot features

이 섹션에서는 Spring Boot에 대해 자세히 설명합니다. 여기에서 사용 및 사용자 지정할 수있는 주요 기능에 대해 배울 수 있습니다. 아직 준비되지 않았다면 "[Part II, “Getting Started”](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#getting-started)"와 "[Part III, “Using Spring Boot”](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#using-boot)" section을 읽고 기본적인 기초 지식을 얻을 수 있습니다.



## 23. SpringApplication

`SpringApplication` class는 `main()` 메소드에서 시작되는 Spring application을 bootstrap하는 편리한 방법을 제공한다. 많은 경우 다음 예제와 같이 static `SpringApplication.run` 메서드에 위임 할 수 있습니다.

```
public static void main(String[] args) {
	SpringApplication.run(MySpringConfiguration.class, args);
}
```

응용프로그램이 시작되면 다음과 비슷한 내용이 표시됩니다 : 

```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::   v2.1.4.RELEASE

2013-07-31 00:08:16.117  INFO 56603 --- [           main] o.s.b.s.app.SampleApplication            : Starting SampleApplication v0.1.0 on mycomputer with PID 56603 (/apps/myapp.jar started by pwebb)
2013-07-31 00:08:16.166  INFO 56603 --- [           main] ationConfigServletWebServerApplicationContext : Refreshing org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@6e5a8246: startup date [Wed Jul 31 00:08:16 PDT 2013]; root of context hierarchy
2014-03-04 13:09:54.912  INFO 41370 --- [           main] .t.TomcatServletWebServerFactory : Server initialized with port: 8080
2014-03-04 13:09:56.501  INFO 41370 --- [           main] o.s.b.s.app.SampleApplication            : Started SampleApplication in 2.992 seconds (JVM running for 3.658)
```

기본적으로 application을 시작한 사용자와 같은 몇가지 시작 관련 세부 정보를 포함한 `INFO` logging messages가 표시됩니다. `INFO`가 아닌 log level이 필요하다면, [Section 26.4, “Log Levels”](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#boot-features-custom-log-levels)에 설명 된대로 설정할 수 있습니다.

### 23.1 Startup Failure

응용프로그램을 시작에 실패하면, 등록 된 `FailureAnalyzer`가 전용 오류 메시지와 문제를 해결하기위한 구체적인 조치를 제공 할 수 있습니다. 예를 들어 port `8080로 web application을 시작하고 해당 port가 이미 사용 중이면 다음 메시지와 비슷한 내용이 표시됩니다: 

```
***************************
APPLICATION FAILED TO START
***************************

Description:

Embedded servlet container failed to start. Port 8080 was already in use.

Action:

Identify and stop the process that's listening on port 8080 or configure this application to listen on another port.
```

* ! Note : Spring Boot는 수많은 `FailureAnalyzer` 구현을 제공하며 사용자가 [직접 추가](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#howto-failure-analyzer) 할 수 있습니다.

failure analyzers가 예외를 처리 할 수없는 경우에도 full conditions report를 표시하여 무엇이 잘못되었는지 보다 잘 이해할 수 있습니다. 이렇게하려면 [enable the `debug` property](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#boot-features-external-config) 또는 `org.springframework.boot.autoconfigure.logging.ConditionEvaluationReportLoggingListener`에 대해 [enable `DEBUG` logging](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#boot-features-custom-log-levels)해야합니다.

예를 들어, `java -jar`를 사용하여 응용프로그램을 실행하는 경우 다음과 같이 `debug` property를 활성화 할 수 있습니다 :

```
$ java -jar myproject-0.0.1-SNAPSHOT.jar --debug
```

### 23.2 Customizing the Banner

시작시 보여지는 배너는 classpath에 `banner.txt` 파일을 추가하거나 `spring.banner.location` property를 해당 파일의 위치로 설정하여 변경할 수 있습니다. 파일의 인코딩이 UTF-8이 아닌 경우 `spring.banner.charset`을 설정할 수 있습니다. 텍스트 파일 외에도 `banner.gif`, `banner.jpg`, 또는 `banner.png` 이미지 파일을 classpath에 추가하거나 `spring.banner.image.location` property를 설정할 수 있습니다. 이미지는 ASCII 아트 표현으로 변환되어 텍스트 배너 위에 인쇄됩니다.

`banner.txt` 파일에서 다음 자리 표시 자 중 하나를 사용할 수 있습니다 : 

**Table 23.1. Banner variables**

| Variable                                                     | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `${application.version}`                                     | `MANIFEST.MF`에 선언 된 응용프로그램의 버전 번호. 예를 들어, `Implementation-Version : 1.0`은 `1.0`으로 보여집니다. |
| `${application.formatted-version}`                           | `MANIFEST.MF`에 선언되고 표시형식(대괄호로 묶이고 `v`접두사가 붙은 형식)의 응용프로그램 버전 번호. 예를 들어 `(v1.0)`. |
| `${spring-boot.version}`                                     | 사용하고 있는 Spring Boot version. 예 : `2.1.4.RELEASE`.     |
| `${spring-boot.formatted-version}`                           | 사용하고 있는 Spring Boot version, 표시형식(대괄호로 묶이고 `v`접두사가 붙은 형식). 예 : `(v2.1.4.RELEASE)`. |
| `${Ansi.NAME}` (or `${AnsiColor.NAME}`, `${AnsiBackground.NAME}`, `${AnsiStyle.NAME}`) | `NAME`은 ANSI escape code의 이름입니다. 자세한 내용은 [AnsiPropertySource`](https://github.com/spring-projects/spring-boot/tree/v2.1.4.RELEASE/spring-boot-project/spring-boot/src/main/java/org/springframework/boot/ansi/AnsiPropertySource.java)를 참조하십시오. |
| `${application.title}`                                       | `MANIFEST.MF`에 선언된 응용프로그램의 title. 예를 들어 `Implementation-Title : MyApp`는 `MyApp`로 인쇄됩니다. |

* ! Tip : 프로그램적으로 배너를 생성하려면 `SpringApplication.setBanner(...)` 메서드를 사용할 수 있습니다. `org.springframework.boot.Banner` 인터페이스를 사용하고 자신만의 `printBanner() `메소드를 구현하십시오.

또한 `spring.main.banner-mode` property를 사용하여 배너를 `System.out` (`console`)에 인쇄할지, configured logger (`log`)로 전송할지 또는 전혀 생성하지 않을지 (`off`)를 결정할 수 있습니다.

인쇄된 배너는 singleBean으로 `springBootBanner`라는 이름으로 등록됩니다.

* ! Note : YAML은 `off`를 `false`로 매핑하므로 다음 예제와 같이 응용 프로그램에서 배너를 비활성화하려면 따옴표를 추가해야합니다.

  ```
  spring:
  	main:
  		banner-mode: "off"
  ```

### 23.3 Customizing SpringApplication

`SpringApplication` 디폴트가 마음에 들지 않으면, 대신 local instance를 생성하고 커스터마이징할 수 있다. 예를 들어, 배너를 끄려면 다음과 같이 작성할 수 있습니다 :

```java
public static void main(String[] args) {
	SpringApplication app = new SpringApplication(MySpringConfiguration.class);
	app.setBannerMode(Banner.Mode.OFF);
	app.run(args);
}
```

* ! Note : `SpringApplication`에 전달 된 생성자 인자는 Spring bean을 위한 configuration source이다. 대부분의 경우 이들은 `@Configuration` 클래스에 대한 참조이지만 XML configuration 또는 스캔해야하는 패키지에 대한 참조 일 수도 있습니다.

`application.properties` 파일을 사용하여 `SpringApplication`을 구성 할 수도 있습니다. 자세한 내용은 *Chapter 24, Externalized Configuration(외부화 된 구성)*을 참조하십시오.

configuration option의 전체 목록은  [`SpringApplication` Javadoc](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/api/org/springframework/boot/SpringApplication.html)을 참조하십시오.

### 23.4 Fluent(유창한) Builder API

`ApplicationContext` 계층(부모/자식 관계가있는 다중 컨텍스트)을 작성해야하거나 “fluent” builder API를 사용하려는 경우 `SpringApplicationBuilder`를 사용할 수 있습니다.

`SpringApplicationBuilder`를 사용하면 여러 메소드 호출을 함께 연결할 수 있으며 다음 예제와 같이 계층 구조를 만들 수있는 `parent` 및 `child` 메소드가 포함됩니다 : 

```java
new SpringApplicationBuilder()
		.sources(Parent.class)
		.child(Application.class)
		.bannerMode(Banner.Mode.OFF)
		.run(args);
```

* ! Note : `ApplicationContext` hierarchy를 만들때 몇가지 제한이 있습니다. 예를 들어 Web components는하위 컨텍스트에 포함되어야하며 동일한 `Environment`가 상위, 하위 컨텍스트 모두에 사용됩니다. 자세한 내용은 [`SpringApplicationBuilder` Javadoc](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/api/org/springframework/boot/builder/SpringApplicationBuilder.html)을 참조하십시오.

### 23.5 Application Events and Listeners

[`ContextRefreshedEvent`](https://docs.spring.io/spring/docs/5.1.6.RELEASE/javadoc-api/org/springframework/context/event/ContextRefreshedEvent.html)와 같은 일반적인 Spring Framework event 외에도 `SpringApplication`은 몇 가지 추가 애플리케이션 이벤트를 전송합니다.

* ! Note : 어떤 이벤트는 `ApplicationContext`가 생성되기 전에 실제로 트리거되기 때문에, `@Bean`으로 리스너를 등록 할 수 없습니다. `SpringApplication.addListeners(...)` 메소드 또는 `SpringApplicationBuilder.listeners(...)` 메소드로 등록 할 수 있습니다.
  응용프로그램의 작성 방법에 관계없이 리스너를 자동으로 등록하려면 `META-INF/spring.factories` 파일을 프로젝트에 추가하고 다음 예제와 같이 `org.springframework.context.ApplicationListener` key로 리스너를 참조 할 수 있습니다. 

  예 : `org.springframework.context.ApplicationListener=com.example.project.MyListener`

application event는 application이 실행될 때 다음 순서로 전송됩니다 : 

1. `ApplicationStartingEvent`는 실행이 시작될 때 listeners 및 initializer 등록을 제외하고 모든 처리전에 전송됩니다.
2. `ApplicationEnvironmentPreparedEvent`는 context에서 사용할 `Environment`를 알게될 때 하지만 context가 생성되기 전에 전송됩니다. 
3. `ApplicationPreparedEvent`는 refresh가 시작되기 직전에 하지만 bean 정의가 로드된 후 전송됩니다. 
4. `ApplicationStartedEvent`는 context가 refresh된 후에 하지만  모든 application과 command-line runner가 호출되기 전에 전송됩니다.
5. `ApplicationReadyEvent` 는 모든 application과 command-line runner가 호출된 후에 전송됩니다. Application이 요청을 처리할 준비가 되었음을 나타냅니다. 
6. `ApplicationFailedEvent`는 시작시 예외가 있을 때 전송됩니다. 

* ! Tip : 종종 응용프로그램 이벤트를 사용할 필요는 없지만, 응용프로그램 이벤트가 있다는 것을 아는 것이 편리 할 수 있습니다. 내부적으로 Spring Boot는 이벤트를 사용하여 다양한 작업을 처리합니다.

application event는 Spring Framework’s event publishing mechanism을 사용하여 전송됩니다. 이 메커니즘의 일부는 하위 컨텍스트의 리스너에 게시 된 이벤트가 조상 컨텍스트의 리스너에도 게시되도록합니다. 결과적으로 응용프로그램이 `SpringApplication` 인스턴스의 계층 구조를 사용하는 경우 listener는 동일한 유형의 application event의 여러 인스턴스를 수신 할 수 있습니다.

리스너가 컨텍스트에 대한 이벤트와 하위 컨텍스트에 대한 이벤트를 구별 할 수있게하려면 응용프로그램 컨텍스트가 주입되도록 요청한 다음 주입된 컨텍스트를 이벤트의 컨텍스트와 비교해야합니다. `ApplicationContextAware`를 구현하거나, listener가 Bean이면 `@Autowired`를 사용하여 컨텍스트를 주입 할 수 있습니다.

### 23.6 Web Environment

`SpringApplication`은 사용자를 대신하여 올바른 유형의 `ApplicationContext`를 생성하려고 시도합니다. `WebApplicationType`을 결정하는 데 사용되는 알고리즘은 매우 간단합니다 : 

- Spring MVC가 존재하면, `AnnotationConfigServletWebServerApplicationContext`가 사용됩니다.
- Spring MVC가없고 Spring WebFlux가 있으면 `AnnotationConfigReactiveWebServerApplicationContext`가 사용됩니다.
- 그렇지 않으면 `AnnotationConfigApplicationContext`가 사용됩니다.

즉, 동일한 애플리케이션에서 Spring MVC와 Spring WebFlux의 새로운 `WebClient`를 사용하는 경우 Spring MVC가 기본적으로 사용됩니다. `setWebApplicationType(WebApplicationType)`을 호출해서 쉽게 재정의 할 수 있습니다.

`setApplicationContextClass(...)`를 호출하여 사용되는 `ApplicationContext` type을 완벽하게 제어 할 수도 있습니다.

* ! Tip : JUnit 테스트에서 `SpringApplication`을 사용할 때 `setWebApplicationType (WebApplicationType.NONE)`을 호출하는 것이 종종 바람직하다.

### 23.7 Accessing Application Arguments 

`SpringApplication.run(...)`에 전달된 애플리케이션 인수에 액세스해야한다면 `org.springframework.boot.ApplicationArguments` bean을 주입 할 수있습니다. `ApplicationArguments` 인터페이스는 다음 예제와 같이 raw String [] 인수와 파싱된 `option` 및 `non-option` 인수 모두에 대한 액세스를 제공합니다 : 

```java
import org.springframework.boot.*;
import org.springframework.beans.factory.annotation.*;
import org.springframework.stereotype.*;

@Component
public class MyBean {

	@Autowired
	public MyBean(ApplicationArguments args) {
		boolean debug = args.containsOption("debug");
		List<String> files = args.getNonOptionArgs();
		// if run with "--debug logfile.txt" debug=true, files=["logfile.txt"]
	}

}
```

* ! Tip : Spring Boot는 또한 `CommandLinePropertySource`를 Spring `Environment`에 등록합니다. `@Value` annotation을 사용하여 단일 응용프로그램 인수를 주입할 수도 있습니다.

### 23.8 Using the ApplicationRunner or CommandLineRunner

`SpringApplication`이 시작된 후에 특정 코드를 실행해야한다면 `ApplicationRunner` 또는 `CommandLineRunner` 인터페이스를 구현할 수 있습니다. 두 인터페이스는 동일한 방식으로 작동하며 `SpringApplication.run(...)`이 완료되기 직전에 호출되는 단일 `run` 메소드를 제공합니다.

`CommandLineRunner` 인터페이스는 응용프로그램 인수에 대한 액세스를 간단한 문자열 배열로 제공하는 반면 `ApplicationRunner`는 앞에서 설명한 `ApplicationArguments` 인터페이스를 사용합니다. 다음 예제에서는 `run` 메서드가있는 `CommandLineRunner`를 보여줍니다 : 

```java
import org.springframework.boot.*;
import org.springframework.stereotype.*;

@Component
public class MyBean implements CommandLineRunner {

	public void run(String... args) {
		// Do something...
	}

}
```

특정 순서로 호출해야하는 여러 `CommandLineRunner` 또는 `ApplicationRunner` Bean이 정의된 경우에는 `org.springframework.core.Ordered` 인터페이스를 추가로 구현하거나 `org.springframework.core.annotation.Order` annotation을 사용할 수 있습니다.

### 23.9 Application Exit

각 `SpringApplication`은 종료시 `ApplicationContext`가 정상적으로 종료되도록 shutdown hook을 JVM에 등록합니다. 모든 표준 Spring lifecycle callback(예 : `DisposableBean` interface 또는 `@PreDestroy` annotation)을 사용할 수 있습니다.

또한 bean은 `SpringApplication.exit()`가 호출 될 때 특정 종료 코드를 반환하려는 경우 `org.springframework.boot.ExitCodeGenerator` 인터페이스를 구현할 수 있습니다. 이 종료 코드는 `System.exit()`에 전달되어 다음 예제와 같이 상태 코드로 반환 될 수 있습니다. : 

```java
@SpringBootApplication
public class ExitCodeApplication {

	@Bean
	public ExitCodeGenerator exitCodeGenerator() {
		return () -> 42;
	}

	public static void main(String[] args) {
		System.exit(SpringApplication
				.exit(SpringApplication.run(ExitCodeApplication.class, args)));
	}

}
```

또, Exception에 의해 `ExitCodeGenerator` interface가 구현 될 가능성이 있습니다. 이러한 예외가 발생하면 Spring Boot는 구현 된 `getExitCod ()` 메소드가 제공한 종료 코드를 리턴합니다.

### 23.10 Admin Features

`spring.application.admin.enabled` property를 지정하여 응용프로그램에 대한 admin-related feature을 활성화 할 수 있습니다. 플랫폼 `MBeanServer`에서 [`SpringApplicationAdminMXBean`](https://github.com/spring-projects/spring-boot/tree/v2.1.4.RELEASE/spring-boot-project/spring-boot/src/main/java/org/springframework/boot/admin/SpringApplicationAdminMXBean.java)을 노출합니다. 이 기능을 사용하여 Spring Boot application을 원격으로 관리 할 수 있습니다. 이 기능은 모든 서비스 래퍼 구현에 유용 할 수 있습니다.

* ! Tip : 응용프로그램이 실행중인 HTTP port를 알고 싶으면 `local.server.port` key를 사용하여 property을 가져옵니다.
* ! Caution : 이 기능을 활성화 할 때는 MBean이 응용프로그램을 종료하는 메소드를 공개하므로 주의하십시오.



## 24. 외부화된 구성 (Externalized Configuration)

Spring Boot는 구성을 외부화하여 다른 환경에서 동일한 응용프로그램 코드로 작업(work)할 수 있습니다. 구성을 외부화하기 위해 properties files, YAML files, environment variables, command-line arguments를 사용할 수 있습니다. Property값은 `@Value`annotation을 사용해서 bean에 직접 주입하거나, Spring의 `Environment` abstraction(추상화)를 통해 액세스하거나 또는`@ConfigurationProperties`를 통해 [구조화된 객체에 바인딩(bound to structured objects)](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#boot-features-external-config-typesafe-configuration-properties)할 수 있습니다.  

Spring Boot는 의미있는 재정의 값을 허용하도록 설계된 아주 특별한 `PropertySource` 순서를 사용합니다. Property는 다음 순서로 검토됩니다 : 

1. home directory의 [Devtools 전역 설정 속성(Devtools global settings properties)](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#using-boot-devtools-globalsettings) (devtools이 활성화 된 경우 `~/.spring-boot-devtools.properties`)
2. test의 [`@TestPropertySource`](https://docs.spring.io/spring/docs/5.1.6.RELEASE/javadoc-api/org/springframework/test/context/TestPropertySource.html) annotations 
3. test의 `properties` attribute.  [`@SpringBootTest`](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/api/org/springframework/boot/test/context/SpringBootTest.html) 및 [application의 특정  조각을 테스트하기 위한 test annotation](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#boot-features-testing-spring-boot-applications-testing-autoconfigured-tests)에서 사용할 수 있습니다.
4. Command line arguments
5.  `SPRING_APPLICATION_JSON`의 property (환경 변수 또는 시스템 속성에 포함된 inline JSON)
8. `ServletConfig` init parameters.
9. `ServletContext` init parameters.
8. `java:comp/env`의 JNDI attributes.
9. Java System properties (`System.getProperties()`).
10. OS 환경 변수 
11. `random.*`에만 속성이 있는 `RandomValuePropertySource`
12. 패키지된 jar의 외부에 있는 [Profile-specific application properties](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#boot-features-external-config-profile-specific-properties)  (`application-{profile}.properties` 와 YAML variants(변종)).
13. jar 내부의 패키지된 [Profile-specific application properties](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#boot-features-external-config-profile-specific-properties)  (`application-{profile}.properties` 와 YAML variants).
14. 패키지된 jar의 외부의 Application property (`application.properties` 와YAML variants)
15. jar 내부의 패키지된 Application propertiey (`application.properties` and YAML variants).
16. `@Configuration` class의 [`@PropertySource`](https://docs.spring.io/spring/docs/5.1.6.RELEASE/javadoc-api/org/springframework/context/annotation/PropertySource.html) annotation.
17. 기본 속성  (`SpringApplication.setDefaultProperties`를 설정하여 지정된).

구체적인 예제를 제공하기 위해 다음 예제와 같이 `name` 속성을 사용하는 `@Component`를 개발한다고 가정합니다 : 

```
import org.springframework.stereotype.*;
import org.springframework.beans.factory.annotation.*;

@Component
public class MyBean {

    @Value("${name}")
    private String name;

    // ...

}
```

응용프로그램 classpath(예 : jar 파일 내부)에서 `name`에 적합한 기본 속성 값을 제공하는 `application.properties` 파일을 가질 수 있습니다. 새로운 환경에서 실행될 때 `application.properties` 파일은 `name`을 override하는 jar 외부에서 제공될 수 있습니다.  일회성 테스트의 경우 특정  command line switch( 예:`java -jar app.jar --name="Spring"`)로 시작할 수 있습니다. 

* ! Tip : `SPRING_APPLICATION_JSON` 속성은 command line에 환경변수로 제공될 수 있습니다. 예를 들어, UN*X shell에서 아래 내용을 사용할 수 있습니다 
  :	`$ SPRING_APPLICATION_JSON='{"acme":{"name":"test"}}' java -jar myapp.jar`

  앞의 예제에서 Spring `Environment`의 `acme.name=test`로 끝납니다. 다음 예제와 같이 JSON을 `Spring.application.json`으로 System property에 제공할 수도 있습니다 : 
  :	`$ java -Dspring.application.json='{"name":"test"}' -jar myapp.jar`
  아래 예제처럼 command line 인자로 JSON을 제공할 수도 있습니다 : 
  :	`$ java -jar myapp.jar --spring.application.json='{"name":"test"}'`
  다음 예제와 같이 JNDI 변수로 JSON을 제공할 수도 있습니다 :  `java:comp/env/spring.application.json`

### 24.1 Configuring Random Values

`RandomValuePropertySource`는 랜덤 값(예:secret 또는 test case)를 주입하기에 유용합니다. 다음 예제와 같이 integers, longs, uuids, 또는 string을 생성 할 수 있습니다 : 

```
my.secret=${random.value}
my.number=${random.int}
my.bignumber=${random.long}
my.uuid=${random.uuid}
my.number.less.than.ten=${random.int(10)}
my.number.in.range=${random.int[1024,65536]}
```

`random.int*` 구문은  `OPEN value (,max) CLOSE` 이며 `OPEN,CLOSE` 는 문자이고 `value,max` 는 정수 입니다. `max`가 제공되면 `value`는 최소값이고 `max`는 최대값입니다.(배타적)

### 24.2 Accessing Command Line Properties

기본적으로 `SpringApplication`은 (`--server.port = 9000`처럼 `--`로 시작하는)모든 command line option argument를 `property`로 변환하고 이를 Spring `Environment`에 추가합니다. 앞서 언급했듯이 command line property는 항상 다른 property source보다 우선합니다.

command line property를 `Environment`에 추가하지 않으려면, `SpringApplication.setAddCommandLineProperties(false)`를 사용해서 비활성화 할 수 있습니다. 

### 24.3 Application Property Files

`SpringApplication`은 아래 위치의 `application.properties` file에서 속성을 로드하고 Spring `Environment`에 추가합니다.

1. A `/config` subdirectory of the current directory
2. The current directory
3. A classpath `/config` package
4. The classpath root

목록은 우선 순위에 따라 정렬됩니다 (목록의 상위에 정의된 속성은 하위에 정의된 속성보다 우선합니다).

* ! Note :  '.properties'대신 [YAML ('.yml') 파일을 사용](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#boot-features-external-config-yaml)할 수도 있습니다.

`application.properties`가 구성 파일명으로 마음에 들지 않으면, `spring.config.name` environment property를 지정하여 다른 파일명으로 전환 할 수 있습니다. `spring.config.location` environment property(쉼표로 구분된 디렉토리 위치 또는 파일 경로 목록)를 사용하여 명시적 위치를 참조할 수도 있습니다. 다음 예제에서는 다른 파일 이름을 지정하는 방법을 보여줍니다 :

```
$ java -jar myproject.jar --spring.config.name=myproject
```

다음 예제에서는 두개의 위치를 지정하는 방법을 보여줍니다 : 

```
$ java -jar myproject.jar --spring.config.location=classpath:/default.properties,classpath:/override.properties
```

* ! Warning : `spring.config.name` 및 `spring.config.location`은 로드해야할 파일을 결정하기 위해 매우 일찍 사용되므로 environment property로 정의되야 합니다(일반적으로 OS environment variable, a system property, 또는 command-line argument). 

`spring.config.location`가 (파일이 아닌)디렉토리에 있으면 `/`에서 끝나야합니다 (그리고, 런타임에, profile-specific file name을 포함해서 로드되기전에  `spring.config.name`에서 생성된 이름을 추가해야 합니다.) `spring.config.location`에 지정된 파일은 profile-specific variant를 지원하지 않는 그대로 사용되며 모든 profile-specific property로 override됩니다.

구성 위치는 역순으로 검색됩니다. 기본적으로 구성된 위치는 `classpath:/,classpath:/config/,file:./,file:./config/`입니다. 결과 검색 순서는 다음과 같습니다: 

1. `file:./config/`
2. `file:./`
3. `classpath:/config/`
4. `classpath:/`

`spring.config.location`을 사용하여 custom config location을 구성하면 기본 위치가 바뀝니다. 예를 들어, `spring.config.location`이 `classpath:/custom-config/,file:./custom-config/`값으로 구성되면 검색 순서는 다음과 같습니다 : 

1. `file:./custom-config/`
2. `classpath:custom-config/`

또는, `spring.config.additional-location`을 사용하여 custom config location을 구성하면 기본 위치에 추가되어 사용됩니다. 추가 위치는 기본 위치보다 먼저 검색됩니다. 예를 들어, `classpath:/custom-config/,file:./custom-config/` 의 추가 위치가 구성된 경우 검색 순서는 다음과 같습니다 : 

1. `file:./custom-config/`
2. `classpath:custom-config/`
3. `file:./config/`
4. `file:./`
5. `classpath:/config/`
6. `classpath:/`

이 검색 순서 지정을 사용하면 하나의 configuration file에 기본값을 지정한 다음 다른 값으로 선택적으로 override할 수 있습니다.기본 위치 중 하나에 있는 `application.properties` (또는 `spring.config.name`으로 선택한 다른 기본 이름)에서 application의 기본 값을 제공할 수 있습니다. 이러한 기본값은 런타임에 사용자 정의 위치 중 하나에있는 다른 파일로 override될 수 있습니다.

* ! Note : 시스템 속성 대신 환경 변수를 사용하면, 대부분의 운영 체제에서 마침표로 구분된 키(period-separated key) 이름을 사용할 수 없지만 대신 밑줄을 사용할 수 있습니다(예 : `spring.config.name` 대신 `SPRING_CONFIG_NAME`).
* ! Note : 응용프로그램이 컨테이너에서 실행되는 경우 환경 변수 또는 시스템 속성 대신 JNDI 속성( `java:comp/env`) 또는 서블릿 컨텍스트 초기화 매개 변수를 사용할 수 있습니다. 

### 24.4 프로파일별 속성 (Profile-specific Properties)

`application.properties` 파일뿐만 아니라 `application-{profile}.properties`와 같은 이름 지정 규칙을 사용하여 프로파일별 속성을 정의 할 수도 있습니다. `Environment` 에는 활성화된 프로파일이 설정되지 않은 경우 사용되는 default profile (`[default]`) 설정이 있습니다. 즉, 명시적으로 활성화 된 프로파일이 없으면 `application-default.properties`의 속성이 로드됩니다.

프로파일별 속성은 표준 `application.properties`와 동일한 위치에서로드되며, 프로파일별 파일이 패키지된 jar의 내부 또는 외부에 있는지에 관계없이 프로파일별 파일은 항상 특정되지 않은 파일을 override합니다. 

여러 개의 프로필이 지정되면 last-wins 전략이 적용됩니다. 예를 들어, `spring.profiles.active` 속성으로 지정된 프로파일은 `SpringApplication` API를 통해 구성된 프로파일 다음에 추가되므로 우선 적용됩니다.

* ! Note : `spring.config.location`에 파일을 지정한 경우, 해당 파일의 프로파일별 변형은 고려되지 않습니다. 프로필별 속성을 사용하려면 `spring.config.location`의 디렉토리를 사용하십시오.

### 24.5 Placeholders in Properties

`application.properties`의 값은 사용될 때 기존 `Environment`를 통해 필터링되므로 앞서 정의된 값(예 : 시스템 속성)을 다시 참조 할 수 있습니다.

```
app.name=MyApp
app.description=${app.name} is a Spring Boot application
```

* ! Tip : 이 기술을 사용하여 기존의 Spring Boot 속성의 "짧은"변형을 만들수도 있습니다. 자세한 내용은 *Section 77.4, “Use ‘Short’ Command Line Arguments”*을 참조하십시오.

### 24.6 Encrypting Properties

Spring Boot는 속성값 암호화를 위한 빌트인은 지원되지 않지만, Spring `Environment`에 포함된 값을 수정하는 데 필요한 hook point를 제공합니다. `EnvironmentPostProcessor` 인터페이스를 사용하면 응용프로그램이 시작되기 전에 `Environment`을 조작할 수 있습니다. 자세한 내용은  [Section 76.3, “Customize the Environment or ApplicationContext Before It Starts”](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#howto-customize-the-environment-or-application-context)를 참조하십시오.

자격 증명과 패스워드를 안전하게 저장하는 방법을 찾고 있다면, [Spring Cloud Vault](https://cloud.spring.io/spring-cloud-vault/)프로젝트는 [HashiCorp Vault](https://www.vaultproject.io/)에 외부화된 설정을 저장하는 것을 지원한다.

### 24.7 Using YAML Instead of Properties

[YAML](https://yaml.org/)은 JSON의 상위 집합이며, 따라서 계층적 구성 데이터를 지정하는데 편리한 형식입니다. `SpringApplication` class는 classpath에 [SnakeYAML](https://bitbucket.org/asomov/snakeyaml) 라이브러리가있으면 YAML을 속성의 대안으로 자동 지원합니다.

* ! Tip : "Starters"를 사용하면 SnakeYAML은 `spring-boot-starter`에 의해 자동으로 제공됩니다.

#### 24.7.1 Loading YAML

Spring Framework는 YAML 문서를 로드하는데 사용할 수 있는 두개의 편리한 클래스를 제공합니다. `YamlPropertiesFactoryBean`은 YAML을 `properties`로 로드하고 `YamlMapFactoryBean`은 YAML을 `Map`으로 로드합니다.

예를 들어 다음 YAML 문서를 생각해보십시오 : 

```yaml
environments:
	dev:
		url: https://dev.example.com
		name: Developer Setup
	prod:
		url: https://another.example.com
		name: My Cool App
```

앞의 예는 다음 속성으로 변환됩니다 : 

```
environments.dev.url=https://dev.example.com
environments.dev.name=Developer Setup
environments.prod.url=https://another.example.com
environments.prod.name=My Cool App
```

YAML list는 `[index]` dereferencers(역 참조 부호)와 함께 property key로 표현됩니다. 예를 들어, 다음 YAML을 보십시오 : 

```yaml
my:
servers:
	- dev.example.com
	- another.example.com
```

앞의 예는 다음 속성으로 변환됩니다 : 

```
my.servers[0]=dev.example.com
my.servers[1]=another.example.com
```

Spring Boot의 `Binder` utility(`@ConfigurationProperties`의 기능)를 사용하여 이와 같은 속성에 바인딩하려면 
`java.util.List` (또는 `Set`) 유형의 target bean에 property가 있어야하며 setter를 제공하거나 변경 가능한 값으로 초기화해야합니다.예를 들어 다음 예제는 앞에 보여진 속성에 바인딩합니다 : 

```java
@ConfigurationProperties(prefix="my")
public class Config {

	private List<String> servers = new ArrayList<String>();

	public List<String> getServers() {
		return this.servers;
	}
}
```

#### 24.7.2 Spring 환경에서 YAML을 속성으로 노출시키기 (Exposing YAML as Properties in the Spring Environment)

`YamlPropertySourceLoader` 클래스는 Spring `Environment`에서 YAML을 `PropertySource`로 노출시킬수 있습니다. 이렇게하면 자리표시자(placeholder)구문과 함께 `@Value` annotation을 사용하여 YAML 속성에 접근할 수 있습니다.

#### 24.7.3 Multi-profile YAML Documents

다음 예제와 같이, `spring.profiles` key를 사용하여 document가 적용되는 시기를 가리키는 하나의 파일에 다중 프로파일별 YAML document를 지정할 수 있습니다 : 

```yaml
server:
	address: 192.168.1.100
---
spring:
	profiles: development
server:
	address: 127.0.0.1
---
spring:
	profiles: production & eu-central
server:
	address: 192.168.1.120
```

앞의 예에서, `development` profile이 활성화 된 경우 `server.address` 속성은 `127.0.0.1`입니다. 마찬가지로 `production` 과 `eu-central`  프로필이 활성화 된 경우 `server.address` 속성은 `192.168.1.120`입니다.  `development`, `production` 과`eu-central` profile이 활성화되지 않은 경우 속성 값은 `192.168.1.100`입니다.

* ! Note : 따라서 `spring.profiles`은 간단한 프로파일명(예 : `production`) 또는 프로파일 표현식을 포함 할 수 있습니다. 프로파일 표현식은 보다 복잡한 프로파일 로직을 표현할 수있게합니다 (예 : `production & (eu-central | eu-west`). 자세한 내용은 [reference guide](https://docs.spring.io/spring/docs/5.1.6.RELEASE/spring-framework-reference/core.html#beans-definition-profiles-java)를 확인하십시오.

응용프로그램 컨텍스트가 시작될 때 명시적으로 활성화된 것이 없으면, 기본 프로파일이 활성화됩니다. 따라서 다음 YAML에서는, "default"프로파일에서만 사용할 수있는`spring.security.user.password`값을 설정합니다 : 

```
server:
  port: 8000
---
spring:
  profiles: default
  security:
    user:
      password: weak
```

다음 예제에서 password는 어떤 프로파일에도 연결되지 않았기 때문에 항상 설정되며, 필요에 따라 다른 모든 프로파일에서 명시적으로 재설정해야합니다.

```
server:
  port: 8000
spring:
  security:
    user:
      password: weak
```

`spring.profiles` element를 사용하여 지정된 스프링 프로파일은 `!` 문자를 사용하여 선택적으로 무효화될 수 있습니다. 무효화된 프로파일과 무효화되지 않은 프로파일이 하나의 문서에 지정되어있으면, 적어도 하나 이상의 무효화되지 않은 프로파일이 매치되어야 하며, 무효화된 프로파일이 일치하지 않아야 합니다. 

#### 24.7.4 YAML 결점(Shortcomings)

`@PropertySource` annotation을 사용하여 YAML 파일을 로드 할 수 없습니다. `@PropertySource` annotation을 사용하여 값을 로드해야 하는 경우 properties file을 사용해야합니다.

프로파일별 YAML 파일에서 다중 YAML document 구문을 사용하면 예기치 않은 동작이 발생할 수 있습니다. 예를 들어, `dev` profile이 활성화 상태인 `application-dev.yml`파일에서 다음 구성을 고려하십시오.

For example, consider the following config in a file called `application-dev.yml`, with the `dev` profile being active:

```yaml
server:
  port: 8000
---
spring:
  profiles: !test
  security:
    user:
      password: weak
```

위의 예에서 프로파일 무시 및 프로파일 표현식은 예상대로 작동하지 않습니다. 프로파일별 YAML 파일과 여러 YAML document를 결합하지 말고 하나만 사용하는 것이 좋습니다.

### 24.8 Type-safe Configuration Properties

`@Value("${property}")` annotation을 사용하여 configuration properties를 주입하는 것은 때로는 번거로울 수 있습니다, 여러 속성으로 작업하거나 데이터가 본질적으로 계층적일 경우 특히 그렇습니다. Spring Boot는 다음 예제와 같이 강하게 유형이 지정된 bean이 응용프로그램의 구성을 관리하고 유효하게하는 속성을 다루는 다른 방법을 제공합니다 : 

```
package com.example;

import java.net.InetAddress;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

import org.springframework.boot.context.properties.ConfigurationProperties;

@ConfigurationProperties("acme")
public class AcmeProperties {

	private boolean enabled;

	private InetAddress remoteAddress;

	private final Security security = new Security();

	public boolean isEnabled() { ... }

	public void setEnabled(boolean enabled) { ... }

	public InetAddress getRemoteAddress() { ... }

	public void setRemoteAddress(InetAddress remoteAddress) { ... }

	public Security getSecurity() { ... }

	public static class Security {

		private String username;

		private String password;

		private List<String> roles = new ArrayList<>(Collections.singleton("USER"));

		public String getUsername() { ... }

		public void setUsername(String username) { ... }

		public String getPassword() { ... }

		public void setPassword(String password) { ... }

		public List<String> getRoles() { ... }

		public void setRoles(List<String> roles) { ... }

	}
}
```

*  위의 POJO는 다음 속성을 정의합니다 : 
  * `acme.enabled` (기본값 : `false`)
  * `acme.remote-address` (`String`으로 부터 강제할 수 있는 타입(??))
  * `acme.security.username` (property 이름으로 이름이 결정되는 중첩된 "security" 객체와 함께 사용. 특히 return 타입은 전혀 사용되지 않고 `SecurityProperties`가 될 수 있습니다. 
  * `acme.security.password`.
  * `acme.security.roles` (`String` collection).

* ! Note : 
  getter와 setter는 Spring MVC와 마찬가지로 표준 Java Beans property descriptor를 통해 바인딩되기 때문에 보통은 필수입니다. 다음과 같은 경우에는 setter를 생략 할 수 있습니다 : 

  - Map이 초기화되면 binder에 의해 변경될 수 있기 때문에 getter는 필요하지만 setter는 필요하지 않습니다. 
  - 컬렉션과 배열은 인덱스(일반적으로 YAML) 또는 쉼표로 구분 된 단일 값(속성)을 사용하여 액세스 할 수 있습니다. 후자의 경우 setter는 필수입니다. 항상 이러한 유형의 setter를 추가하는 것이 좋습니다. 콜렉션을 초기화하는 경우, 콜렉션이 불변이 아닌지 확인하십시오 (앞의 예와 같이).
  - 앞의 예제의 `Security` 필드와 같이 중첩된 POJO 속성이 초기화되면, setter가 필요하지 않습니다. 바인더가 기본 생성자를 사용하여 즉석에서 인스턴스를 만들려면 setter가 필요합니다. 

  어떤 사람들은 getter와 setter를 자동으로 추가하기위해 Project Lombok을 사용합니다. Lombok이 컨테이너에서 자동으로 개체를 인스턴스화 할 때 사용되는 특정 형식의 생성자를 생성하지 않았는지 확인해야합니다.

  마지막으로, 표준 Java Bean 속성만 고려되며 정적 속성에 대한 바인딩은 지원되지 않습니다.

* ! Tip : [`@Value`와 `@ConfigurationProperties`의 차이점](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#boot-features-external-config-vs-value)을 참조하십시오.

또한 다음 예제와 같이 `@EnableConfigurationProperties` annotation에 등록할 속성 클래스들을 나열해야합니다 : 

```java
@Configuration
@EnableConfigurationProperties(AcmeProperties.class)
public class MyConfiguration {
}
```

* ! Note : 그런 식으로 `@ConfigurationProperties` bean이 등록되면 bean은 <prefix>-<fqn>형식의 전통적인 이름을 갖습니다. 여기서 <prefix>는 `@ConfigurationProperties` annotation에 지정된 environment key 접두사이고 <fqn>은  bean의 fully qualified name 입니다. annotation이 prefix를 제공하지 않으면 bean의 fully qualified name이 사용됩니다. 위 예에서 bean 이름은 `acme-com.example.AcmeProperties`입니다. 

앞의 구성은 `AcmeProperties`에 대한 일반 bean을 생성합니다. `@ConfigurationProperties`은 environment 만 처리하고, 특히 context에서 다른 bean을 주입하지 않는 것이 좋습니다. 

`@EnableConfigurationProperties` annotation은 자동으로 프로젝트에 적용되기 때문에  `@ConfigurationProperties` annotation 처리된 기존 bean이 `Environment`에서 구성되도록 하십시오. 

`@EnableConfigurationProperties(AcmeProperties.class)`로 `MyConfiguration`을 처리하는 대신에, 아래 예와 같이 `AcmeProperties`를 bean으로 만들 수 있습니다 : 

```java
@Component
@ConfigurationProperties(prefix="acme")
public class AcmeProperties {

	// ... see the preceding example

}
```

이 구성 스타일은 `SpringApplication` 외부 YAML configuration에서 특히 잘 작동합니다 (다음 예제 참조) : 

```yaml
# application.yml

acme:
	remote-address: 192.168.1.1
	security:
		username: admin
		roles:
		  - USER
		  - ADMIN

# additional configuration as required
```

`@ConfigurationProperties` bean을 사용하여 작업하려면, 다음 예제와 같이 다른 bean과 동일한 방법으로 이를 주입 할 수 있습니다 : 

```java
@Service
public class MyService {

	private final AcmeProperties properties;

	@Autowired
	public MyService(AcmeProperties properties) {
	    this.properties = properties;
	}

 	//...

	@PostConstruct
	public void openConnection() {
		Server server = new Server(this.properties.getRemoteAddress());
		// ...
	}

}
```

* ! Tip : `@ConfigurationProperties`를 사용하면 IDE가 자신의 키에 대한 자동 완성을 제공하는 데 사용할 수있는 메타 데이터 파일을 생성할 수 있습니다. 자세한 내용은 [Appendix B, *Configuration Metadata*](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#configuration-metadata) 부록을 참조하십시오.

#### 24.8.1 Third-party Configuration

`@ConfigurationProperties`를 사용하여 클래스에 주석을 추가하는 것은 물론 public `@Bean` 메소드에서도 사용할 수 있습니다. 이렇게하면 컨트롤 외부에있는 third-party 구성 요소에 속성을 바인딩하려는 경우 특히 유용 할 수 있습니다. 

 `Environment` property에서 bean을 구성하려면, 다음 예와 같이 bean 등록에 `@ConfigurationProperties`를 추가하십시오 : 

```java
@ConfigurationProperties(prefix = "another")
@Bean
public AnotherComponent anotherComponent() {
	...
}
```

`another` 접두어로 정의된 속성은 앞선 `AcmeProperties` 예제와 유사한 방식으로 해당 `AnotherComponent` bean에 매핑됩니다.

#### 24.8.2 느슨한 바인딩 (Relaxed Binding)

Spring Boot는 `Environment` 속성을 `@ConfigurationProperties` bean에 바인딩하기위한 다소 완화된 규칙을 사용하므로 `Environment` 속성명과 bean 속성명이 정확히 일치할 필요는 없습니다. 이것이 유용한 일반적인 예는 대시(-)로 구분된 환경 속성(예 : `contextPath`가 `contextPath`에 바인드) 및 대문자로된 환경 속성(예 : `PORT`가 `port`에 바인드)이 있는것입니다. 

예를 들어, 다음과 같은 `@ConfigurationProperties` 클래스를 보십시오 : 

```java
@ConfigurationProperties(prefix="acme.my-project.person")
public class OwnerProperties {

	private String firstName;

	public String getFirstName() {
		return this.firstName;
	}

	public void setFirstName(String firstName) {
		this.firstName = firstName;
	}

}
```

앞의 예제에서 다음 속성명을 모두 사용할 수 있습니다 : 

**Table 24.1. relaxed binding**

| Property                            | Note                                                         |
| ----------------------------------- | ------------------------------------------------------------ |
| `acme.my-project.person.first-name` | Kebab case(-), `.properties` 와 `.yml` file에서 사용하는 것이 좋습니다. |
| `acme.myProject.person.firstName`   | 표준 camel case 구문.                                        |
| `acme.my_project.person.first_name` | Underscore(_) 표기법, `.properties` and `.yml` file에서 사용하기 위한 대체 형식입니다. |
| `ACME_MYPROJECT_PERSON_FIRSTNAME`   | 대문자 형식,환경 변수에 사용될 때 권장됩니다.                |

* ! Note : 어노테이션의 접두사 값은 kebab case여야 합니다 (소문자이고 `-`로 구분, 예 : `acme.my-project.person`).

**Table 24.2. relaxed binding rules per property source**

| Property Source       | Simple                                                       | List                                                         |
| --------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Properties Files      | Camel case, kebab case, or underscore notation               | `[ ]` 또는 쉼표로 구분된 값을 사용하는 표준 list 구문        |
| YAML Files            | Camel case, kebab case, or underscore notation               | 표준 YAML list 구문 또는 쉼표로 구분 된 값                   |
| Environment Variables | 분리 문자로 밑줄이있는 대문자 형식입니다. 속성명에 `_`를 사용하면 안됩니다. | `MY_ACME_1_OTHER = my.acme[1].other`처럼 밑줄로 묶인 숫자 값. |
| System properties     | Camel case, kebab case, or underscore notation               | `[ ]` 또는 쉼표로 구분된 값을 사용하는 표준 list 구문        |

* ! Tip : 가능한 경우 속성은 `my.property-name = acme`와 같이 소문자 kebab 형식으로 저장하는 것이 좋습니다.

`Map` property에 바인딩 할 때 `key`에 소문자 영숫자 또는`-` 이외의 문자가 포함되어 있으면 원래 값이 유지되도록 대괄호 표기법을 사용해야합니다. 키가 `[]`로 둘러싸여 있지 않으면 영문/숫자가 아니거나 `-`인 문자는 모두 제거됩니다. 예를 들어 다음 속성을 `Map`에 바인딩하는 것을 보십시오 : 

```
acme:
  map:
    "[/key1]": value1
    "[/key2]": value2
    /key3: value3
```

위 속성은  `/key1`, `/key2`와 `key3`를 map의 key로 바인딩합니다. 

#### 24.8.3 복합 유형 병합 (Merging Complex Types)

목록이 한 곳 이상에서 구성된 경우 전체 목록을 대체하여 재정의됩니다.

예를 들어, 기본적으로 `null` 인 `name` 및 `description` attribute를 가진 `MyPojo` 객체를 가정합니다. 다음 예제에서는 `AcmeProperties`에서 `MyPojo` 객체의 list를 노출합니다.

```java
@ConfigurationProperties("acme")
public class AcmeProperties {

	private final List<MyPojo> list = new ArrayList<>();

	public List<MyPojo> getList() {
		return this.list;
	}

}
```

다음 구성을 고려하십시오 :

```yaml
acme:
  list:
    - name: my name
      description: my description
---
spring:
  profiles: dev
acme:
  list:
    - name: my another name
```

`dev` 프로파일이 활성화되어 있지 않으면 `AcmeProperties.list`에 앞서 정의 된대로 하나의 `MyPojo` 항목이 있습니다. 그러나 `dev` 프로파일이 활성화된 경우 `list`에는 여전히 하나의 항목(`my another name`의 name과 `null`의 description)만 포함됩니다. 이 구성은 두 번째 `MyPojo` 인스턴스를 목록에 추가하지 *않으며* 항목을 병합하지 않습니다.

`List`가 여러 프로파일에 지정된 경우, 우선 순위가 가장 높은 프로파일만 사용됩니다. 다음 예제를 보십시오 : 

```yaml
acme:
  list:
    - name: my name
      description: my description
    - name: another name
      description: another description
---
spring:
  profiles: dev
acme:
  list:
    - name: my another name
```

앞의 예에서, `dev` 프로파일이이 활성화되어 있으면 `AcmeProperties.list`에 (name이 `my another name`이고 description이 `null`인)`MyPojo` 항목이 하나 있습니다 . YAML의 경우 쉼표로 구분된 목록과 YAML 목록을 모두 사용하여 목록의 내용을 완전히 override할 수 있습니다.

`Map` property의 경우 여러 소스에서 가져온 속성 값으로 바인딩 할 수 있습니다. 그러나 여러 소스에 동일한 속성이 있는 경우 우선 순위가 가장 높은 속성이 사용됩니다. 다음 예제에서는 `AcmeProperties`에서 `Map<String, MyPojo>`를 노출합니다 : 

```java
@ConfigurationProperties("acme")
public class AcmeProperties {

	private final Map<String, MyPojo> map = new HashMap<>();

	public Map<String, MyPojo> getMap() {
		return this.map;
	}

}
```

다음 구성을 보십시오 : 

```yaml
acme:
  map:
    key1:
      name: my name 1
      description: my description 1
---
spring:
  profiles: dev
acme:
  map:
    key1:
      name: dev name 1
    key2:
      name: dev name 2
      description: dev description 2
```

`dev` 프로파일이 활성화되어 있지 않으면, `AcmeProperties.map`에 (name이 `my name 1`, description이 `my description 1`인)`key1` key가 있는 항목이 하나 있습니다. 그러나 `dev` 프로파일을 사용하는 경우 `map`에는 (name이 `dev name 1`, description이 `my description 1`인)`key1`과 (name이 `dev name 2`이고 description이 `dev description 2`인)`key2` key인 두개의 항목이 있습니다. 

* ! Note : 위의 병합 규칙은 YAML 파일뿐만 아니라 모든 property source의 property에도 적용됩니다.

#### 24.8.4 Properties Conversion

Spring Boot는 `@ConfigurationProperties` bean에 바인딩할 때 외부 애플리케이션 속성을 올바른 유형으로 강제 변환하려고 시도합니다. 사용자 정의 type 변환이 필요한 경우 `ConversionService` bean (`conversionService`라는 bean) 또는 사용자 정의 property editor (`CustomEditorConfigurer` bean을 통해) 또는 사용자 정의 `Converters` (`@ConfigurationPropertiesBinding`으로 정의된 bean)를 제공 할 수 있습니다.

* ! Note : 이 bean은 application lifecycle 중 매우 일찍 요청되므로`ConversionService`가 사용하는 의존성을 제한해야합니다. 일반적으로 필요한 종속성은 생성시 완전히 초기화되지 않을 수 있습니다. configuration key 강제 변환이 필요하지 않은 경우 사용자 지정 `ConversionService`의 이름을 바꾸고 `@ConfigurationPropertiesBinding`으로 정규화 된 사용자 지정 converter에만 의존 할 수 있습니다.

##### 기간 변환(Converting durations)

Spring Boot는 지속 시간 표현을 위한 전용 지원을 제공합니다. `java.time.Duration` property를 노출하면 응용 프로그램 속성에서 다음 형식을 사용할 수 있습니다: 

- `long`표현 (`@DurationUnit`이 지정되지 않는 한 기본 단위로 밀리 초가 사용)
- [`java.util.Duration`로 사용되는](https://docs.oracle.com/javase/8/docs/api//java/time/Duration.html#parse-java.lang.CharSequence-) 표준 ISO-8601 형식
- 값과 단위가 결합된 더 읽기 쉬운 형식 (예: `10s`는 10초를 의미)

다음 예제를 보십시오 : 

```java
@ConfigurationProperties("app.system")
public class AppSystemProperties {

	@DurationUnit(ChronoUnit.SECONDS)
	private Duration sessionTimeout = Duration.ofSeconds(30);

	private Duration readTimeout = Duration.ofMillis(1000);

	public Duration getSessionTimeout() {
		return this.sessionTimeout;
	}

	public void setSessionTimeout(Duration sessionTimeout) {
		this.sessionTimeout = sessionTimeout;
	}

	public Duration getReadTimeout() {
		return this.readTimeout;
	}

	public void setReadTimeout(Duration readTimeout) {
		this.readTimeout = readTimeout;
	}

}
```

session timeout을 30초로 지정하는 건 `30`, `PT30S` 및 `30s` 모두 동일합니다. 500ms의 read timeout은 `500`, `PT0.5S` 및 `500ms` 중 하나로 지정할 수 있습니다.

지원되는 단위를 사용할 수도 있습니다. 지원되는 단위 : 

- `ns` for nanoseconds
- `us` for microseconds
- `ms` for milliseconds
- `s` for seconds
- `m` for minutes
- `h` for hours
- `d` for days

기본 단위는 milliseconds 이고 위 샘플에 보여지는 것 처럼 `@DurationUnit`을 사용하여 재정의할 수 있습니다.

* ! Tip : 기간을 표현하기 위해 단순히 `Long`을 사용하는 이전 버전에서 업그레이드하는 경우 `Duration`전환시 milliseconds가 아니라면 (`@DurationUnit`을 사용해서) 단위를 정의하십시오.  이렇게하면 훨씬 더 풍부한 형식을 지원하면서 투명한 업그레이드 경로가 제공됩니다.

##### 데이터 크기 변환 (Converting Data Sizes)

Spring Framework는 크기를 바이트 단위로 표현할 수있는 `DataSize` 값타입을 가지고 있습니다. `DataSize` property를 노출하면 응용프로그램 속성에서 다음 형식을 사용할 수 있습니다 : 

- `long` 표현 (`@DataSizeUnit`가 지정되지 않는 한 byte를 기본 단위로 사용)
- 값과 단위가 결합 된 더 읽기 쉬운 형식 (예 : `10MB`는 10 megabytes를 의미)

다음 예제를 보십시오 : 

```java
@ConfigurationProperties("app.io")
public class AppIoProperties {

	@DataSizeUnit(DataUnit.MEGABYTES)
	private DataSize bufferSize = DataSize.ofMegabytes(2);

	private DataSize sizeThreshold = DataSize.ofBytes(512);

	public DataSize getBufferSize() {
		return this.bufferSize;
	}

	public void setBufferSize(DataSize bufferSize) {
		this.bufferSize = bufferSize;
	}

	public DataSize getSizeThreshold() {
		return this.sizeThreshold;
	}

	public void setSizeThreshold(DataSize sizeThreshold) {
		this.sizeThreshold = sizeThreshold;
	}

}
```

buffer size를 10 megabytes로 지정하려면, `10`과 `10MB`는 동일합니다. 256 byte의 크기 임계 값은 `256` 또는 `256B`로 지정할 수 있습니다.

지원되는 단위를 사용할 수도 있습니다. 지원되는 단위 : 

- `B` for bytes
- `KB` for kilobytes
- `MB` for megabytes
- `GB` for gigabytes
- `TB` for terabytes

기본 단위는 byte이며 위 샘플에서 설명한대로 `@DataSizeUnit`을 사용하여 재정의 할 수 있습니다.

* ! Tip : size를 표현하기 위해 단순히 `Long`을 사용하는 이전 버전에서 업그레이드하는 경우 `DataSize`전환시 bytes가 아니라면 (`@DataSizeUnit`을 사용해서) 단위를 정의하십시오.  이렇게하면 훨씬 더 풍부한 형식을 지원하면서 투명한 업그레이드 경로가 제공됩니다.

#### 24.8.5 @ConfigurationProperties Validation

Spring Boot는 Spring의 `@Validated` annotation이 있으면 `@ConfigurationProperties` class의 유효성 검사를 시도합니다. configuration class에서 JSR-303 `javax.validation constraint annotation`을 직접 사용할 수 있습니다. 이렇게하려면 다음 예와 같이 호환되는 JSR-303 구현이 classpath에 있는지 확인한 다음 필드에 제약 조건 어노테이션을 추가하십시오.

```java
@ConfigurationProperties(prefix="acme")
@Validated
public class AcmeProperties {

	@NotNull
	private InetAddress remoteAddress;

	// ... getters and setters

}
```

* ! Tip : `@Validated`로 configuration property를 작성하는 `@Bean` 메소드에 애노테이션을 달아 유효성 검증을 트리거할 수도 있습니다.

중첩된 속성도 바인딩 될 때 유효성이 검사되지만 연결된 필드에 `@Valid`를 추가하는 것이 좋습니다. 이렇게하면 중첩된 속성이 없는 경우에도 유효성 검사가 트리거됩니다. 다음 예제는 앞의 `AcmeProperties` 예제를 기반으로합니다 : 

```java
@ConfigurationProperties(prefix="acme")
@Validated
public class AcmeProperties {

	@NotNull
	private InetAddress remoteAddress;

	@Valid
	private final Security security = new Security();

	// ... getters and setters

	public static class Security {

		@NotEmpty
		public String username;

		// ... getters and setters

	}

}
```

`configurationPropertiesValidator`라는 bean 정의를 생성하여 사용자정의 Spring `Validator`를 추가 할 수도 있습니다. `@Bean` 메서드는 `static`으로 선언되어야합니다. configuration properties validator는application’s lifecycle 초기에 생성되며 `@Bean` 메서드를 static으로 선언하면 `@Configuration` class를 인스턴스화하지 않고도 Bean을 생성할 수 있습니다. 이렇게하면 초기 인스턴스 생성으로 인해 발생할 수있는 문제를 피할 수 있습니다. 설정하는 방법을 보여주는  [property validation sample](https://github.com/spring-projects/spring-boot/tree/v2.1.4.RELEASE/spring-boot-samples/spring-boot-sample-property-validation)이 있습니다.

* ! Tip : `spring-boot-actuator` module은 모든 `@ConfigurationProperties` bean을 노출하는 endpoint를 포함합니다. 웹 브라우저에서 `/actuator/configprops`를 지정하거나 해당 JMX endpoint를 사용하십시오. 자세한 내용은 "[Production ready features](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#production-ready-endpoints)" 섹션을 참조하십시오.

#### 24.8.6 @ConfigurationProperties vs. @Value

`@Value` annotation은 핵심 컨테이너 기능이며 type-safe configuration property와 동일한 기능을 제공하지 않습니다. 다음 표는 `@ConfigurationProperties` 및 `@Value`가 지원하는 기능을 요약 한 것입니다 : 

| Feature                                                      | `@ConfigurationProperties` | `@Value` |
| ------------------------------------------------------------ | -------------------------- | -------- |
| [Relaxed binding](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#boot-features-external-config-relaxed-binding) | Yes                        | No       |
| [Meta-data support](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#configuration-metadata) | Yes                        | No       |
| `SpEL` evaluation                                            | No                         | Yes      |

componet에 configuration key 세트를 정의하는 경우, `@ConfigurationProperties`로 POJO를 그룹화하는 것이 좋습니다. 또한 `@Value`는 느슨한 바인딩을 지원하지 않으므로 환경 변수를 사용하여 값을 제공해야하는 경우에는 좋지 않습니다.

마지막으로 `@Value`에 `SpEL` 표현식을 쓸 수는 있지만 이러한 표현식은 [application property files](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#boot-features-external-config-application-property-files)에서 처리되지 않습니다.

## 25. Profiles

Spring Profile은 애플리케이션 구성의 일부를 분리하여 특정 환경에서만 사용할 수 있도록 하는 방법을 제공합니다. `@Component` 또는 `@Configuration`은 아래 예제와 같이 로드시 제한하도록 `@Profile`로 표시할 수 있습니다.

```java
@Configuration
@Profile("production")
public class ProductionConfiguration {

	// ...

}
```

`spring.profiles.active` `Environment` property를 사용하여 활성화할 프로파일을 지정할 수 있습니다. 이 장의 앞부분에서 설명한 방법으로 속성을 지정할 수 있습니다. 예를 들어, 다음 예와 같이 `application.properties`에 이것을 포함시킬 수 있습니다.

```
spring.profiles.active=dev,hsqldb
```

command line에서 `--spring.profiles.active=dev,hsqldb` switch를 사용하여 지정할 수도 있습니다. 

### 25.1 Adding Active Profiles

`spring.profiles.active` property는 다른 property들과 동일한 순서 규칙을 따릅니다. 가장 높은(highest) `PropertySource`가 우선합니다. 즉, `application.properties`에서 활성 프로파일을 지정한 다음 command line switch를 사용하여 **대체**할 수 있습니다. 

때로는 프로파일을 대체하는 대신 활성 프로파일을 추가하는 프로파일별 속성을 갖는 것이 유용합니다. `spring.profiles.include` property는 활성화 프로파일을 무조건적으로 추가하는 데 사용할 수 있습니다. 또한 `SpringApplication` entry point는 추가 프로파일을 설정(즉, `spring.profiles.active` property에 의해 활성화되는 프로파일 위에)하기 위한 Java API를 가집니다. [SpringApplication](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/api/org/springframework/boot/SpringApplication.html)의 `setAdditionalProfiles()` 메소드를 보십시오.

예를 들어, `--spring.profiles.active=prod` switch를 사용하여 아래 property가 있는 애플리케이션을 실행하면 `proddb` 및 `prodmq` 프로파일 또한 활성화됩니다. 

```
---
my.property: fromyamlfile
---
spring.profiles: prod
spring.profiles.include:
  - proddb
  - prodmq
```

* ! Note : `Spring.profiles` property는 특정 document가 configuration에 포함될 시기를 결정하기 위해 YAML document에 정의될 수 있습니다. 자세한 내용은 [Section 77.7, “Change Configuration Depending on the Environment”](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#howto-change-configuration-depending-on-the-environment)을 참조하십시오.

### 25.2 Programmatically Setting Profiles

애플리케이션이 실행되기 전에 `SpringApplication.setAdditionalProfiles(…)`를 호출하여 활성 프로파일을 프로그래밍 방식으로 설정할 수 있습니다. Spring의 `ConfigurableEnvironment` interface를 사용하여 프로파일을 활성화할 수도 있습니다.

### 25.3 프로파일별 구성 파일 (Profile-specific Configuration Files)

`application.properties` (또는 `application.yml`) 와 `@ConfigurationProperties`를 통해 참조된 파일의 프로파일별 변형은 파일로 간주되고 로드됩니다. 자세한 내용은 "[Section 24.4, “Profile-specific Properties”](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#boot-features-external-config-profile-specific-properties)"을 참조하십시오. 