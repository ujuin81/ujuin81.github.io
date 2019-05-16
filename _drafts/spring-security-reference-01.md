---
title: Spring Security Reference 01
layout: post
tags: [Reference, Spring, Spring Security, 정리중]
---

###### * 영문으로 작성된 레퍼런스를 구글 번역의 도움으로 내가 읽기 쉬운 형태로 보기 쉽게 하기 위해 작성
###### * (+ 내용) <- 레퍼런스에 없는 추가 설명 표시 
###### * 완료되지 않음. 아직 진행중 



> Spring Security Reference
> Version : 5.2.0.BUILD-SNAPSHOT
> Url : <https://docs.spring.io/spring-security/site/docs/5.2.0.BUILD-SNAPSHOT/reference/htmlsingle/>



Spring Security는 일반적인 공격에 대한 인증, 권한 부여 및 보호를 제공하는 프레임 워크입니다. imperative(명령형) 및 reactive application 모두에 대한 일등급 지원을 통해 스프링 기반 응용 프로그램 보안을위한 사실상의 표준입니다. 



# Part I. 서문 



이 절에서는 Spring Security의 logistic(?업무조직)에 대해 설명합니다.

## 1. Spring Security Community

이 섹션에서는 어떻게 우리의 방대한 커뮤니티를 최대한 활용할 수 있는지에 대해 설명합니다.



### 1.1 도움 받기 (Getting Help)

- reference 문서 읽기 
- 많은 sample application 중 하나를 사용해보십시오. 
- 질문 : [https://stackoverflow.com](https://stackoverflow.com/) (`spring-security` tag)
- 버그 및 개선 요청 : <https://github.com/spring-projects/spring-security/issues> 

### 1.2 참여하기 

StackOverflow에 있는 질문에 답하고, 새 코드를 작성하고, 기존 코드를 개선하고, 설명서를 지원하고, sample 또는 tutorial을 개발하고, 버그를보고하거나 간단히 제안하는 등 다양한 방법으로 공헌 할 수 있습니다.

### 1.3 Source Code

Spring Security’s source code : <https://github.com/spring-projects/spring-security/

### 1.4 Apache 2 License

Spring Security는 [Apache 2.0 license](https://www.apache.org/licenses/LICENSE-2.0.html)에 따라 공개된 오픈 소스 소프트웨어입니다.  

### 1.5 Social Media

Twitter : [@SpringSecurity](https://twitter.com/SpringSecurity) , [Spring Security team](https://twitter.com/SpringSecurity/lists/team),  [@SpringCentral](https://twitter.com/SpringCentral)



## 2. Spring Security 5.1의 새로운 기능

... 생략 ... 



## 3. Getting Spring Security 

이 섹션에서는 Spring Security 바이너리를 얻는 방법에 대해 알아야 할 모든 것에 대해 설명합니다. 소스 코드를 얻는 방법은 1.3 절. "소스 코드"를 참조하십시오.

### 3.1 Release Numbering

Spring Security version은 MAJOR.MINOR.PATCH 형식입니다. 

... MAJOR/MINOR/PATCH 내용에 대한 부분 생략 ...  



### 3.2 Usage with Maven

대부분의 오픈 소스 프로젝트와 마찬가지로 Spring Security는 종속물을 Maven 아티팩트로 배포합니다. 다음 섹션에서는 Maven을 사용할 때 Spring Security를 사용하는 방법에 대해 자세히 설명합니다.

#### 3.2.1 Spring Boot with Maven

Spring Boot는 Spring Security 관련 의속성을 함께 모아둔 spring-boot-starter-security 스타터를 제공합니다. 스타터를 활용하는 가장 간단하고 선호되는 방법은 통합 IDE (Eclipse, IntelliJ, NetBeans) 또는 https://start.spring.io를 사용하여 Spring Initializr을 사용하는 것입니다.

또는 스타터를 수동으로 추가할 수도 있습니다 :

**pom.xml.** 

```xml
<dependencies>
    <!-- ... other dependency elements ... -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
</dependencies>
```

Spring Boot는 의존성 버전을 관리를 위한 Maven BOM을 제공하기 때문에 버전을 지정할 필요가 없습니다. Spring Security version을 오버라이드하려면 Maven 속성을 제공하면됩니다.

**pom.xml.** 

```xml
<properties>
    <!-- ... -->
    <spring-security.version>5.2.0.BUILD-SNAPSHOT</spring-security.version>
</dependencies>
```

Spring Security는 주요 release에서 변경 사항을 변경하기 때문에 Spring Boot와 함께 최신 버전의 Spring Security를 사용하는 것이 안전합니다. 그러나 때때로 Spring Framework의 버전을 업데이트해야 할 수도 있습니다. 이는 Maven 속성을 추가하여 쉽게 수행 할 수 있습니다.

**pom.xml.** 

```xml
<properties>
    <!-- ... -->
    <spring.version>5.2.0.M2</spring.version>
</dependencies>
```

LDAP, OpenID 등과 같은 추가 기능을 사용하는 경우,  적절한 [Chapter 4, *Project Modules*](https://docs.spring.io/spring-security/site/docs/5.2.0.BUILD-SNAPSHOT/reference/htmlsingle/#modules)도 포함시켜야합니다.

#### 3.2.2 Maven Without Spring Boot

Spring Boot없이 Spring Security을 사용할 때 선호되는 방법은 Spring Security의 BOM을 활용하여 전체 프로젝트에서 일관된 버전의 Spring Security를 사용하는 것입니다.

**pom.xml.** 

```xml
<dependencyManagement>
    <dependencies>
        <!-- ... other dependency elements ... -->
        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-bom</artifactId>
            <version>5.2.0.BUILD-SNAPSHOT</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

최소한의 Spring Security Maven 세트는 일반적으로 다음과 같이 보입니다.

**pom.xml.** 

```xml
<dependencies>
    <!-- ... other dependency elements ... -->
    <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-config</artifactId>
    </dependency>
</dependencies>
```

LDAP, OpenID 등과 같은 추가 기능을 사용하는 경우,  적절한 [Chapter 4, *Project Modules*](https://docs.spring.io/spring-security/site/docs/5.2.0.BUILD-SNAPSHOT/reference/htmlsingle/#modules)도 포함시켜야합니다.

Spring Security는 Spring Framework 5.2.0.M2에 맞춰 빌드되지만 일반적으로 새로운 버전의 Spring Framework 5.x에서 작동해야합니다. 많은 사용자들이 겪게 될 문제는 Spring Security의 전이 의존성이 Spring Framework 5.2.0.M2를 해결하여 이상한 클래스 경로 문제를 일으킬 수 있다는 것입니다. (+??) 가장 쉬운 해결 방법은 아래와 같이 `pom.xml`의 `<dependencyManagement>` 섹션에서 `spring-framework-bom`을 사용하는 것입니다.

**pom.xml.** 

```xml
<dependencyManagement>
    <dependencies>
        <!-- ... other dependency elements ... -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-framework-bom</artifactId>
            <version>5.2.0.M2</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

이렇게하면 Spring Security의 모든 전이 종속성이 스프링 5.2.0.M2 모듈을 사용하게됩니다.

!Note : 이 방법은 Maven의 "BOM(bill of materials)"개념을 사용하며 Maven 2.0.9 이상에서만 사용할 수 있습니다. 의존성이 어떻게 해결되는지에 대한 더 자세한 정보는 [Maven의 Dependency Mechanism 소개 문서](https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html)를 참조하십시오.

#### 3.2.3 Maven Repositories

모든 GA 릴리즈 (즉, .RELEASE로 끝나는 버전)는 Maven Central에 배포되므로 추가 Maven repositories를 pom에 선언 할 필요가 없습니다.

SNAPSHOT 버전을 사용하는 경우 아래와 같이 Spring Snapshot repository를 정의해야합니다.

**pom.xml.** 

```xml
<repositories>
    <!-- ... possibly other repository elements ... -->
    <repository>
        <id>spring-snapshot</id>
        <name>Spring Snapshot Repository</name>
        <url>https://repo.spring.io/snapshot</url>
    </repository>
</repositories>
```

Milestone 또는 release candidate version을 사용하는 경우 Spring Milestone repository가 다음과 같이 정의되어 있어야합니다.

**pom.xml.** 

```xml
<repositories>
    <!-- ... possibly other repository elements ... -->
    <repository>
        <id>spring-milestone</id>
        <name>Spring Milestone Repository</name>
        <url>https://repo.spring.io/milestone</url>
    </repository>
</repositories>
```



## 3.3 Gradle

... 우선 생략 ...



## 4. Project Modules

Spring Security 3.0에서 codebase는 서로 다른 기능 영역과 써드 파티 의존 관계를 명확히 구분하는 별도의 jar로 세분되었습니다. Maven을 사용하여 프로젝트를 빌드하는 경우, 이들은 `pom.xml`에 추가 할 모듈입니다. Maven을 사용하지 않더라도 `pom.xml` 파일을 참조하여 third-party 종속성 및 버전에 대한 아이디어를 얻는 것이 좋습니다. 또는 샘플 응용 프로그램에 포함된 라이브러리를 검사하는 것이 좋습니다.

### 4.1 Core - spring-security-core.jar

core authentication와 access-contol class 및 interfaces, 원격 지원과 basic provisioning API를 포함합니다. Spring Security를 사용하는 모든 애플리케이션에 필요합니다. standalone applications, remote clients, method (service layer) security 및 JDBC user provisioning을 지원합니다. 최상위 패키지에 포함 : 

- `org.springframework.security.core`
- `org.springframework.security.access`
- `org.springframework.security.authentication`
- `org.springframework.security.provisioning`

### 4.2 Remoting - spring-security-remoting.jar

Spring Remoting과의 통합을 제공합니다. Spring Remoting을 사용하는 원격 클라이언트를 작성하지 않는 한 필요하지 않습니다. main package는 `org.springframework.security.remoting`입니다.

### 4.3 Web - spring-security-web.jar

filter 및 관련 web-security infrastructure code가 들어 있습니다. 서블릿 API 의존성이 있는 것(Anything with a servlet API dependency). Spring Security web authentication services와 URL-based access-control이 필요한 경우 필요합니다. main package는 `org.springframework.security.web`입니다.

### 4.4 Config - spring-security-config.jar

security namespace parsing code & Java configuration code가 들어 있습니다. 설정이나 Spring Security의 Java Configuration 지원을 위해 Spring Security XML namespace를 사용하는 경우 필요합니다. main package는 `org.springframework.security.config`입니다. 어떤 클래스도 application에서 직접 사용하기위한 것이 아닙니다.

### 4.5 LDAP - spring-security-ldap.jar

LDAP authentication 및 provisioning code. LDAP 인증을 사용하거나 LDAP user entries를 관리해야하는 경우 필요합니다. top-level package는 `org.springframework.security.ldap`입니다. 

### 4.6 OAuth 2.0 Core - spring-security-oauth2-core.jar

`spring-security-oauth2-core.jar`에는 OAuth 2.0 Authorization Framework와 OpenID Connect Core 1.0을 지원하는 core class와 interface가 포함되어 있습니다. OAuth 2.0 또는 Client, Resource Server, Authorization Server와 같은 OpenID Connect Core 1.0 를 사용하는 응용 프로그램에서 필요합니다. top-level package는 `org.springframework.security.oauth2.core`입니다.

### 4.7 OAuth 2.0 Client - spring-security-oauth2-client.jar

`spring-security-oauth2-client.jar`는 OAuth 2.0 Authorization Framework와 OpenID Connect Core 1.0에 대한 Spring Security의 클라이언트 지원입니다. **OAuth 2.0 Login** and/or OAuth Client 지원을 활용하는 application에서 필요합니다. top-level package는 `org.springframework.security.oauth2.client`입니다.

### 4.8 OAuth 2.0 JOSE - spring-security-oauth2-jose.jar

`spring-security-oauth2-jose.jar`에는 JOSE (Javascript Object Signing and Encryption) framework에 대한 Spring Security의 지원이 포함되어 있습니다. JOSE framework는 당사자간에 클레임을 안전하게 전송하는 방법을 제공하기위한 것입니다. 그것은 collection of specifications에서 만들어졌습니다 :

- JSON Web Token (JWT)
- JSON Web Signature (JWS)
- JSON Web Encryption (JWE)
- JSON Web Key (JWK)

여기에는 top-level packages가 들어있습니다:

- `org.springframework.security.oauth2.jwt`
- `org.springframework.security.oauth2.jose` 

### 4.9 ACL - spring-security-acl.jar

특수 도메인 객체 ACL 구현.  application의 특정 도메인 객체 인스턴스에 보안을 적용하는 데 사용됩니다. top-level package는 `org.springframework.security.acls`입니다.

### 4.10 CAS - spring-security-cas.jar

Spring Security의 CAS 클라이언트 통합. CAS single sign-on server로 Spring Security web authentication을 사용하고자하는 경우. top-level package는 `org.springframework.security.cas`입니다.

### 4.11 OpenID - spring-security-openid.jar

OpenID web authentication 지원. 외부 OpenID server에 대해 사용자를 인증하는 데 사용됩니다. `org.springframework.security.openid`. OpenID4Java가 필요합니다.

### 4.12 Test - spring-security-test.jar

Spring Security로 테스트 지원



## 5. Sample Applications

프로젝트의 소스 코드와 함께 제공되는 몇 가지 [샘플 웹 응용 프로그램](https://github.com/spring-projects/spring-security/tree/master/samples)이 있습니다. 아래에 설명 된 것을 루트 프로젝트의 samples / xml / 하위 디렉토리에서 찾을 수 있습니다.

이 장에서 언급 된 모든 경로는 project source directory에 상대적(relative)입니다.

### 5.1 Tutorial Sample

tutorial sample은 시작하기 좋은 기본 예제입니다. 전체적으로 간단한 네임스페이스 구성을 사용합니다. 컴파일 된 응용 프로그램은 배포 zip 파일에 포함되어 웹 컨테이너에 배포 할 준비가되었습니다 (`spring-security-samples-tutorial-3.1.x.war`).  폼 기반(form-based) 인증 메커니즘은 일반적으로 사용되는 remember-me authentication provider와 함께 사용되어 cookie를 사용하여 login을 자동으로 기억합니다.

XML은 최소한이고 따라하기 쉽기 때문에 튜토리얼 샘플부터 시작하는 것이 좋습니다. 가장 중요한 것은 이 하나의 XML 파일 (and its corresponding `web.xml` entries)을 기존 응용 프로그램에 쉽게 추가 할 수 있다는 것입니다. 이 기본 통합이 이루어질 때에만 method authorization 또는 domain object security에 추가하는 것이 좋습니다. (??) 

### 5.2 Contacts

Contacts Sample는 기본 응용 프로그램 보안 외에도 domain object access control lists(ACL)의보다 강력한 기능을 보여주는 고급 예제입니다. 이 응용 프로그램은 사용자가 contacts의 간단한 데이터베이스(the domain objects)를 관리 할 수있는 인터페이스를 제공합니다.

배포하려면 Spring Security distribution의 WAR 파일을 컨테이너의 `webapps` 디렉토리로 복사하기만하면 됩니다. war파일은 `spring-security-samples-contacts-3.1.x.war`(추가 버전 번호는 사용중인 릴리스에 따라 다름)라고해야합니다.

컨테이너를 시작한 후 응용 프로그램이 로드 할 수 있는지 확인하십시오. http://localhost:8080/contacts(또는 귀하의 웹 컨테이너와 배포한 WAR에 적합한 URL)를 방문하십시오 .

그런 다음 "Debug"를 클릭하십시오. 인증하라는 메시지가 표시되며 해당 페이지에 일련의 사용자 이름과 비밀번호가 제안됩니다. 이 중 하나를 사용하여 인증하고 결과 페이지를보십시오. 다음과 유사한 성공 메시지가 포함되어야합니다.

```
Security Debug Information

Authentication object is of type:
org.springframework.security.authentication.UsernamePasswordAuthenticationToken

Authentication object as a String:

org.springframework.security.authentication.UsernamePasswordAuthenticationToken@1f127853:
Principal: org.springframework.security.core.userdetails.User@b07ed00: Username: rod; \
Password: [PROTECTED]; Enabled: true; AccountNonExpired: true;
credentialsNonExpired: true; AccountNonLocked: true; \
Granted Authorities: ROLE_SUPERVISOR, ROLE_USER; \
Password: [PROTECTED]; Authenticated: true; \
Details: org.springframework.security.web.authentication.WebAuthenticationDetails@0: \
RemoteIpAddress: 127.0.0.1; SessionId: 8fkp8t83ohar; \
Granted Authorities: ROLE_SUPERVISOR, ROLE_USER

Authentication object holds the following granted authorities:

ROLE_SUPERVISOR (getAuthority(): ROLE_SUPERVISOR)
ROLE_USER (getAuthority(): ROLE_USER)

Success! Your web filters appear to be properly configured!
```

위 메시지가 성공적으로 수신되면 샘플 응용프로그램의 홈페이지로 돌아가서 "Manage"를 클릭하십시오. 그런 다음 애플리케이션을 사용해 볼 수 있습니다. 현재 로그온 한 사용자가 사용할 수있는 contacts만 표시되고 `ROLE_SUPERVISOR`인 사용자만 contacts를 삭제할 수있는 액세스 권한이 부여됩니다. 그 뒤에는 `MethodSecurityInterceptor`가 비즈니스 객체를 보호합니다.

응용 프로그램은 다른 contacts와 관련된 액세스 제어 목록을 수정할 수 있습니다. 이를 응용 프로그램 컨텍스트 XML 파일을 검토하여 작동 방법을 이해하고 시도해보십시오. 

## 5.3 LDAP Sample ~ 5.7 Pre-Authentication Sample

... 우선 생략 ...



# Part II. Servlet Applications

## 6. Java Configuration

Java Configuration에 대한 일반적인 지원이 Spring 3.1의 Spring Framework에 추가되었습니다. Spring Security 3.2부터는 사용자가 XML을 사용하지 않고도 Spring Security를 쉽게 구성 할 수있는 Spring Security Java Configuration 지원이 있었습니다. 

[Chapter 7, *Security Namespace Configuration*](https://docs.spring.io/spring-security/site/docs/5.2.0.BUILD-SNAPSHOT/reference/htmlsingle/#ns-config)에 익숙하다면 보안 Java Configuration 지원과의 유사점을 발견해야합니다.

- !Note : Spring Security는 Spring Security Java Configuration의 사용법을 보여주는 많은 [샘플 애플리케이션](https://github.com/spring-projects/spring-security/tree/master/samples/javaconfig)을 제공합니다.

### 6.1 Hello Web Security Java Configuration

첫 번째 단계는 Spring Security Java Configuration을 만드는 것이다. configuration은 응용 프로그램 내에서 모든 보안(protecting the application URLs, validating submitted username and passwords, redirecting to the log in form, etc)을 담당하는 `springSecurityFilterChain`이라는 서블릿 필터를 만듭니다. 

아래에서 Spring Security Java Configuration의 가장 기본적인 예제를 찾을 수있습니다. : 

```
import org.springframework.beans.factory.annotation.Autowired;

import org.springframework.context.annotation.*;
import org.springframework.security.config.annotation.authentication.builders.*;
import org.springframework.security.config.annotation.web.configuration.*;

@EnableWebSecurity
public class WebSecurityConfig implements WebMvcConfigurer {

    @Bean
    public UserDetailsService userDetailsService() throws Exception {
        InMemoryUserDetailsManager manager = new InMemoryUserDetailsManager();
        manager.createUser(User.withDefaultPasswordEncoder().username("user").password("password").roles("USER").build());
        return manager;
    }
}
```

이 구성은 별로 많지 않지만,  많은 일을합니다. 아래 기능 요약을 볼 수 있습니다 : 

- application의 모든 URL에 인증 요구
- login form 생성
- **Username** *user*와 **Password** *password*를 가진 사용자가 폼 기반 인증으로 인증하도록 허용 
- 사용자의 logout을 허용
- [CSRF 공격](https://en.wikipedia.org/wiki/Cross-site_request_forgery) 방지
- [Session 고정(Fixation)](https://en.wikipedia.org/wiki/Session_fixation) 보호
- Security Header 통합
  - 안전한 요청(secure requests)을위한 [HTTP Strict 전송 보안(HTTP Strict Transport Security)](https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security)
  - [X-Content-Type-Options](https://msdn.microsoft.com/en-us/library/ie/gg622941(v=vs.85).aspx) 통합
  - Cache Control (static resource 캐싱을 허용하도록 나중에 응용프로그램에서 재정의할 수 있음)
  - [X-XSS-Protection](https://msdn.microsoft.com/en-us/library/dd565647(v=vs.85).aspx) 통합
  - [Clickjacking](https://en.wikipedia.org/wiki/Clickjacking)을 방지하는 X-Frame-Options 통합https://en.wikipedia.org/wiki/Clickjacking)
- 다음 Servlet API method와 통합 
  - [HttpServletRequest#getRemoteUser()](https://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html#getRemoteUser())
  - [HttpServletRequest.html#getUserPrincipal()](https://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html#getUserPrincipal())
  - [HttpServletRequest.html#isUserInRole(java.lang.String)](https://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html#isUserInRole(java.lang.String))
  - [HttpServletRequest.html#login(java.lang.String, java.lang.String)](https://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html#login(java.lang.String, java.lang.String))
  - [HttpServletRequest.html#logout()](https://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html#logout())

#### 6.1.1 AbstractSecurityWebApplicationInitializer

다음 단계는 `springSecurityFilterChain`을 war에 등록하는 것입니다. 이것은 Servlet 3.0+ 환경에서 [Spring의 WebApplicationInitializer를 지원](https://docs.spring.io/spring/docs/3.2.x/spring-framework-reference/html/mvc.html#mvc-container-config)하는 Java Configuration에서 수행 할 수 있습니다. 놀랍지 않게, Spring Security는 `springSecurityFilterChain`이 등록되도록 해주는 기본 클래스인 `AbstractSecurityWebApplicationInitializer`를 제공합니다. `AbstractSecurityWebApplicationInitializer`를 사용하는 방식은 이미 Spring을 사용하고 있는지 또는 Spring Security가 애플리케이션의 유일한 Spring 구성 요소인지 여부에 따라 다릅니다.

- [Section 6.1.2, “AbstractSecurityWebApplicationInitializer without Existing Spring”](https://docs.spring.io/spring-security/site/docs/5.2.0.BUILD-SNAPSHOT/reference/htmlsingle/#abstractsecuritywebapplicationinitializer-without-existing-spring) - Spring을 사용하고 있지 않다면 이 지시사항을 사용하십시오. 
- [Section 6.1.3, “AbstractSecurityWebApplicationInitializer with Spring MVC”](https://docs.spring.io/spring-security/site/docs/5.2.0.BUILD-SNAPSHOT/reference/htmlsingle/#abstractsecuritywebapplicationinitializer-with-spring-mvc) - Spring을 사용하고 있다면 이 지시사항을 사용하십시오. 

#### 6.1.2 AbstractSecurityWebApplicationInitializer without Existing Spring

Spring이나 Spring MVC를 사용하지 않는다면 `WebSecurityConfig`를 superclass에 전달하여 구성이 선택되었는지 확인해야한다. 아래 예를 볼 수 있습니다 : 

```
import org.springframework.security.web.context.*;

public class SecurityWebApplicationInitializer
    extends AbstractSecurityWebApplicationInitializer {

    public SecurityWebApplicationInitializer() {
        super(WebSecurityConfig.class);
    }
}
```

`SecurityWebApplicationInitializer`는 다음 작업을 수행 :

- 애플리케이션의 모든 URL에 대해 springSecurityFilterChain 필터를 자동으로 등록
- [WebSecurityConfig](https://docs.spring.io/spring-security/site/docs/5.2.0.BUILD-SNAPSHOT/reference/htmlsingle/#jc-hello-wsca)를 로드하는 ContextLoaderListener를 추가 

#### 6.1.3 AbstractSecurityWebApplicationInitializer with Spring MVC

애플리케이션의 다른 곳에서 Spring을 사용했다면 Spring Configuration을 로드하는 `WebApplicationInitializer`가 이미 있을 것입니다. 이전 구성을 사용하면 오류가 발생합니다. 대신 Spring Security를 기존 `ApplicationContext`에 등록해야합니다. 예를 들어 Spring MVC를 사용한다면 `SecurityWebApplicationInitializer`는 다음과 같이 보일 것입니다 :

```
import org.springframework.security.web.context.*;

public class SecurityWebApplicationInitializer
    extends AbstractSecurityWebApplicationInitializer {

}
```

그러면 응용 프로그램의 모든 URL에 대해 springSecurityFilterChain 필터만 등록하면됩니다. 그런 다음 `WebSecurityConfig`가 기존 `ApplicationInitializer`에로드되었는지 확인합니다. 예를 들어, 우리가 Spring MVC를 사용했다면 `getRootConfigClasses()`에 추가 될 것입니다. 

```
public class MvcWebApplicationInitializer extends
        AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[] { WebSecurityConfig.class };
    }

    // ... other overrides ...
}
```

