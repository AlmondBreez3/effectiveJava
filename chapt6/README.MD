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

# 이펙티브자바6장

> Item 34 int 상수 대신 열거 타입을 사용해라
>

### 정수 열거 패턴 - 상당히 취약하다

```java
public static final int APPLE_FUJI = 0;
public static final int APPLE_PIPPIN = 1;
public static final int APPLE_GRANNY_SMITH = 2;

public static final int ORANGE_NAVEL = 0;
public static final int ORANGE_TEMPLE = 1;
public static final int ORANGE_BLOOD = 2;
```

단점이 많다. 타입 안전을 보장할 방법이 없고 표현력도 좋지 않다

### 가장 단순한 열거 타입

```java
public enum Apple {FUJI, PIPPIN, GRANNY_SMITH}
public enum Orange {NAVEL, TEMPLE, BLOOD}
```

열거 타입 자체가 클래스이고 상수 하나당 자신의 인스턴스를 하나씩 만들어 public static final 필드로 공개한다.

열거 타입은 밖에서 접근할 수 있는 생성자를 제공하지 않으므로 사실상 final이다

`열거 타입의 toString 메서드는 출력하기에 적합한 문자열을 내어준다.`

💡Apple과 Orange를 예로 들면, 과일의 색을 알려주거나 과일 이미지를 반환하는 메서드를 추가하고 싶을 때? 행성에 대한 열거 타입을 설명하면서 Enum 클래스가 고차원의 추상 개념 하나를 완벽히 표현해낼 수도 있는 예를 들어보겠다

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

이 Enum클래스를 기반으로 어떤 객체의 지구에서의무게를 입력받아 여덟 행성에서의 무게를 출력하는 일은 다음처럼 짧은 코드로 작성할 수 있다

```java
public class WeightTable {
	public static void main(String[] args) {
		double earthWeight = Double.parseDouble(args[0]);
		double mass = earthWeight / Planet.EARTH.surfaceGravity();
		for (Planet p : Planet.values())
				System.out.printg("%s에서의 무게는 %f이다.%n", p, p.surfaveWeight(mass))
	}
}
```

- 기능을 클라이언트에 노출해야할 합당한 이유가 없다면 private 혹은 package-private으로 선언하라

### 열거 타입 상수마다 동작이 달라지는 메서드

```java
enum Operation {
    PLUS {
        @Override
        public double apply(double x, double y) {
            return x+y;
        }
    },
    MINUS {
        @Override
        public double apply(double x, double y) {
            return x-y;
        }
    },
    TIMES {
        @Override
        public double apply(double x, double y) {
            return x*y;
        }
    },
    DIVIDE {
        @Override
        public double apply(double x, double y) {
            return x/y;
        }
    };

    public abstract double apply(double x, double y);
}

@Test
public void operationApplyTest() {
    double x = 10;
    double y = 15;

    for (Operation value : Operation.values()) {
        System.out.printf("%f %s %f = %f%n", x, value, y, value.apply(x, y));
    }
}
출처: https://jake-seo-dev.tistory.com/54 [제이크서 위키 블로그:티스토리]
```

### 상수별 클래스 몸체와 데이터를 사용한 열거 타입

```java
enum Operation {
    PLUS ("+") {
        @Override
        public double apply(double x, double y) {
            return x+y;
        }
    },
    MINUS ("-") {
        @Override
        public double apply(double x, double y) {
            return x-y;
        }
    },
    TIMES ("*") {
        @Override
        public double apply(double x, double y) {
            return x*y;
        }
    },
    DIVIDE ("/") {
        @Override
        public double apply(double x, double y) {
            return x/y;
        }
    };

    private final String symbol;

    Operation(String symbol) {
        this.symbol = symbol;
    }

    public abstract double apply(double x, double y);

    @Override
    public String toString() {
        return symbol;
    }
}

@Test
public void operationApplyTest() {
    double x = 10;
    double y = 15;

    for (Operation value : Operation.values()) {
        System.out.printf("%f %s %f = %f%n", x, value, y, value.apply(x, y));
    }
}
출처: https://jake-seo-dev.tistory.com/54 [제이크서 위키 블로그:티스토리]
```

- toString()을 재정의하여 symbol 필드를 반환하게 하였다

### Enum에서 toString() 구현 이후에 fromString메서드 구현하기

```java
enum Operation {
    PLUS ("+") {
        @Override
        public double apply(double x, double y) {
            return x+y;
        }
    },
    MINUS ("-") {
        @Override
        public double apply(double x, double y) {
            return x-y;
        }
    },
    TIMES ("*") {
        @Override
        public double apply(double x, double y) {
            return x*y;
        }
    },
    DIVIDE ("/") {
        @Override
        public double apply(double x, double y) {
            return x/y;
        }
    };

    private final String symbol;

    Operation(String symbol) {
        this.symbol = symbol;
    }

    public abstract double apply(double x, double y);

    @Override
    public String toString() {
        return symbol;
    }

    public static final Map<String, Operation> stringToEnum = Stream.of(values()).collect(Collectors.toMap(Object::toString, e -> e));

    public static Optional<Operation> fromString(String symbol) {
        return Optional.ofNullable(stringToEnum.get(symbol));
    }
}
```

### 값에 따라 분기하여 코드를 공유하는 열거 타입

```java
enum PayrollDay {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY;

    private static final int MINS_PER_SHIFT = 8 * 60;

    int pay(int minutesWorked, int payRate) {
        int basePay = minutesWorked * payRate;
        int overtimePay;

        switch(this) {
            // 주말
            case SATURDAY : case SUNDAY :
                overtimePay = basePay / 2;
                break;
            // 주중
            default:
                overtimePay = minutesWorked <= MINS_PER_SHIFT ? 0 : (minutesWorked - MINS_PER_SHIFT) * payRate / 2;
        }

        return basePay + overtimePay;
    }
}
```

- 위험한 코드이다 . 휴가나 다른 깜박한 경우를 추가안했을 때를 대비 못한다
- 이런 한계를 극복하기 위해 전략 열거 타입 패턴이 나온다

### 전략 열거 타입 패턴

```java
enum PayrollDay {
    MONDAY(PayType.WEEKDAY)
    , TUESDAY(PayType.WEEKDAY)
    , WEDNESDAY(PayType.WEEKDAY)
    , THURSDAY(PayType.WEEKDAY)
    , FRIDAY(PayType.WEEKDAY)
    , SATURDAY(PayType.WEEKEND)
    , SUNDAY(PayType.WEEKEND);

    private final PayType payType;

    PayrollDay(PayType payType) {
        this.payType = payType;
    }

    public int pay(int minutesWorked, int payRate) {
        return payType.pay(minutesWorked, payRate);
    }

    // 전략 열거 타입
    enum PayType {
        WEEKDAY {
            @Override
            int overtimePay(int minsWorked, int payRate) {
                return minsWorked <= MINS_PER_SHIFT ? 0 : (minsWorked - MINS_PER_SHIFT) * payRate / 2;
            }
        },
        WEEKEND {
            @Override
            int overtimePay(int minsWorked, int payRate) {
                return minsWorked * payRate / 2;
            }
        };

        abstract int overtimePay(int mins, int payRate);
        private static final int MINS_PER_SHIFT = 8 * 60;

        int pay(int minsWorked, int payRate) {
            int basePay = minsWorked * payRate;
            return basePay + overtimePay(minsWorked, payRate);
        }
    }
}
```

### 정리

- 필요한 원소를 컴파일타임에 다 알 수 있는 상수 집합이라면 열거 타입을 사용하자.
    - ex) 태양계 행성, 한 주의 요일, 체스 말
    - ex) 메뉴 아이템, 연산 코드, 명령줄 플래그
- 대다수 열거 타입은 명시적 생성자나 메서드 없이 쓰이지만, 각 상수를 특정 데이터와 연결짓거나 상수마다 다르게 동작할 때는 필요하다.
    - 이 경우, 보통은 추상 메서드를 선언한 뒤, `switch`문 대신 상수별 메서드 구현이 낫다.
    - 열거 타입 상수가 같은 코드를 공유한다면, 전략 열거 타입 패턴을 사용하자.

> Item 35 Ordinal 메서드 대신 인스턴스 필드를 사용하라
>

모든 열거타입은 해당 상수가 그 열거타입에서 몇번째 위치인지를 반환하는 ordinal이라는 메서드를 제공한다

```java
enum Ensemble {
    SOLO, DUET, TRIO, QUARTET, QUINTET,
    SEXTET, SEPTET, OCTET, NONET, DECTET;

    public int numberOfMusicians() {
        return ordinal() + 1;
    }
}
```

- 동작은 하지만 유지보수하기 끔찍하다
- 상수 선언을 바꾸는 순간 numberOfMusicians가 오동작한다

해결책은? 열거 타입 상수에 연결된 값은 ordinal 메서드로 얻지 말고 인스턴스 필드에 저장하자

```java
enum Ensemble {
    SOLO(1)
    , DUET(2)
    , TRIO(3)
    , QUARTET(4)
    , QUINTET(5)
    , SEXTET(6)
    , SEPTET(7)
    , OCTET(8)
    , NONET(9)
    , DECTET(10);

    private final int numberOfMusicians;

    Ensemble(int numberOfMusicians) {
        this.numberOfMusicians = numberOfMusicians;
    }

    public int numberOfMusicians() {
        return numberOfMusicians;
    }
}
```

대부분 프로그래머는 이 메서드(ordinal())을 쓸 일이 없다. 이 메서드는 EnumSet과 EnumMap같이 열거 타입 기반의 범용 자료구조에 쓸 목적으로 설계되었다

> Item 34 비트 필드 대신 EnumSet을 사용하라
>

비트 필드 열거 상수 코드의 장점

1. 여러 상수를 하나의 집합으로 모을 수 있는 장점
2. 합집합 교집합 등의 연산에 유리하다

비트 필드 열거 상수 코드의 단점

1. 정수 열거 상수의 단점을 그대로 가져간다
2. 모든 원소를 순회하기 쉽지 않다

### EnumSet으로 비트 필드 대체해보기



```java
enum Style { BOLD, ITALIC, UNDERLINE, STRIKETHROUGH }

public void applyStyles(Set<Style> styles) {
    StringJoiner sj = new StringJoiner(", ");

    for (Style style : styles) {
        sj.add(style.name());
    }

    System.out.println("applied styles = " + sj);
}
```

- 원소가 총 64개 이하라면RegularEnumSet을 사용하여 비트필드만큼의 성능을 내준다.
    - 그 이상은JumboEnumSet을 사용하도록 내부적으로 판단해서, 크기에도 유연하다
- 원소를 순회하기도 쉽다.

> Item 37 Ordinal 인덱싱 대신 EnumMap을 사용하라
>

### Ordinal을 배열 인덱스로 이용한 예제- 안좋은 방법

```java
static class Plant {
    enum LifeCycle { ANNUAL, PERENNIAL, BIENNIAL }

    final String name;
    final LifeCycle lifeCycle;

    Plant(String name, LifeCycle lifeCycle) {
        this.name = name;
        this.lifeCycle = lifeCycle;
    }

    @Override
    public String toString() {
        return "Plant{" +
                "name='" + name + '\'' +
                ", lifeCycle=" + lifeCycle +
                '}';
    }
}
@Test
public void plantsByLifeCycleTest() {
    Set<Plant>[] plantsByLifeCycle = (Set<Plant>[]) new Set[Plant.LifeCycle.values().length];

    List<Plant> garden = new ArrayList<>(List.of(
            new Plant("A", Plant.LifeCycle.ANNUAL),
            new Plant("B", Plant.LifeCycle.PERENNIAL),
            new Plant("C", Plant.LifeCycle.BIENNIAL),
            new Plant("D", Plant.LifeCycle.ANNUAL)
    ));

    for (int i = 0; i < plantsByLifeCycle.length; i++) {
        plantsByLifeCycle[i] = new HashSet<>();
    }

    for (Plant plant : garden) {
        plantsByLifeCycle[plant.lifeCycle.ordinal()].add(plant);
    }

    for (int i = 0; i < plantsByLifeCycle.length; i++) {
        System.out.printf("%s: %s%n", Plant.LifeCycle.values()[i], plantsByLifeCycle[i]);
    }
}
```

- Ordinal()을 통해 인덱스를 짚고 있다
- 단점
    - Set클래스는 제네릭 타입을 받는데, 제네릭 타입은 배열과 호환성이 좋지 않다. 그래서 비검사 형변환을 수행해야 하고 깔끔히 컴파일되지 않는다
    - 정확한 정수값을 사용하는지 스스로 보증해야한다 열거 타입만큼 명확하지 않다

### EnumMap을 사용해 데이터와 열거 타입을 매핑

```java
@Test
public void plantEnumMapTest() {
    List<Plant> garden = new ArrayList<>(List.of(
            new Plant("A", Plant.LifeCycle.ANNUAL),
            new Plant("B", Plant.LifeCycle.PERENNIAL),
            new Plant("C", Plant.LifeCycle.BIENNIAL),
            new Plant("D", Plant.LifeCycle.ANNUAL)
    ));

    Map<Plant.LifeCycle, Set<Plant>> plantsByLifeCycle = new EnumMap<>(Plant.LifeCycle.class);

    for (Plant.LifeCycle lifeCycle : Plant.LifeCycle.values()) {
        plantsByLifeCycle.put(lifeCycle, new HashSet<>());
    }

    for (Plant plant : garden) {
        plantsByLifeCycle.get(plant.lifeCycle).add(plant);
    }

    System.out.println("plantsByLifeCycle = " + plantsByLifeCycle);
}
```

### 람다로 코드 줄여보기

```java
@Test
public void plantEnumMapTestWithLambda() {
    List<Plant> garden = new ArrayList<>(List.of(
            new Plant("A", Plant.LifeCycle.ANNUAL),
            new Plant("B", Plant.LifeCycle.PERENNIAL),
            new Plant("C", Plant.LifeCycle.BIENNIAL),
            new Plant("D", Plant.LifeCycle.ANNUAL)
    ));

    EnumMap<Plant.LifeCycle, Set<Plant>> lifeCycleSetEnumMap =
            garden.stream().collect(
                    groupingBy(
                            p -> p.lifeCycle,
                            () -> new EnumMap<>(Plant.LifeCycle.class),
                            toSet()
                    )
            );

    System.out.println("lifeCycleSetEnumMap = " + lifeCycleSetEnumMap);
}
```

- 람다식으로 하면 값이 있는 것에 해당하는 중첩 맵을 만든다

### 2차원 배열에 ordinal 이용-안좋은 방법

```java
enum Phase {
    SOLID, LIQUID, GAS;

    enum Transition {
        MELT, FREEZE, BOIL, CONDENSE, SUBLIME, DEPOSIT;

        private static final Transition[][] TRANSITIONS = {
                {null, MELT, SUBLIME}, // SOLID
                {FREEZE, null, BOIL}, // LIQUID
                {DEPOSIT, CONDENSE, null} // GAS
        };

        public static Transition from(Phase from, Phase to) {
            return TRANSITIONS[from.ordinal()][to.ordinal()];
        }
    }
}
```

- Phase는 각각 다른 상태로 변화할 수 있는 열거 형태이다
- SOLID → LIQUID = MELT가 되는 식
- ordinal은 배열 인덱스의 관계를 몰라서 오류가 발생하는 가능성 높아진다

### EnumMap을 이용하여 2차원 배열의 인덱스로 이요한 예제

```java
public enum Phase {
    SOLID, LIQUID, GAS, PLASMA;

    public enum Transition {
        MELT(SOLID, LIQUID),
        FREEZE(LIQUID, SOLID),
        BOIL(LIQUID, GAS),
        CONDENSE(GAS, LIQUID),
        SUBLIME(SOLID, GAS),
        DEPOSIT(GAS, SOLID),
        IONIZE(GAS, PLASMA),
        DEIONIZE(PLASMA, GAS);

        private final Phase from;
        private final Phase to;

        Transition(Phase from, Phase to) {
            this.from = from;
            this.to = to;
        }

        private final static Map<Phase, Map<Phase, Transition>> map =
                Stream.of(values()).collect(groupingBy(
                        t -> t.from,
                        () -> new EnumMap<>(Phase.class),
                        toMap(t -> t.to, t -> t,
                                (x, y) -> y,
                                () -> new EnumMap<>(Phase.class)
                        )
                ));

        public static Transition from(Phase from, Phase to) {
            return map.get(from).get(to);
        }
    }
}

```

- 첫번째 수집기인 groupingBy에서는 전이를 이전 상태를 기준으로 묶고
- 두번째 수집기인 toMap에서는 이후 상태를 전이에 대응시키는 EnumMap을 생성
- EnumMap을 사용하면 유지보수하기 좋다

### 핵심 정리

- 배열의 인덱스를 위해 ordinal사용하지 말고 EnumMap을 사용하자
- 다차원 관계는 EnumMap<..., EnumMap<...>>로 표기하자

> Item 38 확장할 수 있는 열거 타입이 필요하면 인터페이스를 사용하라
>
- 열거 타입의 확장이 지원되지는 않는다
    - 하지만 가끔 필요 시, 인터페이스를 통해 확장하면 된다

```java
public interface Operation {
    double apply(double x, double y);
}

public enum BasicOperation implements Operation {
    PLUS("+") {
        @Override
        public double apply(double x, double y) {
            return x+y;
        }
    },
    MINUS("-") {
        @Override
        public double apply(double x, double y) {
            return x-y;
        }
    },
    TIMES("*") {
        @Override
        public double apply(double x, double y) {
            return x*y;
        }
    },
    DIVIDE("/") {
        @Override
        public double apply(double x, double y) {
            return x/y;
        }
    };

    private final String symbol;

    BasicOperation(String symbol) {
        this.symbol = symbol;
    }

    @Override
    public String toString() {
        return symbol;
    }
}

public enum ExtendedOperation implements Operation {
    EXP("^") {
        @Override
        public double apply(double x, double y) {
            return Math.pow(x, y);
        }
    },
    REMAINDER("%") {
        @Override
        public double apply(double x, double y) {
            return x % y;
        }
    };

    private final String symbol;

    ExtendedOperation(String symbol) {
        this.symbol = symbol;
    }

    @Override
    public String toString() {
        return symbol;
    }
}
```

### 클래스로 받아보기

```java
@Test
public void operationTest() {
    double x = 4.0;
    double y = 2.0;
    getEnumClass(BasicOperation.class, x, y);
    getEnumClass(ExtendedOperation.class, x, y);
}

private <T extends Enum<T> & Operation> void getEnumClass(Class<T> enumClass, double x, double y) {
    for (Operation operation : enumClass.getEnumConstants()) {
        System.out.printf("%f %s %f = %f%n", x, operation, y, operation.apply(x, y));
}
```

- enum타입의 클래스를 받아 getEnumConstants()를 통해 클래스가 가진 apply메서드를 실행해보았다
- <T extends Enum<T? & Operation>의 의미는 enum이며 Operation을 상속받았다는 것을 이야기한다

### 컬렉션으로 받아보기

```java
@Test
public void operationTest() {
    double x = 4.0;
    double y = 2.0;

    getEnumCollection(Arrays.asList(BasicOperation.values()), x, y);
    getEnumCollection(Arrays.asList(ExtendedOperation.values()), x, y);
}

private void getEnumCollection(Collection<? extends Operation> opSet, double x, double y) {
    for (Operation operation : opSet) {
        System.out.printf("%f %s %f = %f%n", x, operation, y, operation.apply(x, y));
    }
}
```

- enum타입의 값이 들어있는 list를 넘겨본 형태이다
- 특정 연산에서는 EnumSet과 EnumMap을 사용하지 못한다

# 스터디 질문 왜 사용 못하는지 얘기해보자

> item 39 명명 패턴보다는 애너테이션을 사용하라
>

명명패턴이란?

- 메서드의 이름앞을 test…로 짓는 등 이름에 팩턴을 주어 Reflection등으로 패턴 검출시 특정 작업을 수행하는 식의 코딩 형식

단점

- 오탈자의 위험
- 메서드, 파라미터, 클래스명에 대한 설정이 불가능
- 프로그램 요소를 매개변수로 전달할 마땅할 방법이 없다

### 샘플 1: 일반 메서드 애너테이션

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface MethodTest {
}
```

- @Retention과@Target은 메타 애너테이션이라 불린다.
- @Retention은 생존기간을 나타낸다
    - RetentionPolicy.RUNTIME:런타임에도 유지되어야 한다는 표시
- @Target(ElementType.METHOD):해당 애너테이션이 반드시 메서드에 적용되어야 한다는 것을 알려준다
- @MethodTest: 애너테이션과 같이 아무 매개 변수 없이 단순히 대상에 마킹한다는 뜻으로 마킹 애너테이션이라 한다.

```java
public class Sample {
    @MethodTest
    public static void m1() { }
    public static void m2() { }

    @MethodTest
    public static void m3() {
        throw new RuntimeException("실패");
    }
    public static void m4() { }

    @MethodTest
    public void m5() { } // 잘못 사용한 예: 정적 메서드가 아니다.
    public static void m6() { }
    @MethodTest
    public static void m7() {
        throw new RuntimeException("실패");
    }
    public static void m8() { }
}
```

### 예제 샘플 2: 특정 예외를 던져야만 성공하는 테스트

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface ExceptionSingleTest {
    Class<? extends Throwable> value();
}
```

- Value는 기본 파라미터 값을 의미
    - Target 애너테이션의 경우 기본 파라미터 값으로 ElementType을 받는다고 보면 된다
    - 여기서는 Throwable을 상속하는 모든 클래스를 받기 때문에 모든 예외 포용 가능

```java
public class Sample2 {
    @ExceptionSingleTest(ArithmeticException.class)
    public static void m1() {
        int i = 0; // 성공
        i = i / i;
    }

    @ExceptionSingleTest(ArithmeticException.class)
    public static void m2() {
        int[] a = new int[0]; // 실패, 다른 예외 발생
        int i = a[1];
    }

    @ExceptionSingleTest(ArithmeticException.class)
    public static void m3() { } // 실패, 예외가 발생하지 않음
}
```

```java
@Test
public void sample2ClassAnnotationTest() throws ClassNotFoundException {
    int tests = 0;
    int passed = 0;
    Class<?> testClass = Class.forName("item39.Sample2");
    Method[] declaredMethods = testClass.getDeclaredMethods();

    for (Method m : declaredMethods) {
        if(m.isAnnotationPresent(ExceptionSingleTest.class)) {
            tests++;
            try {
                m.invoke(null);
                System.out.println(m + ", 테스트 실패 (예외를 던지지 않음)");
            } catch (InvocationTargetException wrappedExc) {
                Throwable exc = wrappedExc.getCause();
                Class<? extends Throwable> excType = m.getAnnotation(ExceptionSingleTest.class).value(); // 애너테이션 매개변수의 값을 추출한다.

                if(excType.isInstance(exc)) {
                    System.out.printf("테스트 %s 성공: 기대한 예외 %s, 발생한 예외 %s%n", m, excType.getName(), exc);
                    passed++;
                } else {
                    System.out.printf("테스트 %s 실패: 기대한 예외 %s, 발생한 예외 %s%n", m, excType.getName(), exc);
                }
            } catch (Exception exc) {
                System.out.println("잘못 사용한 @ExceptionTest: " + m);
            }
        }
    }

    System.out.printf("성공: %d, 실패: %d%n", passed, tests - passed);
}
```

- Sample2 클래스에 선언된 메서드 중@ExceptionSingleTest 애너테이션이 달린 것을 찾는다.
- InvocationTargetException의 하위 예외가 일어나고, 그 예외가@ExceptionSingleTest 애너테이션의 인수로서 들어온 예외와 일치해야만passed의 카운트가 올라간다.
    - InvocationTargetException은 리플렉션 API의 통합 예외로.getCause() 메서드를 통해서 실제 예외가 무엇인지 알 수 있다.

### 샘플 3: 샘플2와 동일한데 배열로 받는 애너테이션

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface ExceptionArrayTest {
    Class<? extends Throwable>[] value();
}
```

- 단 하나의 애너테이션을 받더라도 아무런 에러가 나오지 않는다
    - ex)@ExceptionArrayTest(IndexOutOfBoundsException)처럼 작성해도 문제없다.

```java
public class Sample3 {
    @ExceptionArrayTest({
            IndexOutOfBoundsException.class
            , NullPointerException.class
    })
    public static void doublyBad() {
        List<String> list = new ArrayList<>();
        list.add(5, null);
    }
}
```

### 샘플 4: @Repeatable이용하여 여러개의 값을 받을 수 있는 애너테이션 만들기

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@Repeatable(ExceptionTestContainer.class)
public @interface ExceptionRepeatableTest {
    Class<? extends Throwable> value();
}
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface ExceptionTestContainer {
    ExceptionRepeatableTest[] value();
}
```

```java
public class Sample4 {
    @ExceptionRepeatableTest(IndexOutOfBoundsException.class)
    @ExceptionRepeatableTest(NullPointerException.class)
    public static void doublyBad() {
        List<String> list = new ArrayList<>();
        list.add(5, null);
    }
}
```

### 핵심 정리

- 어노테이션으로 할 수 있는 일을 굳이 명명 패턴으로 처리하지 말자
- 자바 프로그래머라면 어노테이션 타입들을 사용해야 한다

> item 40 Override 애너테이션을 일관되게 사용하라
>

해당 애너테이션을 일관되게 사용한다면 여러가지 버그들을 예방해준다

▶️ 영어 알파벳 2개로 구성된 문자열(바이그램)을 표현하는 클래스

```java
public class Bigram {
    private final char first;
    private final char second;

    public Bigram(char first, char second) {
        this.first  = first;
        this.second = second;
    }

    public boolean equals(Bigram b) {
        return b.first == first && b.second == second;
    }

    public int hashCode() {
        return 31 * first + second;
    }

    public static void main(String[] args) {
        Set<Bigram> s = new HashSet<>();
        for (int i = 0; i < 10; i++)
            for (char ch = 'a'; ch <= 'z'; ch++)
                s.add(new Bigram(ch, ch));
        System.out.println(s.size());
    }
}
```

→ main 메서드가 실행되고 Set은 중복을 허용하지 않으니 26이 출력 될 것 같지만, 실제로는 260이 출력된다.

문제의 원인: equals 메소드를 재정의한게 아니라 다중정의해버린 것이다. Object의 `equals()` 를 재정의하려면 매개변수 타입을 Object로 해야 하는데 그렇지 않아서 상속이 아닌 별개의 equals 메서드를 정의한 꼴이 되었다.

수정하면

```java
@Override public boolean equals(Object o) {
        if (!(o instanceof Bigram2))
            return false;
        Bigram2 b = (Bigram2) o;
        return b.first == first && b.second == second;
}
```

equals에 문제가 있음에도 컴파일에는 성공하기 때문에, `@Override` 를 달지 않게 되면 위의 잘못한 점을 컴파일러가 알려주지 못한다.

## Override의 예외사항

구체 클래스에서 상위 클래스의 메서드를 재정의할 때는 굳이 @Override를 달지 않아도 된다. 구체 클래스인데 구현하지 않은 추상 메서드가 남아 있다면 컴파일러가 자동으로 사실을 알려주기 떄문이다

- 재정의는 인터페이스의 메서드를 재정의할 때도 사용할 수 있다.  단, 추상 클래스나 인터페이스에는 상위 클래스나 상위 인터페이스의 메서드를 재정의하는 모든 메서드에 @Override를 다는 것이 좋다
- 예컨데 Set 인터페이스는 Collection인터페이스를 확장했지만 새로 추가한 멧드는 없기 때문에 모든 메서드 선언에 @Override를 달아 실수로 추가한 메서드가 없음을 보장한다

> **핵심 정리:** 재정의한 모든 메서드에 @Override 애너테이션을 의식적으로 달면, 여러분이 실수했을때 컴파일러가 바로 알려줄 것이다. 예외는 한 가지 뿐이다. 구체 클래스에서 상위 클래스의 추상 메서드를 재정의한 경우에는 이 애너테이션을 달지 않아도 된다.”
>

> item 41: 정의하려는 것이 타입이라면 마커 인터페이스를 사용하여라
>

마커 인터페이스란?:

- 아무 메서드도 담고 있지 않고 단지 자신을 구현하는 클래스가 특정 속성을 가짐을 표시해주는 인터페이스를 의미한다
- 대표적인 예로, Serializable 인터페이스가 있다. 단순히 자신을 구현한 클래스의 인스턴스는 ObjectOutputStream을 통해 직렬화할 수 있다고 알려주는 역할을 한다

마커 인터페이스의 장점

- 마커 인터페이스는 두가지 면에서 애너테이션 보다 장점을 가진다
    - 마커 인터페이스는 타입이기 때문에 애너테이션을 사용했다면 런타임에서나 발견될 오류를 컴파일 타임에 잡을 수 있다
    - 적용 대상을 더 정밀하게 지정할 수 있다 그냥 마킹하고 싶은 클래스에서만 그 인터페이스를 구현하면 마킹된 타입은 자동으로 그 인터페이스의 하위 타입임이 보장된다.

어떤 상황에서 마커 애너테이션과 인터페이스를 써야 할까?

1. 클래스와 인터페이스 외의 프로그램 요소(모듈/패키지/필드/지역변수 등)에 마킹해야 할때는 **애너테이션**을 사용한다.
2. **마킹이 된 객체를 매개변수로 받는 메서드**를 작성할 일이 있다면 마커 인터페이스를 사용해야 한다. 마커 인터페이스를 해당 메서드의 매개변수 타입으로 사용하여 컴파일 타임에 오류를 잡아낼 수 있기 때문이다.

> 📚 **핵심 정리**
>
>
> 새로 추가하는 메서드 없이 단지 타입 정의가 목적이라면 마커 인터페이스를 정의하자. 클래스나 인터페이스 외의 프로그램 요소에 마킹해야 하거나, 애너테이션을 적극 활용하는 프레임워크의 일부로 마커를 편입시키고자 한다면 마커 애너테이션을 사용하자.
>

💡 추가 리서치

```java
public interface Deletable {
}
```

개체를 제거할 수 있는지 여부를 나타내는 마커를 만들 수 있다

데이터베이스에서 엔티티를 삭제를 하려면 이 엔티티를 나타내는 객체가 Deletable 마커 인터페이스를 구현해야 한다

```java
public class Entity implements Deletable {
    // implementation details
}
```

데이터베이스에서 엔티티를 제거하는 메서드가 있는 DAO 개체가 있다고 가정하면 마커 인터페이스를 구현한 객체만 삭제할 수 있도록 delete()메서드를 작성할 수 있다

```java
public class ShapeDao {

    // other dao methods

    public boolean delete(Object object) {
        if (!(object instanceof Deletable)) {
            return false;
        }

        // delete implementation details
        
        return true;
    }
}
```

결론 :, **우리는 객체의 런타임 동작에 대한 표시를 JVM에 제공하고 있다.**  객체가 마커 인터페이스를 구현하는 경우 데이터베이스에서 삭제할 수 있다. **그래서** 컴파일 타임에 오류를 잡을 수 있는 건가봄?