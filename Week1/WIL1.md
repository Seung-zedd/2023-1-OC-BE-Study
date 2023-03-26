# <section 2> 스프링 웹 개발 기초

section: Spring(김영한)
생성일: 2023년 3월 25일 오후 9:06

[스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술 v2022-11-28.pdf](section%202%20%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%2064e9a7a80a004c0c885150e1ca498c7a/%25EC%258A%25A4%25ED%2594%2584%25EB%25A7%2581_%25EC%259E%2585%25EB%25AC%25B8_-_%25EC%25BD%2594%25EB%2593%259C%25EB%25A1%259C_%25EB%25B0%25B0%25EC%259A%25B0%25EB%258A%2594_%25EC%258A%25A4%25ED%2594%2584%25EB%25A7%2581_%25EB%25B6%2580%25ED%258A%25B8_%25EC%259B%25B9_MVC_DB_%25EC%25A0%2591%25EA%25B7%25BC_%25EA%25B8%25B0%25EC%2588%25A0_v2022-11-28.pdf)

# 📚Topic covered

1. 정적 컨텐츠
2. MVC와 템플릿 엔진
3. API
4. RESTful API란?

# 📖**정적 컨텐츠(static)**

- 서버에서 파일(welcome page)을 웹 브라우저에 **그대로** 내려줌
- 스프링부트는 정적 컨텐츠를 자동으로 제공함 (관련된 reference)🔽

[Spring Boot Features](https://docs.spring.io/spring-boot/docs/2.3.1.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-static-content)

### 작동 원리

![Untitled](section%202%20%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%2064e9a7a80a004c0c885150e1ca498c7a/Untitled.png)

1. 웹 브라우저에 UML을 타이핑하면(request) 서버가 이를 알아듣고(reply) html 파일을 보낼 준비를 함
2. 톰캣 서버는 hello-static 관련 컨트롤러가 없으면, resources안에 있는 `static/hello-static.html` 을 찾아서 클라이언트한테 html파일을 보내주고, 파일의 내용을 브라우저에 display함

🧐참고로, **Controller**에서는 리턴값의 value를 보고, templates에 있는 URL(Uniform Resources Locator)의 hello.html을 찾아서 **렌더링**함

```java
public String hello(Model model){
        model.addAttribute("data", "hello!!"); // (key, attributeValue)
        return "hello";
```

즉, ${data}라는 키 값을 찾으면, value를 리턴하기 때문에 웹페이지에서 *안녕하세요. hello!!*이 나옴

👉static content 중에 “static” 키워드를 보니, java에서 런타임 환경에서 생성한 인스턴스말고, static으로 씌워서 컴파일 단계에서 빌드되는 것과 비슷한 원리인 것 같다.

# 📖MVC와 템플릿 엔진(dynamic)

### 🔖MVC

- Model, View, Controller의 약자인데, view는 **화면**을 그리는데 모든 역량을 집중함.

반면에 Model, Controller는 **비즈니스 로직(내부적인 로직)**을 처리하는데 집중을 함

### 🔖템플릿 엔진

- 서버에서 프로그래밍을 해서 html을 동적으로 변경해 주는 것을 말함
    - 강의 내용에서는 thymeleaf가 템플릿 엔진에 해당

자, 그럼 앞의 resource안의 static과 어떻게 다르게 동작하는지 파헤쳐보자. 

[`http://localhost:8080/hello-mvc?name=spring](http://localhost:8080/hello-mvc?name=spring)!!!!!`  를 입력하고 view를 다시 실행한 결과🔽

![Untitled](section%202%20%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%2064e9a7a80a004c0c885150e1ca498c7a/Untitled%201.png)

- 우클릭 → 페이지 소스를 보면, <html> 태그로 된 소스코드들을 볼 수 있는데, 이런 방식을 **XML방식**이라고 한다.

### 작동 원리

![Untitled](section%202%20%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%2064e9a7a80a004c0c885150e1ca498c7a/Untitled%202.png)

- 앞의 static contents는 정적으로 컴파일된 내용을 그대로 렌더링해주는 것과 달리, controller에서는 ?(query string) 옆에 있는 name의 value값을 변경해주면, **view도 동적으로 변한다.**

# 📖API

### 🔖API

- 안드로이드나 아이폰 클라이언트를 개발하면, **서버 입장**에서는 html파일 그대로가 아니라, **json 데이터포맷**으로 클라이언트한테 파일을 내려줌

```java
@GetMapping("hello-string")
    @ResponseBody // http의 바디부에 return 값을 직접 대입하겠다.
    public String helloString(@RequestParam("name") String name){
        return "hello " + name;
    }
```

![Untitled](section%202%20%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%2064e9a7a80a004c0c885150e1ca498c7a/Untitled%203.png)

- 앞의 템플릿 엔진과 비교하자면, API는 **html 태그 이런거 없이** 내용 그대로 올려준다.
    
    👉이러한 방식을 **JSON**이라고 한다.
    
- `@ResponseBody`를 사용하면 viewResolver를 사용하지 않고 HttpMessageConverter가 동작함
    - 이렇게 하면, 템플릿 엔진을 일일이 플러그인 할 필요가 없음

앞에서는 String방식으로 API를 만들었다면, 지금부터는 **실무에서 가장 자주 사용되는 객체의 json파일로 리턴하는 방식**을 살펴볼 것이다.

```java
@GetMapping("hello-api")
    @ResponseBody
    public Hello helloApi(@RequestParam("name") String name){
        Hello hello = new Hello();
        hello.setName(name); // setter 메소드
        return hello; // 객체가 오면 json파일로 리턴함 
    }

    static class Hello{
        private String name;

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }
    }
```

![Untitled](section%202%20%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%2064e9a7a80a004c0c885150e1ca498c7a/Untitled%204.png)

- 앞의 “hello-string”과 달리, “hello-api”는 hello라는 인스턴스를 생성해서 그대로 리턴해줬는데, JsonConverter는 **{key: value}와 같은 Json 데이터포맷**으로 웹 브라우저에 내용을 내려준다.

### 작동 원리

![Untitled](section%202%20%E1%84%89%E1%85%B3%E1%84%91%E1%85%B3%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%20%E1%84%80%E1%85%B5%E1%84%8E%E1%85%A9%2064e9a7a80a004c0c885150e1ca498c7a/Untitled%205.png)

- 문자면 StringConverter로, 객체면 JsonConverter로 동작한다. 즉 view이런 거 없이 HttpMessageConverter에서 html파일을 내려준다는 의미

# 정리)

1. static contents는 welcome webpage와 같이, 컴파일 단계에서 작성된 소스코드 내용을 그대로 내려줌
2. MVC와 템플릿 엔진은 viewResolver를 통해 Controller의 내용을 view로 보여줌
3. 마지막으로 API는 `@ResponseBody` annotation을 추가로 작성하여 String방식과 Json방식 2가지로 변환해주는데, 실무에서는 Json방식을 주로 사용하기 때문에 **객체(인스턴스)를 리턴하는 습관**을 들이자!

# 📖RESTful API란?

[rest-apis](https://www.ibm.com/kr-ko/cloud/learn/rest-apis)

- REST(*REpresentational State Transfer, 즉* 자원을 정의하고 자원에 대한 주소를 지정하는 방법 전반을 의미) 아키텍처 스타일의 디자인 원칙을 준수하는 API로서,

API(Application Programming Interface)는 애플리케이션이나 디바이스가 서로 간에 연결하여 통신할 수 방법을 정의하는데, 여기에 **개발자에게 비교적 높은 수준의 유연성과 자유를 제공**한다고 해서 RESTful API라고 한다.

- API: 하나의 애플리케이션이나 서비스가 다른 애플리케이션이나 서비스 내의 **리소스에 액세스할 수 있도록 해주는 메커니즘**
- clients: 액세스를 수행하는 앱이나 서비스를 의미
- server: 리소스가 포함된 앱이나 서비스

## 📑REST 디자인 원칙

1. 균일한 인터페이스: 사용자의 이름이나 이메일 주소 등의 동일한 데이터 조각(또는 동일한 리소스)이 오직 하나의 **URI(Uniform Resource Identifier,** 인터넷상의 리소스 **“자원 자체”**를 식별하는 고유한 문자열 시퀀스**)**에 속함을 보장
2. **클라이언트**-**서버** 디커플링: 클라이언트와 서버 앱 간에 서로 **독립적**이어야 한다는 말로, 클라이언트 앱은 요청된 리소스의 **URI**만, 서버 앱은 **HTTP를 통해 요청된 데이터**만 알아야하고, 서로 상호작용할 수 없다는 의미임
3. Stateless: 서버측 세션을 추가로 필요로 하지 않으며. 서버 애플리케이션은 클라이언트 요청과 관련된 **데이터를 저장할 수 없다.**
4. 캐싱 가능성: 서버 응답에는 전달된 리소스에 대해 캐싱이 허용되는지 여부에 대한 정보도 포함되어야 하며, 목적은 서버측의 **확장성 증가**와 함께 클라이언트측의 **성능 향상**을 동시에 얻는 것
5. 계층 구조 아키텍처: **호출**과 **응답**이 서로 다른 계층을 통과하며, 중개자(network core)와 통신하는지 여부를 클라이언트나 서버가 알 수 없도록 설계되어야 한다.

## 📑REST API의 작동 방식

- HTTP 요청을 통해 통신함으로써 리소스 내에서 레코드(**CRUD** 라고도 하며, Create, Read, Update, Delete)의 작성, 읽기, 업데이트 및 삭제 등의 표준 데이터베이스 기능을 수행

**REST API**가 서버에게 다음과 같은 **4가지 HTTP 메소드**를 요청하여 서버에게 **CRUD작업**을 시킬 수 있다.(@는 HTTP 메소드에 대응하는 Spring의 annotation임)

1. Get 요청(**@GetMapping)** → 레코드 검색(Read)
2. Post 요청(**@PostMapping)** → 레코드 작성(Create)
3. Put 요청(**@PutMapping)** → 레코드 업데이트(Update)
4. Delete 요청(**@DeleteMapping)** → 레코드 삭제(Delete)

👉JSON이 인기 있는 이유는, 사람과 기계 모두가 읽을 수 있기 때문이며, 프로그래밍 언어와 무관한 언어이기 때문이기도 하다.

👉또한, REST API는 **보안 면**에서도 탁월한데, 안전한 데이터 전송을 위한 **HTTPS** 및 암호 보안을 위한 **해싱 알고리즘** 사용 등과 같은 업계 우수 사례로 시작되며, **매개변수 검증**과 **JSON 웹 토큰**은 인가된 클라이언트만 API에 액세스할 수 있도록 보장하는 또 다른 방법같은 우수 사례들이 존재한다.