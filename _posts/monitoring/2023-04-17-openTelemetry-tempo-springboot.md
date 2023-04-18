## vscode를 활용한 springboot 환경에서 테스트 진행
---
### 1. vscode 확장 프로그램을 설치합니다. 

- Spring Boot Extension Pack
(확장 설치 후에 jdk 까지 설치 합니다. )
- Extension Pack for Java

![Alt text](Images/sprintbootjava.png)

JDK가 설치되어 있지 않을 경우 아래의 창이 열리며, 다운받아 설치 합니다. 
![Alt text](Images/jdk-install.png)




---
### 2. 기본 프로젝트 생성 
gradle 환경의 프로젝트를 생성하기 위해 아래 확장 설치

![Alt text](Images/extension-gradle-for-java.png)

F1 키를 눌러서 스프링 프로젝트 생성을 시작합니다. 
![Alt text](Images/K-011.png)
![Alt text](Images/K-012.png)
![Alt text](Images/K-013.png)
![Alt text](Images/K-014.png)
![Alt text](Images/K-015.png)

---

![Alt text](Images/K-016.png)
![Alt text](Images/K-017.png)
![Alt text](Images/K-018.png)

---
### 3. Swagger 추가

API 테스트를 위하여 Swagger 프레임워크를 추가합니다. 

소스수정내역
1. ExampleController.java 추가 후 아래 코드 입력

``` java
package com.example.demo;
import io.swagger.v3.oas.annotations.Hidden;
import io.swagger.v3.oas.annotations.Parameter;
import io.swagger.v3.oas.annotations.tags.Tag;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@Tag(name = "예제 API", description = "Swagger 테스트용 API")
@RestController
@RequestMapping("/")
public class ExampleController {
    // @Operation(summary = "문자열 반복", description = "파라미터로 받은 문자열을 2번 반복합니다.")
    @Parameter(name = "str", description = "2번 반복할 문자열")
    @GetMapping("/returnStr")
    public String returnStr(@RequestParam("str") String str) {
        return str + "\n" + str;
    }

    @GetMapping("/example")
    public String example() {
        return "예시 API";
    }

    @Hidden
    @GetMapping("/ignore")
    public String ignore() {
        return "무시되는 API";
    }
}
```

2. application.properties 수정 (따로 추가하지 않아도 사용가능)
``` properties
springdoc.packages-to-scan=com.example.demo
springdoc.default-consumes-media-type=application/json;charset=UTF-8
springdoc.default-produces-media-type=application/json;charset=UTF-8
springdoc.swagger-ui.path=/
springdoc.swagger-ui.disable-swagger-default-url=true
springdoc.swagger-ui.display-request-duration=true
springdoc.swagger-ui.operations-sorter=alphaapplication
```

3. build.gradle 에 의존성추가.
``` gradle
dependencies {
    //springdoc 의존성추가.
	implementation 'org.springdoc:springdoc-openapi-starter-webmvc-ui:2.0.2' 
	...
}
```
수정하는 내역은 아래 형광색으로 표시했습니다. 

![Alt text](Images/gradle-swagger-springdoc-project-tree.png)

이후 gradle extention 에서 

Tasks -> application -> bootRun 으로 실행 후 
브라우저를 실행하여 localhost:8080 으로 정상동작을 확인합니다. 

![Alt text](Images/K-019.png)

<b>※ 앞으로 서버로 실행가능한 jar 파일을 만들기 위해서는 Tasks -> build -> bootJar으로 빌드 합니다. </b>

![Alt text](Images/gradle-extension-tree.png)

---
### 4. opentelemetry를 통한 Grafana Tempo 자동계측 추가

 1. OpenTelemetry Instrumentation for Java 다운로드
    - 깃허브 페이지 : https://github.com/open-telemetry/opentelemetry-java-instrumentation 
    - 다운로드 파일 : https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases
2. 자바 옵션으로 다운받은 opentelemetry-javaagent-all.jar 파일 추가
    ```
    JAVA_OPTS=-javaagent:/opentelemetry-javaagent-all.jar
    ```

3. 시스템 환경 속성 추가

    윈도우 : 시스템 속성 -> 고급탭 -> 환경변수

    ![Alt text](Images/K-020.png)

    리눅스 : /etc/profile
    ```
    export OTEL_EXPORTER=otlp_span
    export OTEL_EXPORTER_OTLP_ENDPOINT=https://localhost:4317
    export OTEL_EXPORTER_OTLP_INSECURE=true
    export OTEL_RESOURCE_ATTRIBUTES=service.name=SomeName
    ```
    java의 경우 사전에 환경변수등록을 하지 않을 경우 JAVA_OPTS 로 변경 불가능


4. 자바옵션에 환경설정 내용까지 포함하여 스프링서버 실행
    ```
    JAVA_OPTS= -javaagent:/opentelemetry-javaagent-all.jar 
    -Dotel.exporter.otlp.endpoint=http://domain:4317
    -Dotel.resource.attributes=service.name=test
    ```

java의 경우 닷넷과 다르게 환경변수와 옵션으로 opentelemetry 에이전트를 추가해주면 자동계층이 가능해 집니다. 