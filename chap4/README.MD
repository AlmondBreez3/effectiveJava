# 클래스와 인터페이스 
> Item 15: 클래스와 멤버의 접근 권한을 최소화하라

클래스 내부 데이터와 내부 구현 정보를 외부 컴포넌트로부터 잘 숨기는 것이 우리의 핵심 목표!
오직 API를 통해서만 다른 컴포넌트와 소통하며 서로의 내부 동작 방식에는 전혀 개의치 않는다

- 기본 원칙 : 모든 클래스와 멤버의 접근성을 가능한 한 좁혀야 한다. 
    소프트웨어가 올바로 동작하는 한 항상 가장 낮은 접근 수준을 부여해야 한다
- 한 클래스에서만 사용하는 package-private 톱레벨 클래스나 인터페이스는 이를 사용하는 클래스 안에 private static으로 중첩시켜보자(item24)
- public일 필요가 없는 클래스의 접근 수준을 package-private 톱레벨 클래스로 좁히는 일

멤버(필드, 메서드, 중첩 클래스, 중첩 인터페이스)에 부여할 수 있는 접근 수준은 네가지
1. private : 멤버를 선언한 톱레벨 클래스에서만 접근할 수 있다
2. package-private:멤버가 소속된 패키지 안의 모든 클래스에서 접근할 수 있다. 접근 제한자를 명시하지 ㅇ낳았을때 적용되는 패키지 접근 수준. (단, 인터페이스의 멤버는 기본적으로 public이 적용된다)
3. protected: package-private의 접근 범위를 포함하며, 이 멤버를 선언한 클래스의 하위 클래스에서도 접근할 수 있다
4. public: 모든 곳에서 접근할 수 있다

### 클래스의 공개 API를 세심히 설계한 후, 그 외의 모든 멤버는 private으로 만들자. 

- 상위 클래스의 메서드를 재정의할 때 그 접근 수준을 상위 클래스에서보다 좁게 설정할 수 없다는 것이다(리스코프 치환 원칙: 상위 클래스의 인스턴스는 하위 클래스의 인스턴스로 대체해 사용할 수 있어야 한다는 규칙)
- 테스트만을 위해 클래스 인터페이스, 멤버를 고액 API로 만들어서는 안된다. 테스트 코드를 테스트 대상과 같은 패키지에 두면 package-private 요소에 접근할 수 있기 때문이다. 
- public 클래스의 인스턴스 필드는 되도록 public이 아니어야 한다(아이템 16)

길이가 0이 아닌 배열은 모두 변경 가능하니 주의하자. 따라서 클래스에서 public static final 배열 필드를 두거나 이 필드를 반환하는 접근자 메서드를 제공해서는 안된다
예시)
```java
//보안 허점이 존재한다
public staic final Thing[] VALUES = {...}
```
해결책 1) public 배열을 private으로 만들고 public 불변 리스트를 추가
```java
private static final Thin[] PRIVATE_VALUES = {...};
public static final List<Thing> VALUES = Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES));
```
해결책 2) 배열을 private으로 만들고 그 복사본을 반환하는 public 메서드를 추가하는 방법(방어적 복사)
```java
private static final Thing[] PRIVATE_VALUES = {...}
public static final Thing[] values() {
    return PRIVATE_VALUES.clone()
}
```
- java 9 에서는 모듈 시스템이라는 개념이 도입되면서 두가지 암묵적 접근 수준이 추가되었다. 패키지가 클래스들의 묶음이듯, 모듈은 패키지들의 묶음이다.
- 하지만 이 새로운 두 접근 수준은 상당히 주의해서 써야한다
- public클래스는 상수용 public static final 필드 외에는 어떠한 public 필드도 가져서는 안된다. public static final 필드가 참조하는 객체가 불변인지 확인해야한다

> Item 16: public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라

퇴보한 클래스 예시) 
```java
class Point {
    public double x;
    public double y;
}
```
이 예시는 캡슐화나 객체 지향 클래스를 구현하지 못하였다. 필드를 모두 private으로 바꾸고 public 접근자(getter)를 추가한다
올바른 예시)
```java
class Point {
    private double x;
    private double y;
    
    public Point(double x, double y) {
        this.x = x;
        this.y = y;
    }
    
    public double getX() { return x; }
    public double getY() { return y; }
}
```
이런 식으로 코드를 짜면 패키지 바깥에ㅓ 접근할 수 있는 접근자(getter)를 제공함으로써 클래스 내부 표현 방식을 언제든 바꿀 수 있는 유연성을 얻을 수 있다.
public 클래스가 필드를 공개하면 이를 사용하는 클라이언트가 생겨날 것이므로 내부 표현 방식을 마음대로 바꿀 수 없게 된다

하지만 package-private 클래스 혹은 private 중첩 클래스라면 데이터 필드를 노출한다 해도 하등의 문제가 없다

* package-private에 대한 추가 공부
예시 코드
```java
package user;

public class UserClass {

	private String name;

	String getName() {
		return name;
	}
}
```
```java
package user;

public class OtherClass {

	private final UserClass userClass;

	public OtherClass() {

		this.userClass = new UserClass();
	}
	
	public String getName() {
		return this.userClass.getName();	
	}
}
```
package-private은 같은 패키지 내부에 있는 클래스들에만 접근할 수 있다

핵심정리 : public 클래스는 절대 가변 필드를 직접 노출해서는 안된다

> item 17 변경 가능성을 최소화하라

- 불변클래스란 간단히 말해 그 인스턴스의 내부 값을 수정할 수 없는 클래스다
- 불변 인스턴스에 간직된 정보는 고정되어 객체가 파괴되는 순간까지 절대 달라지지 않는다

### 클래스를 불면으로 만들려면 다음 다섯 가지 규칙을 따르면 된다
- 객체의 상태를 변경하는 메서드를 제공하지 않는다
- 클래스를 확장할 수 없도록 한다. 하위 클래스에서 부주의하게 혹은 나쁜의도로 객체의 상태를 변하게 만드는 사태를 막아준다.
- 모든 필드를 final로 선언한다
- 모든 필드를 private으로 선언한다
- 자신외에는 내부의 가변 컴포넌트에 접근할 수 없도록 한다 
  - 함수형 프로그래밍은 코드에서 불변이 되는 영역의 비율이 높아지는 장점이 있다


- 뷸뵨 클래스라면 한번 만든 인스턴스를 최대한 재활용하자. 불변 객체는 근본적으로 스레드 안전하여 따로
  동기화할 필요가 없기 때문이다.
- 클래스는 꼭 필요한 경우가 아니라면 불변이어야 한다
- 불변으로 만들 수 없는 클래스라도 변경할 수 있는 부분을 최소한으로 줄여야 한다
- 다른 합당한 이유가 없다면 클래스의 모든 필드는 private final이어야 한다
- 생성자는 불변식 설정이 모두 완료된, 초기화가 완벽히 끝난 상태이 객체를 생성해야 한다
- 확실한 이유가 ㅇ벗다면 생성자와 정적 팩터리 외에는 그 어떤 초기화 메서드도 public으로 제공해서는 안된다


> item 18 상속보다는 컴포지션을 사용하라

- 상속은 코드를 재사용하는 강력한 수단이지만 항상 최선은 아니다. 잘못 사용하면 오류를 내기 쉬운 소프트웨어를 만든다
- 일반적인 구체 클래스를 패키지 경계를 넘어, 즉 다른 패키지의 구체 클래스를 상속하는 일은 위험하다
- 메서드 호출과 달리 상속은 캡슐화를 깨뜨린다

```java
public class MyHashSet<E> extends HashSet<E> {
    private int addCount = 0; // 추가된 원소의 개수

    @Override
    public boolean add(E e) {
        //여기도 실행되어서 중복값이 출력되는 것이다
        addCount++;
        return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        //여기도 실행되고
        addCount = addCount + c.size(0;
        return super.addAll(c);
    }

    public int getAddCount() {
        return addCount;
    }
}

// 객체 생성 후 3개의 엘리먼트를 addAll 메서드로 추가
MyHashSet<String> mySet = new MyHashSet<>();
mySet.addAll(List.of("탱1","탱2","탱3"));

// 출력되는 값은?
System.out.println(mySet.getAddCount());
```

addCount의 값이 3이 나올 것으로 기대했지만 실제로는 6이 반환된다. 원인은 바로 HashSet의 addAll 메서드가
add메서드를 사용하여 구현되었기 때문이다

```java
// HashSet(AbstractSet)의 addAll 메서드
public boolean addAll(Collection<? extends E> c) {
    boolean modified = false;
    for (E e : c)
        if (add(e))
            modified = true;
    return modified;
}
```

addAll메서드에는 각 요소를 add메서드를 호출해서 추가하므로 addCount를 증가시키는 코드가 없어야 한다

해결방법? 메서드를 재정의하는 것보다 새로 만들자!
- 기존 클래스를 *확장하는* 대신에 새로운 클래스를 만들고 private field로 기존 클래스의 인스턴스를
    참조하게 하면 된다. 기존 클래스가 새로운 클래스의 구성요소로 쓰인다는 뜻에서 이를 컴포지션이라고 한다
- 새로운 클래스의 인스턴스 메서드들은 기존 클래스에 대응하는 메서드를 호출해 그 결과를 반환한다. 이를 전달이라고 한다
- 기존 클래스를 확장하는 대신에 새로운 클래스를 만들고 private 필드로 기존 클래스의 인스턴스를 참조하게 하면 됩니다. 기존 클래스가 새로운 클래스의 구성요소로 쓰인다는 뜻에서 이를 컴포지션(Composition)이라고 한다

```java
public class MySet<E> extends ForwardingSet<E>  {
    private int addCount = 0;

    public MySet(Set<E> set) {
        super(set);
    }

    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> collection) {
        addCount = addCount + collection.size();
        return super.addAll(collection);
    }

    public int getAddCount() {
        return addCount;
    }
}

public class ForwardingSet<E> implements Set<E> {
    private final Set<E> set;
    public ForwardingSet(Set<E> set) { this.set = set; }
    public void clear() { set.clear(); }
    public boolean isEmpty() { return set.isEmpbty(); }
    public boolean add(E e) { return set.add(e); }
    public boolean addAll(Collection<? extends E> c) { return set.addAll(c); }
    // ... 생략
}
```

- 인스턴스를 감싸고 있다는 뜻에서 MySet과 같은 클래스를 래퍼 클래스라고 하며 다른 Set에 계측 기능을 덧씌운다는 뜻에서 데코레이터 패턴(Decorator Pattern)이라고 합니다

그럼 상속은 언제?
- 클래스 B가 클래스 A와 is-a관계일때만 사용해야 한다


## 질문
- BigSet랑 BigInteger의 큰 차이는? -4장할 때
- 정적 팩터리 방식에 대해 얘기해보고 싶다

int / BigInteger 