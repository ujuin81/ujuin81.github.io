---
title: spring boot reference guide-Using Spring Boot
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


# Part III. 스프링 부트 사용하기 

... 앞 부분 생략 (차후 필요에 따라 추가할 수도 있음) ...  

## 14. Structuring Your Code

스프링 부트는 특정 코드 레이아웃을 요구하지 않습니다. 하지만 도움이되는 몇가지 모범 사례가 있습니다.

### 14.1 “default” Package 사용

package 선언이 없는 class 사용을 **피해야 합니다.** `@ComponentScan`, `@EntityScan` 또는 `@SpringBootApplication` 시에 모든 jar의 모든 class를 읽어들여 응용프로그램에 문제를 일으킬 수 있습니다. 

- !Tip :  `com.example.project` 처럼 Java 권장 패키지 이름 규칙을 사용을 권장합니다.



### 14.2 Main Application Class 위치 

main application class는 다른 클래스들 보다 상위에 있는 root package에 두는 것을 권장합니다. `@SpringBootApplication` annotation이 달린 class의 package를 사용하여 특정 항목(예를 들어 `@Entity`) 검색합니다. root package를 사용하면 구성 요소 검사를 프로젝트에만 적용할 수 있습니다. 
(+default pacakge 사용을 피하라는 이유와 동일한 내용) 

- !Tip : `@SpringBootApplication`사용을 원하지 않으면, `@EnableAutoConfiguration` , `@ComponentScan` annotations 을 대신 사용할 수 있습니다.

아래는 일반적인 레이아웃을 보여줍니다 : 

```
com
 +- example
     +- myapplication  
         +- Application.java (+root package:com.example.myapplication)
         |
         +- customer
         |   +- Customer.java
         |   +- CustomerController.java
         |   +- CustomerService.java
         |   +- CustomerRepository.java
         |
         +- order
             +- Order.java
             +- OrderController.java
             +- OrderService.java
             +- OrderRepository.java
```

`Application.java` 파일은 아래와 같이 `@SpringBootApplication`과 함께 `main` 함수가 정의되어있습니다. 

```java
package com.example.myapplication;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication //!!!
public class Application {

    public static void main(String[] args) { //!!!
        SpringApplication.run(Application.class, args);
    }

}
```



## 15. Configuration Classes

Spring Boot는 자바 기반 구성을 선호합니다. XML source와 함께`SpringApplication`를 사용할 수도 있지만, 주 소스는 단일 `@Configuration` clsss로 권장합니다. (Although it is possible to use `SpringApplication` with XML sources, we generally recommend that your primary source be a single `@Configuration` class.) 
일반적으로 `main` method가 정의된 class는 primary `@Configuration`으로서 좋은 후보입니다. 

- !Tip : XML 구성을 사용하는 많은 Spring 구성 예제가 인터넷에 게시되어있습니다. 가능하다면 항상 Java-based configuration을 사용해보십시오. `Enable*` annotations을 검색하는 것이 좋은 출발점이 될 수 있습니다.



### 15.1 추가 구성 클래스 가져오기 

모든 `@Configuration`를 단일 class에 넣을 필요는 없습니다. `@Import` annotation은 추가 구성 클래스를 가져올 수 있습니다. 또는 `@ComponentScan`을 사용하여 `@Configuration` 클래스를 포함해서 모든 Spring components를 자동으로 선택할 수 있습니다.



### 15.2 XML 구성 가져오기

XML 기반 구성을 사용해야만 하는 경우에도 `@Configuration` class로 시작하는 것이 좋습니다.  `@ImportResource`annotation을 사용하여 XML 구성 파일을 로드할 수 있습니다. 



## 16. 자동 구성 (Auto-configuration)

Spring Boot auto-configuration는 여러분이 추가한 jar 의존성을 기반으로 Spring application을 자동으로 설정하려고 시도합니다. 예를 들어,  `HSQLDB`가 classpath에 있고 database 연결 bean을 수동으로 설정하지 않았다면 Spring Boot는 내장(in-memory) database를 자동으로 구성합니다. 

`@EnableAutoConfiguration` 또는 `@SpringBootApplication` annotation을 `@Configuration` class 중 하나에 추가하여 자동 구성을 선택해야합니다.

- !Tip : `@SpringBootApplication`, `@EnableAutoConfiguration` annotation 중 오직 하나만 추가하십시오.  일반적으로 기본 `@Configuration` class에만 둘 중 하나를 추가하는 것이 좋습니다.



### 16.1 자동 구성 점차적으로 대체하기 (Gradually Replacing Auto-configuration)

언제든지, 자동 구성의 특정 부분을 대체하기 위해 자체 구성을 정의할 수 있습니다. 예를 들어 `DataSource` bean을 추가하면 기본 내장 database 지원은 사라집니다. 

현재 적용된 자동 구성이 무엇인지, 왜 적용됐는지 알아야 한다면 `--debug` switch로 application 시작하십시오. 이렇게 하면 core logger 중 일부에 대한 debug log가 활성화되고 console에 conditions report가 기록됩니다.



### 16.2 특정 Auto-configuration Class 비활성화 

원하지 않는 특정 Auto-configuration Class가 적용되는 경우 다음과 같이 `@EnableAutoConfiguration`의 `exclude` 속성을 사용하여 해당 Class를 비활성화 할 수 있습니다.

```
import org.springframework.boot.autoconfigure.*;
import org.springframework.boot.autoconfigure.jdbc.*;
import org.springframework.context.annotation.*;

@Configuration
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class}) 
public class MyConfiguration {
}
```

class가 classpath에 없으면 annotation의 `excludeName` attribute를 사용하고 fully qualified name을 지정할 수 있습니다. 마지막으로 `spring.autoconfigure.exclude` property를 사용해서 제외할 자동 구성 클래스 목록을 제어할 수도 있습니다. 

- !Tip : [Tip]annotation과 property를 사용하여 제외를 정의할 수 있습니다.



## 17. 스프링 빈과 의존성 주입

표준 Spring Framework 기술을 자유롭게 사용하여 bean과 의존성 주입을 정의할 수 있습니다.  (`@ComponentScan` (bean 찾기)을 사용하고 `@Autowired` (생성자 주입)를 사용)

위에서 제안한 대로 (root package에 application class가 위치)  코드를 짜면, 인수 없이 `@ComponentScan` 을 추가할 수 있습니다. application components (`@Component`, `@Service`, `@Repository`, `@Controller` etc.) 는 자동으로 Spring Bean으로 등록될 수 있습니다. 

```java
package com.example.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service //<--
public class DatabaseAccountService implements AccountService {

    //생성자 주입시 `final`을 사용하여 이후에 변경할 수 없음을 표시  
    private final RiskAssessor riskAssessor;

    //@Autowired //<-- 생성자가 1개면 @Autowired 생략 가능 
    public DatabaseAccountService(RiskAssessor riskAssessor) {
        this.riskAssessor = riskAssessor;
    }

    // ...

}
```



## 18. @SpringBootApplication Annotation 사용하기

Many Spring Boot developers like their apps to use auto-configuration, component scan and be able to define extra configuration on their "application class". 

단일`@SpringBootApplication` annotation은 다음 3가지 기능을 사용할 수 있다

- `@EnableAutoConfiguration`: Spring Boot의 자동 구성 매커니즘의 활성화 
- `@ComponentScan`: application에 있는 pacakage에서 `@Component` scan 활성화 
- `@Configuration`: 추가 bean을 등록하거나 추가 구성 class를 import 

다음 예제와 같이 `@SpringBootApplication` annotation은 `@Configuration`, `@EnableAutoConfiguration`, `@ComponentScan` 을 사용하는 것과 같습니다. 

```java
package com.example.myapplication;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication // same as @Configuration @EnableAutoConfiguration @ComponentScan
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```

```java
//참고 : @SpringBootApplication 정의
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = {
        @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
        @Filter(type = FilterType.CUSTOM,
                classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication { 
    //... 
} 
```

- !Tip : `@SpringBootApplication`는 또한 `@EnableAutoConfiguration`과 `@ComponentScan`의 속성을 사용자 정의하기 위한 별칭을 제공합니다.  

- !Tip : 이 기능 중 어떤 것도 필수 사항이 아니며 단일 annotation을 가능한 기능의 어떤 것으로도 대체하도록 선택할 수 있습니다. 예를 들어 application에서 component scan을 사용하지 않을 수 있습니다.

  ```java
  //package, import ... 
  
  @Configuration
  @EnableAutoConfiguration
  ////이 예제에서 Application은 @Component-annotated class가 자동으로 감지되지 않고 사용자 정의 Bean이 명시 적으로 import된다는 점을 제외하면 다른 Spring Boot 애플리케이션과 동일합니다
  @Import({ MyConfig.class, MyAnotherConfig.class })
  public class Application {
      
    public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
    }
  
  } 
  ```



## 19. 응용 프로그램 실행하기

application을 jar로 패키징하고 embedded HTTP server를 사용하는 가장 큰 장점 중 하나는 다른 방법과 마찬가지로 application을 실행할 수 있다는 것입니다. Spring Boot applications의 디버깅 또한 쉽습니다. 특별한 IDE plugins이나 확장이 필요 하지 않습니다. 

- !Tip : 이 절에서는 jar 기반 패키징에 대해서만 설명합니다. application을 war 파일로 패키지하는 경우 server 및 IDE 설명서를 참조해야합니다.



### 19.1 IDE에서 실행 

먼저 project를 import해야 합니다. (IDE 및 build system에 따라 import 순서에 차이가 있습니다.) 대부분의 IDE는 Maven 프로젝트를 직접 가져올 수 있습니다. (Eclipse의 경우 `File`메뉴→ `Import…`→ `Existing Maven Projects` )

프로젝트를 IDE로 직접 가져올 수 없는 경우 build plugin을 사용하여 IDE metadata를 생성할 수 있습니다. Maven에는 Eclipse와 IDEA용 플러그인이 포함되어 있습니다. Gradle은 다양한 IDE용 플러그인을 제공합니다. 

- !Tip : 실수로 web application을 두번 실행하면, “Port already in use”  error가 표시됩니다. STS 사용자는 `Run` 버튼 대신 `Relaunch` 버튼을 사용하여 기존 인스턴스가 닫혀있는지 확인할 수 있습니다. 



### 19.2 패키지된 응용프로그램에서 실행 

Spring Boot Maven이나 Gradle plugin을 사용하여 실행 가능한 jar를 생성하는 경우, 다음 예제와 같이 `java -jar`를 사용해 응용프로그램을 실행할 수 있습니다. 

```
$ java -jar target/myapplication-0.0.1-SNAPSHOT.jar
```

또한 원격 디버깅 지원이 활성화된 패키지된 application을 실행할 수 있습니다. 아래와 같이 패키지된 application에 디버거를 연결할 수 있습니다.

```
$ java -Xdebug -Xrunjdwp:server=y,transport=dt_socket,address=8000,suspend=n \
       -jar target/myapplication-0.0.1-SNAPSHOT.jar

```



### 19.3 Maven Plugin 사용하기

Spring Boot Maven plugin은 application을 빠르게 컴파일하고 실행하는데 사용할 수 있는 `run` goal을 포함합니다. IDE에서와 마찬가지로 Application은 분해된 형태(??)로 실행됩니다.  다음 예제는 Spring Boot application을 실행하는 일반적인 Maven 명령어를 보여줍니다. 

```
$ mvn spring-boot:run

```

다음 예와 같이 `MAVEN_OPTS` 운영 체제 환경 변수를 사용할 수도 있습니다.

```
$ export MAVEN_OPTS=-Xmx1024m

```



### 19.4 Gradle Plugin 사용하기

Spring Boot Gradle plugin에는 application을 분해 된 형태로 실행하는 데 사용할 수있는 `bootRun` task가 포함되어 있습니다. `bootRun` task는 `org.springframework.boot` 및 `java` plugin을 적용 할 때마다 추가되며 다음 예제에 표시됩니다.

```
$ gradle bootRun

```

다음 예제와 같이 `JAVA_OPTS` 운영 체제 환경 변수를 사용할 수도 있습니다.

```
$ export JAVA_OPTS=-Xmx1024m

```



### 19.5 Hot Swapping 

(+ 서버 기동 후 소수 수정 후 서버 재기동 없이 소스가 런타임으로 반영되도록 하는것 )

Spring Boot application은 단순한 Java 애플리케이션이므로 JVM hot-swapping을 즉시 사용할 수 있습니다. JVM hot-swapping은 대체 할 수있는 bytecode로 인해 다소 제한적입니다. 보다 완벽한 솔루션을 위해 JRebel을 사용할 수 있습니다.

`spring-boot-devtools` module 또한 빠른 응용 프로그램 재시작을 지원합니다. 자세한 내용은 이 장의 뒷부분에있는 Chapter 20. Devloper Tools section과 How swapping "How-to"를 참조하십시오.



## 20. 개발자 도구 (Developer Tools)

--> 

Spring Boot includes an additional set of tools that can make the application development experience a little more pleasant. The `spring-boot-devtools` module can be included in any project to provide additional development-time features. To include devtools support, add the module dependency to your build, as shown in the following listings for Maven and Gradle:

**Maven.** 

```
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>

```



**Gradle.** 

```
configurations {
    developmentOnly
    runtimeClasspath {
        extendsFrom developmentOnly
    }
}
dependencies {
    developmentOnly("org.springframework.boot:spring-boot-devtools")
}

```



| ![[Note]](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/images/note.png) |
| ------------------------------------------------------------ |
| Developer tools are automatically disabled when running a fully packaged application. If your application is launched from `java -jar` or if it is started from a special classloader, then it is considered a “production application”. If that does not apply to you (i.e. if you run your application from a container), consider excluding devtools or set the `-Dspring.devtools.restart.enabled=false` system property. |

| ![[Tip]](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/images/tip.png) |
| ------------------------------------------------------------ |
| Flagging the dependency as optional in Maven or using a custom`developmentOnly` configuration in Gradle (as shown above) is a best practice that prevents devtools from being transitively applied to other modules that use your project. |

| ![[Tip]](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/images/tip.png) |
| ------------------------------------------------------------ |
| Repackaged archives do not contain devtools by default. If you want to use a [certain remote devtools feature](https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#using-boot-devtools-remote), you need to disable the `excludeDevtools`build property to include it. The property is supported with both the Maven and Gradle plugins. |