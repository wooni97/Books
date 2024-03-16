# SOLID 5원칙

## SRP(Single Responsibility Principle) - 단일 책임 원칙

### SRP(단일 책임 원칙)이란?

*어떤 클래스를 변경해야 하는 이유는 오직 하나뿐이어야 한다.*

그렇다면 변경이 여럿 이유 때문에 일어나는 경우는 어떤 경우일까?

### SRP 원칙 위반 예제

```java
class Employee {
    String name;
    String positon;

    Employee(String name, String position) {
        this.name = name;
        this.positon = position;
    }

	// * 초과 근무 시간을 계산하는 메서드 (두 팀에서 공유하여 사용)
    void calculateExtraHour() {
        // ...
    }

    // * 급여를 계산하는 메서드 (회계팀에서 사용)
    void calculatePay() {
        // ...
        this.calculateExtraHour();
        // ...
    }

    // * 근무시간을 계산하는 메서드 (인사팀에서 사용)
    void reportHours() {
        // ...
        this.calculateExtraHour();
        // ...
    }

    // * 변경된 정보를 DB에 저장하는 메서드 (기술팀에서 사용)
    void saveDababase() {
        // ...
    }
}
```

만약 회계팀에서 급여를 계산하는 기존의 방식을 새로 변경하여, 코드에서 초과 근무 시간을 계산하는 메서드 calculateExtraHour() 의 알고리즘 업데이트가 필요해졌다. 

그 변경에 의한 파급효과로 인해 인사팀에서 사용하는 reportHours()에도 영향을 주게 되어버린다. 그렇게 되면 reportHours()메서드에 대해서도 수정이 필요하다.

인사팀 입장에서는 내가 변경이 되어야 하는 이유가 아닌데 변경이 되는 불상사가 발생한다.  이것은 Employee 클래스가 회계팀, 인사팀에 대한 책임을 한꺼번에 가지고 있기 때문이다.

이렇게 연쇄적으로 변경이 일어나게 되면 좋은 구조가 아니다.

```java
// * 회계팀에서 사용되는 전용 클래스
class PayCalculator {
    // * 초과 근무 시간을 계산하는 메서드
    void calculateExtraHour() {
        // ...
    }
    void calculatePay() {
        // ...
        this.calculateExtraHour();
        // ...
    }
}

// * 인사팀에서 사용되는 전용 클래스
class HourReporter {
    // * 초과 근무 시간을 계산하는 메서드
    void calculateExtraHour() {
        // ...
    }
    void reportHours() {
        // ...
        this.calculateExtraHour();
        // ...
    }
}
```

단일 책임 원칙을 적용하려면 각 책임에 맞게 클래스를 분리하면 된다.

## OCP(Open Closed Principle) - 개방 폐쇄 원칙

### OCP(개방 폐쇄 원칙)이란?

*자신의 확장에는 열려있고 변경에는 닫혀있어야 한다.*

→ 새로운 변경 사항이 발생했을때 유연하게 코드를 추가함으로써 큰 힘을 들이지 않고 기능을 확장시키고, 객체를 직접적으로 수정하지 않고 변경사항을 적용할 수 있어야 한다.

### **OCP 원칙 위반 예제**

Car 클래스가 있고, 운전자인 Driver 클래스가 Car 클래스의 type을 창문을 열고 기어를 조작한다.

Car.java

```java
public class Car {
    
	public void openWindow() {
		System.out.println("창문을 수으로 엽니다");
	}
	
  public void operateGear(Car car) {
		System.out.println("기어를 수동으로 조작합니다.");
  }
	
    
}
```

Driver.java

```java
public class Driver {

    public void drive(Car car) {
			car.openWindow();
			car.operateGear();
    }
}
```

Main.java

```java
public class Main {
    public static void main(String[] args) {

        Driver driver = new Driver();
        Car matiz = new Car();

        driver.drive();
     
    }
}

/*
창문을 수동으로 엽니다
기어를 수동으로 조작합니다.
*/
```

그런데 어느날, 운전자에게 소나타라는 새로운 차가 생겼다. 그런데 소나타는 창문이 수동이 아닌 자동으로 열고, 기어도 수동 조작이 아닌 자동 조작이다. 이럴 경우엔 메서드의 수정이 필요하다. 

```java
public class Car {
    
	public void openWindow() {
		System.out.println("창문을 자동으로 엽니다");
	}
	
  public void operateGear(Car car) {
		System.out.println("기어를 자동으로 조작합니다.");
  }
    
}

public class Main {
    public static void main(String[] args) {

        Driver driver = new Driver();
        Car sonata = new Car();

        driver.openWindow(sonata);
        driver.operateGear(sonata);

				driver.drive();

    }
}
```

앞으로 운전자에겐 더욱 다양한 차들을 몰 확률이 높다. 이럴때마다 계속해서 일일히 변경을 해줘야 하는 것인가? 그리고 이렇게 변경을 해주다가 마티즈를 다시 몰고 싶을땐 또 변경을 하고?

이렇게 객체를 직접적으로 수정하게 되면 유지보수는 번거로워지고 또 그에 따른 비용도 높아지게 된다. 

그렇기에 객체를 직접 수정하지 않고도 변경 사항을 적용할 수 있는 OCP 원칙을 지켜야한다.

이는 객체지향의 원칙 추상화와 관련이 있는데, 공통적인 속성 행위를 끌어올려서 단순화 하여 상위 클래스 또는 인터페이스를 운전자와 차종들 사이에 둔다.

 여기서 단순화라는 것은 openWindow 메서드에서 창문을 수동으로 열든, 자동으로 열든 그건 중요하지 않고  **“창문 개방”**이라는 것에만 초점을 두는 것이다. 또 operateGear 메서드에서도 **“기어 조작”**에만 초점을 두는 것이다.

```java
abstract class Car {
   public void openWindow() {}
   public void operateGear() {}
}

public class Matiz extends Car {
    @Override
    public void openWindow() {
        System.out.println("창문을 수동으로 엽니다.");
    }

    @Override
    public void operateGear() {
        System.out.println("기어를 수동으로 조작합니다.");
    }
}

public class Sonata extends Car {

    @Override
    public void openWindow() {
        System.out.println("창문을 자동으로 엽니다.");
    }

    @Override
    public void operateGear() {
        System.out.println("기어를 자동으로 조작합니다.");
    }
}

public class Main {
    public static void main(String[] args) {

        Car matiz = new Matiz();
        Car sonata = new Sonata();

        Driver driver = new Driver();

        driver.drive(matiz);
        driver.drive(sonata);

    }
}
```

**OCP 원칙을 잘 지키는 사례가 JDBC이다.**

JDBC를 사용하는 클라이언트는 데이터베이스가 오라클에서 MySQL로 바뀌더라도 복잡한 하드 코딩 없이 그냥 connection 객체 부분만 교체해주면 된다. 

![image](https://github.com/wooni97/Books/assets/55667589/862c9564-e97c-41ca-9fde-a24b5e0df1f9)


자바 애플리케이션은 데이터베이스라고 하는 주변의 변화에 닫혀(closed) 되어 있는 것이다. 반대로 데이터베이스를 손쉽게 교체한다는 것은 데이터베이스가 자신의 확장에는 열려 있다는 말이 된다.

## LSP - 리스코프 치환원칙

### LSP(리스코프 치환원칙)이란?

*서브 타입은 언제나 자신의 기반 타입으로 교체할 수 있어야 한다.*

교체한다는게 뭘까? 교체란, 자식 클래스는 최소한 자신의 부모 클래스에서 가능한 행위는 수행이 보장되어야 한다.

→ **즉, 부모 클래스의 인스턴스를 사용하는 위치에 자식 클래스의 인스턴스를 대신 사용했을때 코드가 원래 의도대로 동작해야 한다**.

LSP 원칙을 잘 적용한 예제가 자바의 컬렉션 프레임워크(Collection Framework)

![image](https://github.com/wooni97/Books/assets/55667589/a6d76118-07c9-495c-82c1-a9e52f7418cf)

변수에 LinkedList 자료형을 담아 사용하다, 중간에 다른 HashSet 자료형으로 바꿔도 add() 메서드 동작을 보장받기 위해서는 Collection이라는 인터페이스 타입으로 변수를 선언하여 할당하면 된다.

```java
void myData() {
	// Collection 인터페이스 타입으로 변수 선언
    Collection data = new LinkedList();
    data = new HashSet(); // 중간에 전혀 다른 자료형 클래스를 할당해도 호환됨
    
    modify(data); // 메소드 실행
}

void modify(Collection data){
    list.add(1); // 인터페이스 구현 구조가 잘 잡혀있기 때문에 add 메소드 동작이 각기 자료형에 맞게 보장됨
    // ...
}
```

### LSP 위반 예제

부모 클래스의 행동 규약을 자식 클래스가 위반하면 안되는 것이 리스코프 치환 원칙의 핵심.

행동 규약 위반 : 자식 클래스가 오버라이딩 할때, 잘못되게 재정의 하면 원칙을 위배할 수 있음.

1. 잘못된 메소드 오버라이딩

```java
public class Rectangle {

    public int getArea(int width, int height) {
        return width * height;
    }
}

public class Square extends Rectangle{

    public int getArea(int side) {
        return side * 2;
    }
}

public class Main {
    public static void main(String[] args) {

        Rectangle rectangle = new Rectangle();
        int area = rectangle.getArea(3, 4);
        System.out.println(area);

        Rectangle square = new Rectangle();
        int area2 = square.getArea(4);
        System.out.println(area2);
    }
}

/*
컴파일 에러
*/
```

1. 부모의 의도와 다른 오버라이딩
    
    ```java
    public class Rectangle {
        private int width;
        private int height;
    
        public void setWidth(int width) {
            this.width = width;
        }
    
        public void setHeight(int height) {
            this.height = height;
        }
    
        public int getArea() {
            return width * height;
        }
    }
    
    public class Square extends Rectangle {
        
    		@Override
        public void setWidth(int width) {
            super.setWidth(width);
            super.setHeight(width);
        }
    
        @Override
        public void setHeight(int height) {
            super.setHeight(height);
            super.setWidth(height);
        }
    }
    
    public int clientMethod(Rectangle rc) {
    
        rc.setWidth(5);
        rc.setHeight(3);
        assert(rc.getArea() == 15);
    }
    ```
    
    상속의 특성을 잘 적용하면 LSP 원칙을 지켜질거라고 했는데..
    
    상속은 is-a 관계라고 했다. 
    
    **Square가 Rectangle이 아닌건가? IS-A 관계가 성립하지 않는것일까?**
    
    → 추상적인 개념상 정사각형은 직사각형일 수 있지만, 메서드 를 작성한 사람의 관점에서 Square 객체는 절대로 Rectangle 객체가 아니다. 왜냐면 Sqare 객체의 행위가 메소드에서 기대하는 Rectangle 객체의 행위와 일치하지 않기 때문이다. 즉 행위 측면에서 봤을 때 Square는 Rectangle이 아니다.
    
    하위 타입이 상위 타입으로 캐스팅 되더라도 기존 기능에는 문제 없이 잘 상속이 되어야 하는데, 위에는 추상화가 잘못된것이다. 
    
    ![image](https://github.com/wooni97/Books/assets/55667589/334c5d7b-294c-4891-8687-dcfd0c9fe534)

    이럴 경우엔 Shape라는 상위 인터페이스를 끌어올려 Square, Rectangle이 각각의 특성에 맞게 동작하도록 구현하면 된다. 
    
    ## ISP - 인터페이스 분리 원칙
    
    ### ISP(인터페이스 분리 원칙)이란?
    
    *클라이언트는 자신이 사용하지 않는 메서드에 의존 관계를 맺으면 안된다.*
    
    → 어떤 인터페이스를 구현 또는 상속 받는 객체가 있을때, 
    
    이 객체가 과연 그 인터페이스의 모든 기능을 활용하고 있는가?
    
    만약 인터페이스의 추상 메서드들을 범용적으로 이것저것 구현한다면, 그 인터페이스를 상속받은 클래스는 자신이 사용하지 않는 인터페이스마저 억지로 구현해야 한다.
    
    인터페이스 분할 원칙이란 인터페이스를 잘게 분리 하여, **클라이언트의 목적과 용도에 맞게 적합한 인터페이스만을 제공하는 것이다.**
    
    ### ISP 원칙 위반 예제
    
    스마트폰 종류의 클래스를 구현하기 앞서 인터페이스로 스마트폰을 추상화 하였다.
    
    ```java
    interface ISmartPhone {
        void call(String number); // 통화 기능
        void message(String number, String text); // 문제 메세지 전송 기능
        void wirelessCharge(); // 무선 충전 기능
        void AR(); // 증강 현실(AR) 기능
        void biometrics(); // 생체 인식 기능
    }
    ```
    
    최신 기종의 스마트폰을 클래스로 구현한다면 인터페이스의 동작 모두가 필요하므로 문제가 없다.
    
    그러나, 옛 기종의 스마트폰을 클래스로 구현한다면 사용하지 않는 기능들이 여럿 존재한다. 
    
    ```java
    
    class S3 implements ISmartPhone {
        public void call(String number) {
        }
    
        public void message(String number, String text) {
        }
    
        public void wirelessCharge() {
            System.out.println("지원 하지 않는 기능 입니다.");
        }
    
        public void AR() {
            System.out.println("지원 하지 않는 기능 입니다.");
        }
    
        public void biometrics() {
            System.out.println("지원 하지 않는 기능 입니다.");
        }
    }
    ```
    
    무선 추전 등과 같은 기능은 포함되어있지 않기 때문에 이렇게 억지스럽게 구현을 해야한다.
    
    이는 ISP 원칙에 위반된것이다.
    
    ```java
    interface SmartPhone {
        void call(String number); // 통화 기능
        void message(String number, String text); // 문제 메세지 전송 기능
    }
    
    interface WirelessChargable {
        void wirelessCharge(); // 무선 충전 기능
    }
    
    interface ARable {
        void AR(); // 증강 현실(AR) 기능
    }
    
    interface Biometricsable {
        void biometrics(); // 생체 인식 기능
    }
    
    class S3 implements SmartPhone {
        
    		public void call(String number) {
        }
    
        public void message(String number, String text) {
        }
    }
    ```
    
    각각의 기능의 맞게 인터페이스를 잘게 분리하여 구성한다.
    
    클래스가 지원되는 기능만을 선별하여 implements하면 ISP 원칙이 지켜지는 것이다. - 인터페이스 최소주의
    
    ### SRP와 ISP
    
    단일 책임 원칙과 인터페이스 분할 원칙은 같은 문제에 대한 두가지 다른 해결책이라고 볼 수 있다.
    
    둘이 완전히 일치하는 것은 아니다.
    ![image](https://github.com/wooni97/Books/assets/55667589/fcdc05bd-20c0-4cfc-ad4e-cd89dd3eb1af)

    위와 같이 게시판 인터페이스엔 글쓰기, 읽기, 삭제, 수정 추상 메서드가 정의되어있다. 이는 게시판에 필요한 기능들이며 게시판의 단일 책임 원칙에 위배되지 않는다.
    
    하지만 이를 구현하는 사용자와 관리자 입장에서, 삭제라는 기능이 관리자에게만 있고 사용자에게는 없을때 ISP 원칙 위반으로 이어진다. 이럴 경우에는 책임을 더 분리해야한다.
    
    ## DIP - 의존 역전 원칙
    
    ### DIP(의존 역전 원칙)이란?
    
    *고차원 모듈은 저차원 모듈에 의존하면 안된다.*
    
    → 객체에서 어떤 클래스를 참조해서 사용해야하는 상황이 생긴다면, 그 클래스를 직접 참조하는것이 아니라 그 대상의 상위 요소로 참조하라는 원칙
    
    - 고차원 모듈? 저차원 모듈?
        
        고차원 모듈이란 추상적인 레벨이 높고 더 단순화 되어있는 것. ex) 인터페이스
        
        저차원 모듈이란? 구현체
        
    
    왜 고차원 모듈에 의존해야 할까?
    
    → 변경이 잦은 구현체를 의존하면 코드를 바꿔야 하는 경우가 많다. 이는 유지보수성이 떨어진다는 것.
    
    외부에서 바라보는 것은 변경이 없어야 한다.
    
    ### DIP 원칙 위반 예제
    
    운전자가 차를 운전할때 할 수 있는 동작은 창문을 여는것과 기어를 조작하는 것이다.
    
    운전자는 현재 마티즈를 운전하고 있다. 그런데 어느날 새로운 차인 소나타를 운전해야 하는 일이 생겼다.
    
    이럴 경우엔 drive 메서드에 변수 타입을 교체해 줘야 한다. 
    
    ```java
    public class Sonata {
     
        void openWindow() {
            System.out.println("창문을 자동으로 엽니다.");
        }
    
        
        void operateGear() {
            System.out.println("기어를 자동으로 조작합니다.");
        }
    }
    
    public class Matiz {
        void openWindow() {
            System.out.println("창문을 수동으로 엽니다.");
        }
    
      
        void operateGear() {
            System.out.println("기어를 수동으로 조작합니다.");
        }
    }
    
    public class Driver {
    
        public void drive(Matiz matiz) {
            matiz.openWindow();
            matiz.operateGear();
        }
    }
    ```
    
    위 코드의 문제는 구현된 하위 모듈을 의존하고 있는 것이다. DIP 원칙을 잘 지켰다면 새로운 차로 변경할때마다 변수 타입을 교체해줘야 하는 필요가 없다.
    
    ```java
    interface Car {
        void openWindow();
        void operateGear();
    } 
    
    public class Sonata implements Car{
        private String name;
    
        public Sonata(String name) {
            this.name = name;
        }
    
        @Override
        public void openWindow() {
            System.out.println(this.name +"의 창문을 자동으로 엽니다.");
        }
    
        @Override
        public void operateGear() {
            System.out.println(this.name +"의 기어를 자동으로 조작합니다.");
        }
    }
    
    public class Matiz implements Car {
        private String name;
    
        public Matiz(String name) {
            this.name = name;
        }
    
        @Override
        public void openWindow() {
            System.out.println(this.name + "의 창문을 수동으로 엽니다.");
        }
    
        @Override
        public void operateGear() {
            System.out.println(this.name + "의 기어를 수동으로 조작합니다.");
        }
    }
    
    public class Main {
        public static void main(String[] args) {
    
            Car matiz = new Matiz("matiz");
            Car sonata = new Sonata("sonata");
    
            Driver driver = new Driver();
    
            driver.drive(matiz);
            driver.drive(sonata);
    
        }
    }
    ```
    
    OCP 원칙 또한 준수한 셈이라고 볼 수 있는데, 하나의 해결책 안에는 여러 설계원칙이 녹아있다.
