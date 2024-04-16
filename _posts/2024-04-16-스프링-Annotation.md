---
title: 스프링 Annotation
excerpt: "10주차 스터디"

categories:
  - Spring

permalink: /spring/2/

toc: true
toc_sticky: true
date: 2024-04-16
last_modified_at: 2024-04-16
---

## Annotation

소스 코드에 추가되어 특정한 정보를 제공하거나 처리하는 메타데이터의 형태입니다. 주석과는 다르게 컴파일러나 런타임 시스템에게 특별한 의미를 전달합니다.

## 메타 어노테이션

메타 어노테이션은 다른 어노테이션을 정의할 때 사용되는 어노테이션입니다. 즉, 어노테이션을 정의할 때 그 어노테이션의 특성을 지정하는 데 사용됩니다.

- 예시
    - @Target - 어노테이션이 적용될 대상을 지정
    - @Retention - 어노테이션의 유지 정책 지정
    - @Documented - 해당 어노테이션을 javadoc에 포함
    - @Inherited - 어노테이션의 상속을 가능하게 함
    - @Repeatable - 어노테이션을 반복해서 적용할 수 있게 함

## 표준 어노테이션

표준 어노테이션은 자바 플랫폼(API)에서 제공하는 기본 어노테이션입니다. 자주 사용되는 어노테이션들로 이루어져 있습니다.

- 예시
    - @Override - 메서드가 부모 클래스의 메서드를 오버라이딩했음을 나타냄. 컴파일러에게 알려줌
    - @Deprecated - 해당 요소가 더 이상 사용되지 않음을 나타냄
    - @SuppressWarnings - 특정한 경고 메시지를 무시하고 컴파일러가 해당 경고를 발생시키지 않도록 함

### 어노테이션 용도

1. 컴파일러에게 코드 작성 문법 에러를 체크하도록 정보를 제공
2. 소프트웨어 개발툴이 빌드나 배치시 코드를 자동으로 생성할 수 있도록 정보 제공
3. **실행시(런타임시)특정 기능을 실행하도록 정보를 제공**

### 어노테이션 동작 순서

1. **패키지 수입(import)**:
    - 필요한 어노테이션을 사용하기 위해 필요한 패키지를 수입합니다.
2. **클래스 또는 필드에 어노테이션 적용**:
    - 클래스, 메서드, 필드 등에 어노테이션을 적용합니다. 이때 적용된 어노테이션은 해당 요소에 대한 정보를 추가합니다.
3. **컴파일 및 실행**:
    - 소스 코드를 컴파일하여 실행합니다. 어노테이션에 따라 컴파일러나 런타임에 추가적인 작업이 수행될 수 있습니다.
4. **어노테이션 프로세서 사용(선택 사항)**:
    - 어노테이션 프로세서를 사용하여 컴파일 시에 어노테이션에 대한 추가적인 작업을 수행할 수 있습니다. 이 작업은 컴파일 시점에 이루어지며, 주로 코드 생성이나 코드 변환 등을 수행합니다.
5. **런타임(Runtime) - 리플렉션을 이용한 어노테이션 활용**:
    - 런타임 시점에서 클래스의 메타데이터를 분석하고 처리하는 데에 사용됩니다.
    - 주로 리플렉션(Reflection)을 사용하여 클래스에 적용된 어노테이션 정보를 읽고 이에 따른 동적인 작업을 수행합니다.
    - 예를 들어, 스프링 프레임워크에서는 **`@Autowired`**, **`@RequestMapping`** 등의 어노테이션을 사용하여 런타임 시에 빈(Bean)을 주입하거나 HTTP 요청과 컨트롤러 메서드를 매핑합니다.

어노테이션 방식은 일반적인 객체지향 프로그래밍 스타일의 코드나 패턴 등에 적용되지 못했습니다. 하지만 **어노테이션은 스프링 프레임워크와 같이 IoC 방식의 프레임워크에서 프레임워크가 참조하는 메타정보로 많이 사용되고 있습니다.** 이는 그러한 방식으로 쓰였을 때, 몇가지 유리한 점이 있기 때문입니다.

## XML과 Annotation

```xml
<x:special target="type" class="com.mycompany.myproject.MyClass" />
```

```java
@Special
public class MyClass{...}
```

### XML 장단점

**XML**

- 장점
    - 작성하기 편합니다
    - 빌드 과정이 필요없습니다
    - 순수 자바 코드로 작성하는 것보다 간결합니다
- 단점
    - XML은 특정 구조에 따라야 하며, 자바 코드와의 호환성을 유지하기 위해 추가적인 구문을 사용해야 할 수 있습니다
    - 텍스트 기반으로 작성되므로 타이핑 오류가 발생할 수 있습니다.

**Annotation**

- 장점
    - XML보다 훨씬 간결합니다
    - 컴파일 타임에 검증이 가능합니다.
- 단점
    - 변경할 때마다 매번 클래스를 새로 컴파일해줘야 합니다.
    - 소스 코드에 들어가기 때문에 설정정보의 변경을 위해선 소스 코드에 접근해야만 합니다.
    

### @ComponentScan

- 스프링은 지정된 패키지를 스캔하여 클래스를 찾습니다. 이때 리플렉션을 사용하여 클래스의 메타데이터를 읽어옵니다.
- 스캔된 클래스 중에서 **`@Component`**, **`@Service`**, **`@Repository`**, **`@Controller`** 등의 어노테이션이 붙은 클래스를 찾아서 빈으로 등록합니다.
- 어노테이션 정보를 읽을 때에는 클래스의 **`getAnnotations()`** 메서드를 사용하여 해당 클래스에 적용된 모든 어노테이션을 가져올 수 있습니다.

### @Component

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Indexed
public @interface Component {

	/**
	 * The value may indicate a suggestion for a logical component name,
	 * to be turned into a Spring bean in case of an autodetected component.
	 * @return the suggested component name, if any (or empty String otherwise)
	 */
	String value() default "";

}
```

스프링에서 컴포넌트 스캔을 위해 사용되는 @Component의 일부 내용입니다.
해당 어노테이션이 붙은 클래스를 컴포넌트로 인식하고, 해당 클래스의 인스턴스를 빈(Bean)으로 등록합니다. **@Controller, Service, Repository 모두 위 사진과 같은 코드가 포함되 있습니다.**

- **`@Controller`**: 웹 애플리케이션의 컨트롤러로 사용되며, HTTP 요청을 처리하고 응답을 생성합니다.
- **`@Service`**: 비즈니스 로직을 수행하는 서비스 클래스로 사용되며, 주로 트랜잭션 처리가 필요한 비즈니스 로직을 포함합니다.
- **`@Repository`**: 데이터베이스와의 상호 작용을 담당하는 데이터 접근 계층 클래스로 사용되며, 주로 CRUD 작업을 수행합니다.

### @Autowired

**`@Autowired`** 어노테이션은 스프링 프레임워크에서 의존성을 주입(Dependency Injection)할 때 사용됩니다. 스프링은 **`@Autowired`** 어노테이션이 붙은 필드, 생성자, 메서드를 스캔하여 해당하는 빈(Bean)을 찾아서 주입합니다.

**필드 주입(Field Injection)**:

- **`@Autowired`** 어노테이션을 필드 위에 붙여서 사용합니다.
- 스프링은 해당 필드의 타입에 해당하는 빈을 찾아서 자동으로 주입합니다.

```java
@Component
public class MyService {
    @Autowired
    private MyRepository myRepository;
}
```

**생성자 주입(Constructor Injection)**:

- 생성자에 **`@Autowired`** 어노테이션을 붙여서 사용합니다.
- 스프링은 해당 생성자의 매개변수에 해당하는 빈을 찾아서 자동으로 주입합니다.

```java
@Service
public class MyService {
    private final MyRepository myRepository;

    @Autowired
    public MyService(MyRepository myRepository) {
        this.myRepository = myRepository;
    }
}
```

**메서드 주입(Method Injection)**:

- 메서드의 매개변수에 **`@Autowired`** 어노테이션을 붙여서 사용합니다.
- 스프링은 해당 메서드의 매개변수에 해당하는 빈을 찾아서 자동으로 주입합니다.

```java
@Component
public class MyService {
    private MyRepository myRepository;

    @Autowired
    public void setMyRepository(MyRepository myRepository) {
        this.myRepository = myRepository;
    }
}
```

### @Bean, @Configuration - 빈 수동 등록

**`@Bean`** 어노테이션은 스프링 프레임워크에서 빈(Bean)을 등록할 때 사용됩니다. 일반적으로 **`@Configuration`** 어노테이션이 붙은 클래스에서 메서드에 **`@Bean`** 어노테이션을 사용하여 해당 메서드가 생성하는 객체를 빈으로 등록합니다.

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean
    public MyService myService() {
        return new MyService();
    }
}
```

위의 코드에서 **`AppConfig`** 클래스는 **`@Configuration`** 어노테이션이 붙어 있으므로 스프링에게 이 클래스가 설정 정보를 제공하는 클래스임을 알려줍니다. **`myService()`** 메서드에 **`@Bean`** 어노테이션이 붙어 있으므로 이 메서드가 생성하는 **`MyService`** 객체를 스프링 빈으로 등록합니다.

## 질문

```
Lombok 라이브러리에 대해 알고 있나요? 알고 있다면 롬복이 만드는 메소드들이 생성되는 시점은 언제인가요?

**-> Lombok은 메소드를 컴파일 하는 과정에 개입해서 추가적인 코드를 만들어냅니다. 이것을 어노테이션 프로세싱이라고 하는데, 어노테이션 프로세싱은 자바 컴파일러가 컴파일 단계에서 어노테이션을 분석하고 처리하는 기법을 말합니다.(Lombok 라이브러리를 추가할 때 CompileOnly, AnnotationProcessor를 추가하는 이유도 된다.)**
```

```
Spring의 대표적인 Annotation은 무엇이 있나요?

-> @Component, @ComponentScan, @Bean, @Controller, @RequestHeader, @RequestMapping, @RequestParam,
@ModelAttribute, @ResponseBody, @Autowired, @GetMapping, @PostMapping 등이 있습니
```

```
Annotation에 대해 설명해주세요

-> 어노테이션(Annotation)은 자바 프로그래밍 언어의 기능 중 하나로, 
소스 코드에 메타데이터를 추가하는 방법입니다. 
이 메타데이터는 컴파일러나 개발 도구에게 특정 작업을 수행하도록 지시하거나, 
런타임 시에 프로그램의 동작을 변경하도록 지시하는 데 사용됩니다. 
어노테이션은 주석(comment)과 비슷하게 보이지만, 프로그램에 직접적인 영향을 미치는 반면 
주석은 컴파일 및 실행 시 무시됩니다.
```

```
리플렉션에 대해 설명해주세요

-> 리플렉션(Reflection)은 프로그램이 자기 자신을 검사하고 수정할 수 있는 능력을 말합니다. 
즉, 실행 중인 프로그램의 클래스, 메서드, 필드 등의 정보를 분석하고 조작할 수 있는 기능을 제공합니다.
리플렉션은 자바의 특성 중 하나이며, 자바의 모든 객체는 런타임 시에 해당 객체의 클래스와 관련된 정보를 확인할 수 있습니다.
```

```
@Bean과 @Component의 차이점에 대해 설명해주세요

-> @Bean은 설정 정보 클래스(@Configuration)에 스프링 빈을 정의할 때 메소드 레벨에서 사용됩니다. 개별 클래스나
외부 라이브러리에 의해 제공되는 객체를 빈으로 등록할 때 사용됩니다.
@Component는 클래스 레벨에 사용되며, 해당 클래스를 스프링 빈으로 등록합니다.
주로 자동 컴포넌트 스캔을 통해 스프링 빈을 자동으로 등록할 때 사용됩니다.
보통 개발자가 직접 정의한 클래스에서 사용됩니다.

간단히 말하면, @Bean은 개발자가 직접 빈을 정의하고 구성할 때 사용되고, @Component는 자동으로 스캔되어 스프링 컨테이너에 의해 관리되는 클래스에 사용됩니다.
```

---

참조

[Java에서 어노테이션(Annotation) 이란 무엇인가에 대해 알아보자.](https://honeyinfo7.tistory.com/56)

[[Spring Boot] 어노테이션 정리](https://sddev.tistory.com/225)

[[Spring] @Controller, @Service, @Repository 란? (feat.@Component)](https://wooj-coding-fordeveloper.tistory.com/69)

[신입 개발자 기술면접 질문 정리 - 백엔드](https://dev-coco.tistory.com/163)

[[기술면접] 신입이 준비하는 Spring 기술 면접](https://geminihoroscope.tistory.com/90)