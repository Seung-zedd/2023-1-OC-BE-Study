# <Chapter 7> 스프링 삼각형과 설정 정보

# 📚Topic Covered

- IoC와 프레임 워크, 라이브러리
- 생성자를 통한 의존성 주입 @Autowired 비교
- AOP

# 📘IoC와 프레임 워크, 라이브러리

### 🤔스프링 프레임워크와 스프링 삼각형의 관계?

👉 영어 문장과 알파벳의 관계, 즉 스프링 삼각형이 스프링 프레임워크의 부분 집합이라고 볼 수 있음

⚠️본인은 스프링의 삼각형이라고 표현하기에는 뭔가 추상적이니, 이해하기 쉽게 스프링의 삼대장이라고 하겠다. 

그럼 지금부터 **스프링의 삼대장(IoC/DI, AOP, PSA)**를 차근차근 소개해보겠다

## 📖IoC/DI: 제어의 역전/의존성 주입

- 전의 입문강의에서, 의존성은 “사용한다”라고 하였다. 그런데 이것을 자바로 표현하자면 **의존성은 new**이고, new를 실행하는 Car와 Tire 사이에서 Car가 Tire에 의존한다라고 볼 수 있다.

```java
new Car();

...
static Class Car {
	Tire tire = new Tire(); // 객체 Car는 의존하는 Tire가 없으면 알맹이가 없다 -> 쉽게 생각하면, 인터페이스와 부품 관계...?
}
...
```

- 사실, 이것은 소프트웨어공학에서는 HAS-A 관계라고 하는데, whole-part 관계, 그리고 전체가 사라지면 부분의 존재 유무에 따라 Aggregation(집합) 또는 Composition(구성) 관계로 나뉜다.
    - Aggregation: 약한 결합력을 가짐, 즉 전체가 사라져도 부분은 존재
    - Composition: 강한 결합력을 가짐, 전체가 사라지면 부분도 사라짐
        
        ex) 비빔밥
        

## 📑new를 사용해 의존성을 주입한 프로젝트

### 💻Tire 클래스 → 브랜드타이어들의 상위클래스

```java
package expert001_01;

public interface Tire {
    String getBrand();
}
```

### 💻KoreaTire 클래스 → 부품 역할

```java
package expert001_01;

public class KoreaTire implements Tire {

    @Override
    public String getBrand() {
        return "코리아 타이어";
    }
}
```

### 💻AmericaTire 클래스 → 부품 역할

```java
package expert001_01;

public class AmericaTire implements Tire{
    @Override
    public String getBrand() {
        return "미국 타이어";
    }
}
```

### 💻Car 클래스 → Tire를 HAS-A 관계로 갖는 클래스(Composition)

```java
package expert001_01;

public class Car {
    Tire tire; // Tire 클래스 객체 생성

    public Car() {
        tire = new KoreaTire();
        // tire = new AmericaTire();
    }

    public String getTireBrand() {
        return "장착된 타이어: " + tire.getBrand();
    }
}
```

### 💻Driver 클래스 → Car를 사용하는 객체

```java
package expert001_01;

public class Driver {
    public static void main(String[] args) {
        Car car = new Car(); 
        System.out.println(car.getTireBrand()); 
	// O/P: 장착된 타이어: 코리아 타이어
    }
}
```

- 운전자는 Car 객체를 생성하는데, car의 메소드를 호출하면 Car 클래스는 Tire클래스를 의존하기 때문에, Tire 인터페이스에 해당하는 메소드가 실행될 것이다
    - 이때, KoreaTire 클래스는 Tire 인터페이스의 메소드를 오버라이딩 했기 때문에, 출력 결과는 `장착된 타이어: 코리아 타이어` 가 나올 것이다

## 📑new를 사용하지 않고 생성자를 통해 의존성 주입

- 말그대로 객체를 생성할 때 new를 사용하지 않고, 고차원의 모듈(여기서는 Driver)에서 new를 통해 인스턴스를 생성할 때 인자에 의존하는 클래스를 넣으면 된다

### 💻TIre 인터페이스

```java
package expert001_02;

public interface Tire {
    String getBrand();
    }
```

### 💻KoreaTire 클래스

```java
package expert001_02;

public class KoreaTire implements Tire {

    @Override
    public String getBrand() {
        return "코리아 타이어";
    }
}
```

### 💻AmericaTire 클래스

```java
package expert001_02;

public class AmericaTire implements Tire{

    @Override
    public String getBrand() {
        return "미국 타이어";
    }
}
```

### 💻Car 클래스

```java
package expert001_02;

public class Car {
    Tire tire;

    public Car(Tire tire) {
        this.tire = tire; // new를 사용하여 의존성을 주입하는 대신 생성자 파라미터에 의존하는 Tire 클래스를 넣음
    }

    public String getTireBrand() {
        return "장착된 타이어: " + tire.getBrand();
    }
}
```

### 💻Driver 클래스

```java
package expert001_02;

public class Driver {
    public static void main(String[] args) {
        Tire tire = new KoreaTire(); // Tire클래스 다형성을 활용한 KoreaTire 객체 생성
        // Tire tire = new AmericaTire();
        Car car = new Car(tire); // car 객체 생성

        System.out.println(car.getTireBrand()); // O/P: 코리아 타이어
    }
}
```

## 📖생성자를 통해 의존성을 주입한 코드의 장점

- Driver의 **하위 모듈인 Car 클래스**가 new를 통해 KoreaTire를 생산할지 AmericaTire를 생산할지 결정하면 **코드의 유연성이 떨어짐!**

우리는 스프링 강의에서 DIP(Dependency Inversion Principle)원칙이 객체에서 어떤 Class를 직접 참조하는 것이 아니라 그 대상의 **상위 요소(추상 클래스 or 인터페이스)로 참조하라는 원칙**이라고 배웠었다.

🔎그렇다면, 이 예제를 적용해보자면, Car가 의존하는 Tire 클래스를 직접 참조(또는 의존)하는 것이 아니라, 생성자의 인자에 의존하는 Tire 클래스를 주입함으로써 상위 모듈인 Driver 클래스에서 Car 객체를 생성하면 자동으로 Tire 클래스를 참조할 수 있다는 것이다.

👉이외에도, 기존의 모듈화된 것 말고 **새로 모듈로 만든 코드만 배포**하면 되므로, **컴파일 및 빌드의 비용을 줄일 수 있다는 장점**이 있음!

## 📖스프링을 통한 의존성 주입: XML 파일 사용

- Driver 클래스의 코드만 달라졌으므로, 이것만 다룬다

### 💻Driver 클래스

```java
package expert002;

// 종합 쇼핑몰(스프링 프레임워크)에 대한 정보를 가지고 있는 패키지
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Driver {
    public static void main(String[] args) {
        // 종합 쇼핑몰에 대한 정보
        ApplicationContext context = new ClassPathXmlApplicationContext("expert002/expert002.xml");
        
        // 종합 쇼핑몰에서 Car를 구매
        Car car = context.getBean("car", Car.class); 

        // 종합 쇼핑몰에서 Tire를 구매
        Tire tire = context.getBean("tire", Tire.class);

        car.setTire(tire);

        System.out.println(car.getTireBrand());
    }
}
```

- getBean을 통해 객체를 가져올 수 있음

### 📄expert002.xml

```xml
 <?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="tire" class="expert002.KoreaTire"></bean>

    <bean id="americaTire" class="expert002.AmericaTire"></bean>

    <bean id="car" class="expert002.Car"></bean>

</beans>
```

### 장점

- 모듈을 재컴파일/재배포하지 않아도 XML파일만 수정하면 프로그램의 실행 결과를 바꿀 수 있음

🤔그런데, XML파일은 태그가 있어서 가독성이 떨어져 @Responsebody 어노테이션을 통해 XML을 JSON파일로 변환해 사용한다고 한다

- 또한, 모듈을 변경하고자 할 때, 클래스의 코드를 변경하는 것이 아닌 `<bean id="name" ... </bean>` 의 이름 태그만 변경해주면 되기 때문에 아주 간편하다.

## 📖스프링을 통한 의존성 주입: @Autowired를 통한 속성 주입

- @Autowired는 setter 메소드로 tire 속성을 초기화해주지 않고 강제로 의존 클래스에 의존성을 주입하는 것을 말하는데, 이것이 아래와 같이 스프링 빈과 관련이 있다.

> @Autowired는 **type 매칭**을 우선으로 하고, 그 다음에 XML파일에서 작성한 “bean id”로 매칭을 한다.
> 

🤔만약 bean id가 없고 Tire 인터페이스를 구현한 같은 하위클래스가 있으면 어떻게 될까?

❗답은, **No unique bean이라는 에러**가 뜨면서 객체를 관리해 줄 수가 없다

👉따라서, type class 말고도, 항상 bean 태그의 id 속성을 작성하는 습관을 들이자.

# 📘AOP(Aspect-Oriented Programming)

- 스프링 DI가 new를 통한 의존성 주입이라면, 스프링 AOP는 로직(code)의 주입이라고 할 수 있다.

### 🔖횡단 관심사(cross-cutting concern)

- 다수의 모듈에 **공통적**으로 나타나는 부분이 **반복적으로 존재**하는 것
    - mySQL같은 DBMS 시스템에서 insert, update, delete, select 쿼리 연산자가 항상 반복해서 등장하는 것도 포함

> 반복/중복은 분리해서 한 곳에서 관리하라
> 
- 코드 = 핵심 관심사 + 횡단 관심사,  여기서 핵심 관심사는 모듈별로 특정한 코드가 작성된 곳을 말한다. 예를 들면, 남자와 여자라는 객체가 있는데 둘의 코드 진행 순서는 다음과 같다
    
    남자: 열쇠로 문을 열고 집에 들어간다 → 컴퓨터로 게임을 한다 → 소등하고 잔다 → 자물쇠를 잠그고 집을 나선다
    
    여자: 열쇠로 문을 열고 집에 들어간다 → 요리를 한다 → 소등하고 잔다 → 자물쇠를 잠그고 집을 나선다
    

위와 같이 다른 객체에 공통적으로 나타나는 부분을 횡단 관심사라고 하고, 밑줄 친 특별한 부분을 핵심 관심사라고 한다