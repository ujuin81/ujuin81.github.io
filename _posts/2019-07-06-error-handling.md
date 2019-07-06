---
title: Error Handling
layout: post
tags: [spring boot, reference]
---

# Spring Boot 의 Error Handling Reference 

> 원본 Reference Url : https://docs.spring.io/spring-boot/docs/2.1.4.RELEASE/reference/htmlsingle/#boot-features-error-handling

### 29.1.11 Error Handling

기본적으로, 스프링 부트는 모든 에러를 적절히 처리하는 `/error` 매핑을 제공하며, 서블릿 컨테이너에 "전역(global)" 에러페이지로 등록됩니다. machine client의 경우 error의 세부사항, HTTP status, exception message와 함께 JSON 응답을 생성합니다. browser client의 경우 HTML 형식에 동일한 데이터를 렌더링하는 “whitelabel” error view가 있습니다 (사용자 정의시, `error`를 해결(resolve)하는 `view`를 추가). 기본 동작을 완전히 바꾸려면, `ErrorController`를 구현(implement)하고 해당 타입의 bean 정의를 등록하거나 `ErrorAttributes` 타입의 bean을 추가하여 기존 매커니즘을 사용하지만 내용은 대체됩니다. 

* Tip : `BasicErrorController`는 custom `ErrorController`의 기본 클래스로 사용할 수 있습니다. 이것은 새로운 content type을 위한 핸들러를 추가하려는 경우에 특히 유용합니다 (기본은 `text/html`을 명시적으로 처리하고 다른 모든 항목에 대체기능(fallback)을 제공합니다). 그렇게 하기 위해서는, `BasicErrorController`를 확장(extend)하고, `produce` attribute를 가지는 `@RequestMapping` public method를 추가하고, 새로운 타입의 bean을 생성합니다. 

  > [개인 내용 추가 : 위 `BasicErrorController` 부분 이해 관련 소스]
  >
  > ```java
  > @Controller
  > @RequestMapping("${server.error.path:${error.path:/error}}")
  > public class BasicErrorController extends AbstractErrorController {
  >     // ... 생략 ... 
  >     @RequestMapping(produces = MediaType.TEXT_HTML_VALUE)
  > 	public ModelAndView errorHtml(HttpServletRequest request,
  > 			HttpServletResponse response) {
  > 		HttpStatus status = getStatus(request);
  > 		Map<String, Object> model = Collections.unmodifiableMap(getErrorAttributes(
  > 				request, isIncludeStackTrace(request, MediaType.TEXT_HTML)));
  > 		response.setStatus(status.value());
  > 		ModelAndView modelAndView = resolveErrorView(request, response, status, model);
  > 		return (modelAndView != null) ? modelAndView : new ModelAndView("error", model);
  > 	}
  > }
  > ```

다음 예제와 같이 `@ControllerAdvice` annotation이 있는 클래스를 정의하여 특정 controller 와/또는 exception type에 대해 반환할 JSON document를 사용자 정의할 수 있습니다 : 

```java
@ControllerAdvice(basePackageClasses = AcmeController.class)
public class AcmeControllerAdvice extends ResponseEntityExceptionHandler {

	@ExceptionHandler(YourException.class)
	@ResponseBody
	ResponseEntity<?> handleControllerException(HttpServletRequest request, Throwable ex) {
		HttpStatus status = getStatus(request);
		return new ResponseEntity<>(new CustomErrorType(status.value(), ex.getMessage()), status);
	}

	private HttpStatus getStatus(HttpServletRequest request) {
		Integer statusCode = (Integer) request.getAttribute("javax.servlet.error.status_code");
		if (statusCode == null) {
			return HttpStatus.INTERNAL_SERVER_ERROR;
		}
		return HttpStatus.valueOf(statusCode);
	}

}
```

앞의 예에서,`YourException`이 `AcmeController`와 같은 패키지에 정의된 컨트롤러에서 throw된 경우라면 `CustomErrorType` POJO의 JSON 표현이 `ErrorAttributes` 표현 대신 사용됩니다.



#### Custom Error Pages

status code에 대한 사용자 정의 HTML 에러 페이지를 표시하려면, `/error` 폴더에 파일을 추가하면 됩니다. 에러 페이지는 static HTML(static resource 폴더 아래에 추가된) 또는 templates를 사용하여 빌드될 수 있습니다. 파일의 이름은 정확한 status code 또는 series mask여야 합니다. 

예를 들어 `404`를 static HTML 파일에 매핑하려면 아래와 같은 폴더 구조여야 합니다 : 

```
src/
 +- main/
     +- java/
     |   + <source code>
     +- resources/
         +- public/
             +- error/
             |   +- 404.html
             +- <other public assets>
```

FreeMarker 템플릿을 사용하여 `5xx` 에러를 모두 매핑하려면 폴더 구조가 다음과 같습니다 : 

```
src/
 +- main/
     +- java/
     |   + <source code>
     +- resources/
         +- templates/
             +- error/
             |   +- 5xx.ftl
             +- <other templates>
```

더 복잡한 매핑의 경우, 다음 예제와 같이 `ErrorViewResolver` 인터페이스를 구현하는 bean을 추가할 수도 있습니다 : 

```java
public class MyErrorViewResolver implements ErrorViewResolver {

	@Override
	public ModelAndView resolveErrorView(HttpServletRequest request,
			HttpStatus status, Map<String, Object> model) {
		// Use the request or status to optionally return a ModelAndView
		return ...
	}

}
```

또한 [`@ExceptionHandler` methods](https://docs.spring.io/spring/docs/5.1.6.RELEASE/spring-framework-reference/web.html#mvc-exceptionhandlers)와 [`@ControllerAdvice`](https://docs.spring.io/spring/docs/5.1.6.RELEASE/spring-framework-reference/web.html#mvc-ann-controller-advice)와 같은 일반적인 Spring MVC 기능을 사용할 수도 있습니다. `ErrorController`는 처리되지 않은 예외를 선택합니다.



#### Spring MVC의 외부 에러 페이지 매핑(Mapping Error Pages outside of Spring MVC)

Spring MVC를 사용하지 않는 응용 프로그램의 경우, `ErrorPageRegistrar` interface를 사용하여 `ErrorPages`를 직접 등록 할 수 있습니다. 이 추상화는 기본 내장된 서블릿 컨테이너와 직접 동작하며 Spring MVC `DispatcherServlet`이 없어도 동작합니다.

```java
@Bean
public ErrorPageRegistrar errorPageRegistrar(){
	return new MyErrorPageRegistrar();
}

// ...

private static class MyErrorPageRegistrar implements ErrorPageRegistrar {

	@Override
	public void registerErrorPages(ErrorPageRegistry registry) {
		registry.addErrorPages(new ErrorPage(HttpStatus.BAD_REQUEST, "/400"));
	}

}
```

* Note : (Jersey와 Wicket과 같은 일부 non-Spring web framework에서 일반적인것처럼) `Filter`에 의해 처리되는 경로로 `ErrorPage`를 등록하면 `Filter`는 다음과 같이 `ERROR` dispatcher로 명시적으로 등록되어야합니다 : 

  ```java
  @Bean
  public FilterRegistrationBean myFilter() {
  	FilterRegistrationBean registration = new FilterRegistrationBean();
  	registration.setFilter(new MyFilter());
  	...
  	registration.setDispatcherTypes(EnumSet.allOf(DispatcherType.class));
  	return registration;
  }
  ```

  기본` FilterRegistrationBean`은 `ERROR` dispatcher 타입이 포함되어 있지 않습니다.

주의 : 서블릿 컨테이너에 배포할 때, Spring Boot는 적합한 에러 페이지에 error status와 함께 요청을 전달(forward)하기 위해 error page filter를 사용합니다. 응답이 아직 커밋되지 않은 경우에만 요청을 올바른 오류 페이지로 전달할 수 있습니다. 기본적으로, WebSphere Application Server 8.0 이상에서는 서블릿의 서비스 메소드 완료시 응답을 커밋합니다. `com.ibm.ws.webcontainer.invokeFlushAfterService`를 `false`로 설정하여이 이러한 동작을 사용 불가능하게 해야합니다.