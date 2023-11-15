# 6장 열거타입과 애너테이션
클래스의 일종인 열거타입(Enum), 인터페이스의 일종인 애너테이션(Annotation)

> item 34 int 상수 대신 열거 타입을 사용하라

안좋은 정수 열거 패턴
```java
public static final int APPLE_FUJI = 0;
public static final int APPLE_PIPPIN = 1;
public static final int APPLE_GRANNY_SMITH = 2;

public static final int ORANGE_NAVEL = 0;
public static final int ORANGE_TEMPLE = 1;
public static final int ORANGE_BLOOD = 2;

```

- 정수 열거 패턴을 사용한 프로그램은 깨지기 쉽다. 

```java
public enum Apple {FUJI, PIPPIN, GRANNY_SMITH}
public enum Orange {NAVEL, TEMPLE, BLOOD}
```

이렇게 선언하면 상수 하나당 자신의 인스턴스를 하나씩 만들어 public static field로 공개한다. 열거 타입은
밖에서 접근할 수 있는 생성자를 제공하지 않으므로 사실상 final이다.

- 열거 타입의 toString 메서드는 출력하기에 적합한 문자열을 내어준다. 

## 열거 타입에 임의의 메서드나 필드를 추가할 수 있고 임의의 인터페이스를 구현하게 할 수 도 있다
예시 코드
```java
public enum Planet {
    MERCURY(3.302e+23, 2.439e6),
    VENUS (4.869e+24, 6.052e6),
    EARTH (5.975e+24, 6.378e6),
    MARS (6.419e+23, 3.393e6),
    JUPITER(1.899e+27, 7.149e7),
    SATURN(5.685e+26,6.027e7),
    URANUS(8.683e+25,2.556e7),
    NEPTUNE(1.024e+26, 2.477e7)
    
    private final double mass;
    private final double radius;
    private final double surfaceGravity;
    
    private static final double G = 6.67300E-11;
    
    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
        surfaceGravity = G * mass / (radius * radius);
    }
    
    public double mass() {return mass;}
    public double radius() {return radius;}
    public double surfaceGravity() {return surfaceGravity;}
    
    public double surfaceWeight(double mass ) {
        return mass* surfaceGravity;
    }
    
}
```
열거타입은 근본적으로 붊변이라 모든 필드는 final이어야 한다. 필드를 public으로 선언해도 되지만, private으로 두고
별도의 public 접근자 메서드를 두는 게 낫다

> item 