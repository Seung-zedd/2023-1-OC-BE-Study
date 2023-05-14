# <Chapter 4> 자바가 확장한 객체 지향

# 📚Topic Covered

- 추상 클래스와 인터페이스
- static과 final 그리고 불변 객체

# 📘abstract 키워드 - 추상 메서드와 추상 클래스

## 📖추상 메서드

- 반환형 앞에 `abstract` 키워드를 붙여서 선언할 수 있으며, **바디가 없는 함수**를 의미한다.
    - 이것은 추후에 배울 인터페이스와 매우 깊은 관련이 있는데, 즉 상속받은 **자식클래스에서 부모클래스의 함수를 강제로 오버라이딩**시키는 역할을 한다.
        
        👉쉽게 말하면, 인터페이스는 이렇게 함수를 구현하라는 guide 역할
        

### 💻추상 메서드를 1개 이상 포함한 추상클래스 예시코드

```java
public abstract class Animal { // abstract로 선언된 추상클래스
	abstract void cry(); // body가 없는 추상메서드
}
```

### 💻고양이 subclass의 예시코드

```java
public class Cat extends Animal {
	void cry() {
	System.out.println("Meow"); // cry()를 오버라이딩해서 "Meow"가 출력
}
	
```

---

## 📖추상 클래스(abstract class)

- **new 키워드로 객체를 새로 만들 수 없는 클래스**를 말한다. 이것 역시 추상 메서드와 마찬가지로 하위클래스를 이렇게 만들라는 guide 역할을 해준다.
    - 또한, 인스턴스를 만들지 않아 메모리를 절약할 수 있는 장점이 있다

⚠️**추상 메서드**를 하나라도 갖고 있는 클래스는 반드시 **추상클래스로 선언**해야한다 → 역시 인터페이스와 관련있음!

# 📘생성자(객체 생성자 메서드가 정확한 용어)

- 웬만한 것들은 이미 C++에서 다뤄서 생략했는데, Java에서 꼭 기억해둬야 할 생성자의 특징이 있는데, 반드시 유념해두자
    
    1) 개발자가 아무런 생성자도 만들지 않으면, 자바는 인자가 없는 디폴트 생성자를 자동으로 만들어준다.
    
    👉이건 C++도 마찬가지이므로 쉬움!
    
    2) **인자가 있는 생성자를 하나라도 만든다면, 자바는 기본 생성자를 만들어 주지 않는다.**
    
    👉이것은, 함수 오버로딩과 관련이 있는데, 디폴트 생성자와 인자가 있는 생성자는 함수 시그니처가 다르기 때문에 다르게 인식
    

# 📘static과 final 그리고 불변 객체

## 📖클래스 생성 시의 실행 블록, static 블록

- 우리가 아무생각 없이 `public static void main(String[] args)` 로 치는 것 중(혹은 자동완성) static 키워드가 **바로 static 블록에 메소드를 생성하는 행위**다. 이는 JVM(Java Virtual Machine)이 메모리를 직접 생성하지 않으며, `static` 이라는 특성때문에 프로그램이 종료될 때까지 Method 영역에는 main 메소드가 살아있다

### 🔖JVM이 담당하는 메모리 영역

1. Heap 영역: **객체와 인스턴스 데이터**를 저장함 Java의 *가비지 컬렉터(Garbage Collector)*가 이 영역의 메모리를 관리
2. Stack 영역: **메서드 호출**과 **로컬 변수**를 저장
3. Method 영역: **클래스**와 **메서드** 정보, **상수**, **정적 변수**를 저장

👉C와 같은 low-level 메모리 구조를 고려할 필요가 없음(BSS 영역과 같은)

- 또한, 자바는 순서대로 명령어를 처리하는 절차지향적인 언어이기 때문에, 해당 패키지 또는 클래스가 처음으로 사용될 때 메모리가 로딩되는데, 다음과 같은 예시코드를 보며 이해해보자

### 💻동물 클래스

```java
package staticBlock;

public class 동물 {
    static{
        System.out.println("동물 클래스 레디 온!");
    }
}
```

### 💻Driver03 코드

```java
package staticBlock; 

public class Driver03 {
    public static void main(String[] args) {
        System.out.println("main 메서드 시작!"); // code#1
        동물 뽀로로 = new 동물(); // code#2
    }
}
```

- 명령어의 순서를 보면, JVM에 의해 Method 영역에서 main함수와 static으로 선언된 동물클래스가 생성되는데,

main 메소드 실행 → 동물 클래스에 접근 후, sout 메소드 실행

---

또한, **static 블록**은 인스턴스가 여러 개 생성되더라도, **한 번만 실행된 다는 것**을 다음 코드를 통해 알 수 있다

### 💻Driver04

```java
package staticBlock;

public class Driver04 {
    public static void main(String[] args) {
        System.out.println("main 메서드 시작!"); // code#1
        동물 뽀로로 = new 동물(); // code#2
        동물 피카츄 = new 동물();
    }
}
```

---

그렇다면, 클래스의 static 속성에 접근하는 예제코드를 살펴보자.

⚠️static 클래스가 아니라, 클래스 레벨에서 사용되는(field, method) static임을 잊지말자

### 💻Driver05

```java
package staticBlock;

public class Driver05 {
    public static void main(String[] args) {
        System.out.println("main 메서드 시작!"); // code#1
        System.out.println(Animal.age);
    }
}

    class Animal {
        static int age = 0; // code#3

        static {
            System.out.println("Animal class ready on!"); // code#2
        }
    }
```

- 순서는, main메소드 호출 → Animal 클래스에 접근 후, sout 메소드 호출(왜냐하면, 객체 생성자 메소드를 먼저 호출하기 때문) → Animal 클래스의 정적 필드에 접근

## 📖final 키워드

- 자바에서는 클래스, 필드(또는 속성이나 변수), 메서드와 같이 3가지 영역에서만 `final` 키워드를 선언할 수 있다.

### 1) final과 클래스

- `public final class 고양이{}` 와 같이 클래스 왼쪽에 선언하면, **상속을 허락하지 않겠다**는 의미

### 2) final과 필드(클래스 변수, 인스턴스 변수를 아우름)

- C++와 마찬가지로, final은 선언과 동시에 특정 값으로 초기화해줘야한다.
- 한번 초기화했으면, **그 값을 변경할 수 없다**

### 3) final과 메소드

- 자식클래스에서 final이 선언된 메소드를 **오버라이딩 할 수 없음**

## 📖instanceof 연산자

- instanceof 연산자는 만들어진 **객체가 특정 클래스의 인스턴스인지 물어보는 연산자**로, true 또는 false를 리턴한다

> 객체_참조_변수 instanceof 클래스명
> 

예) `동물 동물객체 = new 동물();`

`System.out.prinln(동물객체 instanceof 동물);` // true

⚠️instanceof의 리턴값은 객체 참조 변수의 타입이 아닌 **실체 객체의 타입에 의해 처리**한다

`동물 조류객체 = new 조류();` 는 다음과 같은 이유로 **조류의 인스턴스**임과 동시에 **동물의 인스턴스**다

1. 조류 클래스를 new 키워드로 선언함으로써 힙 영역에 객체를 생성한다. 그래서 객체 참조변수인 조류객체로 조류 클래스 영역에 접근할 수 있다
2. 조류 클래스는 동물 클래스를 상속받았기 때문에, **자동으로 부모클래스의 인스턴스**이기도하다(*by 객체 지향 프로그래밍의 상속 원칙*)

이러한 상속 원칙을, 똑같이 인터페이스 구현 관계에서도 적용할 수 있다.

### 💻박쥐, 참새 → Flyable 인터페이스

```java
package instanceOf02;

interface Flyable {

}

class 박쥐 implements Flyable {

}

class 참새 implements Flyable {

}

public class Driver {
    public static void main(String[] args) {
        Flyable 박쥐객체 = new 박쥐();
        Flyable 참새객체 = new 참새();

        System.out.println(박쥐객체 instanceof Flyable);
        System.out.println(박쥐객체 instanceof 박쥐);
        System.out.println(참새객체 instanceof Flyable);
        System.out.println(참새객체 instanceof 참새);
    }
}
```

- 위의 리턴값은 전부 true인데, 호기심삼아 Flyable을 각각 박쥐, 참새 클래스명으로 변경했는데 역시 상속 원칙에 따라 모두 true값을 리턴한다

# 📘interface 키워드와 implements 키워드

- 앞의 추상 클래스를 다루면서 interface를 잠깐 소개했지만, 이것만은 짚고 넘어가자.

> 인터페이스는 **public 추상 메소드**와 **정적 상수**만 가질 수 있다
> 

인터페이스 코드에서 작성하면 알겠지만, 굳이 `public static final` 을 데이터 타입 앞에 선언할 필요가 없는데, 이는 자바 개발자의 세심한 배려(?)덕분이다

- 또한, 정적 상수(static final variable)만 가질 수 있는 이유는, new로 객체를 생성하지 않기 때문에, 힙 영역이 아닌 Method 영역에서 static 블록을 만들기 때문

## 📖implements 키워드

- 하위클래스가 인터페이스를 상속받을 때 쓰이는 키워드로, 구상클래스(Concrete class)는 `extends` 를 쓰는 것과 다르다는 것을 유념해두자

# 📘this 키워드

- 자기 자신을 지칭하는 키워드로, 정확히는 **객체의 인스턴스를 참조하는 키워드**다.
    - 지역 변수와 속성(객체 변수, 정적 변수)의 이름이 같은 경우 지역 변수가 우선한다

# 📘super 키워드

- super키워드로 상위클래스에 접근을 할 수는 있지만, super.super 형태로 부모가 아닌 **조부모 클래스의 인스턴스에는 접근이 불가능**하다.

# 📘불변 객체

[[Java] 불변 객체(Immutable Object) 및 final을 사용해야 하는 이유](https://mangkyu.tistory.com/131)

⚠️객체 지향 책만으로는 이해가 안되서, “망나니개발자”님의 티스토리를 참고해보았다

### 🤔불변 객체란?

- 객체 생성 이후 내부의 상태가 변하지 않는 객체를 의미한다
    - **read-only 메소드**만 제공하고, 참조를 통해 값을 수정하면 내부의 데이터값도 변경되기 때문에(∵call-by-reference), **내부를 복사하여 전달하는 call-by-value를 적용한 *방어적 복사(defensive-copy)***를 제공함

### 🔎Java에서의 불변 객체 생성하는 방법

1. 클래스를 final로 선언
2. 모든 클래스 변수를 private과 final로 선언
3. 객체를 생성하기 위한 생성자 또는 정적 팩토리 메소드를 추가
4. 참조에 의해 변경가능성이 있는 경우 방어적 복사를 이용하여 전달

😵3과 4는 *이펙티브 자바*를 배운다음 다시보자…

### ❗불변 객체 및 final을 사용해야 하는 이유

1. **Thread-safe**하여 멀티 쓰레드 환경에서의 병렬 프로그래밍에 유용하고, 동기화를 고려하지 않아도 된다
    
    👉이는, 서로 다른 쓰레드가 ****동일한 자원에 접근할 경우, **Race condition**이 발생하는데, **불변 객체**를 사용한다는 것은 곧 **공유 자원도 불변**이라 **동일한 값을 반환**하기 때문에 동기화를 하지 않아도 된다
    
    - 대표적인 예로, `String name = “Old”;` 와 같이 값을 초기화하면, 그 값을 변경할 수 없다
2. 실패 원자적인(Failure Atomic)메소드를 만들 수 있다
    
    👉가변 객체를 통해 예외가 발생하면 불안정한 상태에 빠질 수 있는 것과 달리, 불변 객체를 사용하면 **메소드 호출 전의 상태 유지 가능**
    
3. Cache나 Map 또는 Set 등의 요소로 활용하기에 더욱 적합
    
    👉한 번 데이터가 저장된 이후에 다른 작업들을 고려하지 않아도 되므로 사용하는데 용이
    
4. *부수 효과(Side Effect)*를 피해 **오류가능성을 최소화**
    
    👉부수 효과란, Setter 함수를 통해 여러 객체에서 값을 변경함으로써 발생하는 변화를 일컫는데, 불변 객체는 값의 수정이 불가능하기 때문에 **다른 메소드가 호출되더라도 객체의 상태가 안정적이라 유지보수에 용이**
    
5. 다른 사람이 작성한 함수를 예측가능하며 안전하게 사용
    
    👉불변 객체의 특성 덕분에, 값의 변경없이 재사용성이 높음
    
6. 가비지 컬렉션의 성능을 높일 수 있다
    
    👉가비지 컬렉터가 스캔해야 되는 객체의 수가 줄어서 스캔해야 하는 메모리 영역과 빈도수가 줄어들기 때문에 GC의 지연 시간을 줄일 수 있음
    

### 🔖Weak Generational Hypothesis

- Garbage Collector(GC)는 새롭게 생성된 객체는 대부분 금방 죽는다는 가설에 맞추어 설계됨