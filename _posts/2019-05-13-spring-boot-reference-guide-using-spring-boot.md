---
title: spring boot reference guide-Using Spring Boot
layout: post
---

> Spring Boot Reference Guide 
> Version : 2.1.4.RELEASE
> Url : <https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#using-boot>

###### * 영문으로 작성된 레퍼런스를 구글 번역의 도움으로 내가 읽기 쉬운 형태로 보기 쉽게 하기 위해 작성

###### * 필요한 부분부터 작성하기 시작했으므로 앞 부분은 차후 **필요에 따라** 추가 할수도..

# Part III. 스프링 부트 사용하기 

... 앞 부분 생략 (차후 필요에 따라 추가할 수도 있음) ...  

## 14. Structuring Your Code

스프링 부트는 특정 코드 레이아웃을 요구하지 않습니다. 하지만 도움이되는 몇가지 모범 사례가 있습니다.


#### 14.1 “default” Package 사용

package 선언이 없는 class 사용을 **피해야 합니다.** `@ComponentScan`, `@EntityScan` 또는 `@SpringBootApplication` 시에 모든 jar의 모든 class를 읽어들여 응용프로그램에 문제를 일으킬 수 있습니다. 

| [Tip]                                                        |
| ------------------------------------------------------------ |
| `com.example.project` 처럼 Java 권장 패키지 이름 규칙을 사용을 권장합니다. |



#### 14.2 Main Application Class 위치 

main application class는 다른 클래스들 보다 상위에 있는 root package에 두는 것을 권장합니다. `@SpringBootApplication` annotation이 달린 class의 package를 사용하여 특정 항목(예를 들어 `@Entity`) 검색합니다. root package를 사용하면 구성 요소 검사를 프로젝트에만 적용할 수 있습니다. 
(+default pacakge 사용을 피하라는 이유와 동일한 내용) 

| [Tip]                                                        |
| ------------------------------------------------------------ |
| `@SpringBootApplication`사용을 원하지 않으면, `@EnableAutoConfiguration` , `@ComponentScan` annotations 을 대신 사용할 수 있습니다. |

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

| [Tip]                                                        |
| ------------------------------------------------------------ |
| XML 구성을 사용하는 많은 Spring 구성 예제가 인터넷에 게시되어있습니다. 가능하다면 항상 Java-based configuration을 사용해보십시오. `Enable*` annotations을 검색하는 것이 좋은 출발점이 될 수 있습니다. |



#### 15.1 추가 구성 클래스 가져오기 

모든 `@Configuration`를 단일 class에 넣을 필요는 없습니다. `@Import` annotation은 추가 구성 클래스를 가져올 수 있습니다. 또는 `@ComponentScan`을 사용하여 `@Configuration` 클래스를 포함해서 모든 Spring components를 자동으로 선택할 수 있습니다.



#### 15.2 XML 구성 가져오기

XML 기반 구성을 사용해야만 하는 경우에도 `@Configuration` class로 시작하는 것이 좋습니다.  `@ImportResource`annotation을 사용하여 XML 구성 파일을 로드할 수 있습니다. 



## 16. 자동 구성 (Auto-configuration)

Spring Boot auto-configuration는 여러분이 추가한 jar 의존성을 기반으로 Spring application을 자동으로 설정하려고 시도합니다. 예를 들어,  `HSQLDB`가 classpath에 있고 database 연결 bean을 수동으로 설정하지 않았다면 Spring Boot는 내장(in-memory) database를 자동으로 구성합니다. 

`@EnableAutoConfiguration` 또는 `@SpringBootApplication` annotation을 `@Configuration` class 중 하나에 추가하여 자동 구성을 선택해야합니다.

| [Tip]                                                        |
| ------------------------------------------------------------ |
| `@SpringBootApplication`, `@EnableAutoConfiguration` annotation 중 오직 하나만 추가하십시오.  일반적으로 기본 `@Configuration` class에만 둘 중 하나를 추가하는 것이 좋습니다. |



#### 16.1 자동 구성 점차적으로 대체하기 (Gradually Replacing Auto-configuration)

언제든지, 자동 구성의 특정 부분을 대체하기 위해 자체 구성을 정의할 수 있습니다. 예를 들어 `DataSource` bean을 추가하면 기본 내장 database 지원은 사라집니다. 

현재 적용된 자동 구성이 무엇인지, 왜 적용됐는지 알아야 한다면 `--debug` switch로 application 시작하십시오. 이렇게 하면 core logger 중 일부에 대한 debug log가 활성화되고 console에 conditions report가 기록됩니다.


