# 람다와 스트림
1. item 42 : 익명 클래스보다는 람다를 사용하라
    
    ```java
   Collections.sort(words, (s1,s2 -> Integer.compare(s1.length(), s2.length())));
   ```
   자바에서 "타입 추론 규칙"은 자바 언어 명세의 장하나를 통째로 차지할 만큼 복잡하다
   ```java
   public enum Operation {
            PLUS("+") {
                public double apply(double x, double y) { return x + y;}
            },
            MINUS("-") {
                public double apply(double x, double y) { return x - y;}
            },
            TIMES("*") {
                public double apply(double x, double y) { return x * y;}
            },
            DIVIDE("/") {
                public double apply(double x, double y) { return x / y;}
            },
   }
      
   ```
   - 람다를 사용하면 열거 타입에 인스턴스 필드를 두는 편이 낫다. 열거 타입의 인스턴스 필드를 이용하는 방식으로 상수별로 
   다르게 동작하는 코드를 쉽게 구현할 수 있다
   - 람다는 이름이 없고 문서화도 못한다
   - 코드 자체로 동작이 명확히 설명되지 않거나 코드 줄 수가 많아지면 람다를 쓰지 말아야 한다
   - 람다는 자신을 참조 할 수 없다
   - 람다 표현식은 함수형 인터페이스에서 사용할 때 주로 활용되며, 
   이를 사용하면 코드가 간결해지고 가독성이 향상됩니다. 그러나 람다 표현식을 사용할 수 있는 조건은 함수형 인터페이스를 사용해야 한다는 것이며, 
   그 외의 인터페이스나 클래스에서는 람다 표현식을 사용할 수 없습니다. 

# 이펙티브자바7장

> 💡 Item 42 :익명 클래스보다는 람다를 사용하라
>

JDK 1.1 이후

함수 객체를 만드는 주요 수단은 익명 클래스가 되었다.

익명 클래스란, 별도의 클래스 선언으로 확장하지 않고 코드부에서 바로 구현하는 기술이다. 일회성으로 사용하고 버려지는 경우 따로 클래스를 생성하는 비용을 줄일 수 있다는 장점이 있다

```java
Collections.sort(words, new Comparator<String>() {
	public int compare(String s1, String s2){
     	return Integer.compare(s1.length(), s2.length());
   }
});
```

Comparator 인터페이스는 정렬을 담당하는 추상 전략을 뜻하며, 문자열을 정렬하는 구체적인 전략을 익명 클래스로 구현하고 있기 때문에 전략 패턴과도 비슷하다.

JAVA 8 등장

함수형 인터페이스라 부르는 인터페이스들의 인스턴스를 **람다식(lambda expression)**을 사용해 만들 수 있게 되었다. 람다 함수는 익명 클래스보다 훨씬 간결하기 때문에, 어떤 동작을 하는지가 명확히 드러난다는 장점이 있다.

```java
Collections.sort(words, (s1,s2) -> Integer.compare(s1.length(), s2.length()));
```

### 람다의 타입 추론

람다는 컴파일러가 대신 문맥을 살펴 타입을 추론해주기 때문에, 코드에서 생략해도 괜찮다. **따라서 타입을 명시해야 코드가 명확할 때만 제외하고는 람다의 모든 매개변수 타입은 생략하자.**

- 람다와 제네릭: 컴파일러는 타입 정보 대부분을 제네릭에서 얻기 때문에, 제네릭을 사용하라는 조언들은 람다와 함께 쓸 때 더욱 중요해진다.  제네릭 타입이아니라 raw 타입을 사용하면 타입 추론이 안되어 컴파일 오류가 발생한다

- 비교자 생성 메서드 : 람다 자리에 비교자 생성 메서드를 사용하면 코드를 더 간결하게 만들 수 있다

```java
Collections.sort(words,comparingInt(String::length));
words.sort(comparingInt(String::length));
```

### 예시 : Enum 타입

```java
public enum Operation {
    PLUS  ("+") {
    	public double apply(double x, double y) {return x + y; }
    },
    MINUS ("-") {
    	public double apply(double x, double y) {return x + y; }
    },  
    ...
}
```

람다 사용 후:

```java
public enum Operation {
    PLUS  ("+", (x, y) -> x + y),
    MINUS ("-", (x, y) -> x - y),
    TIMES ("*", (x, y) -> x * y),
    DIVIDE("/", (x, y) -> x / y);

    private final String symbol;
    private final DoubleBinaryOperator op;  // 함수형 인터페이스 

    Operation(String symbol, DoubleBinaryOperator op) {
        this.symbol = symbol;
        this.op = op;
    }

    @Override public String toString() { return symbol; }

    public double apply(double x, double y) {
        return op.applyAsDouble(x, y);
    }
}
```

### 주의점

1. 적은 코드로 구현하기 어렵거나 내부에서 인스턴스 필드나 메서드를 사용해야 한다면 상수별 클래스 몸체를 사용하자
2. 람다는 함수형 인터페이스에서만 사용가능하다
3. 람다는 자기 자신을 참조할 수 없다 (this사용 불가)
4. 람다를 직렬화하는 일은 극히 삼가야 한다

### 익명 클래스 단점과 함수형 프로그래밍

익명 클래스는 여전히 코드가 길어지기 때문에 함수형 프로그래밍에 적합하지 않다

한수형 프로그래밍은 선언적 프로그래밍, 즉 어떻게가 아닌 무엇을 달성한지에 초점을 맞춘다.

명령형 프로그래밍 예시

```java
public int add(int[] arr) {  // 명령형 프로그래밍
    int result = 0;
    for (int i = 0; i < arr.length; i++){
      result += arr[i];
    }
    return result;
}
```

1. 배열을 반복해서 더하는 모든 과정을 자세하게 설명하고 있다
2. 메모리에 저장된 정보, 즉 상태를 변화시키고 있다
3. 무슨 일이 일어나고 있는지 코드를 분석해야 하기 때문에 가독성이 떨어진다

함수형 프로그래밍 예시

```java
public int add(int[] arr) {
  return Arrays.stream(arr)
  			.reduce((prev, current) => prev + current) // Integer::sum
            .getAsInt();
}
```

1. 어떻게가 아닌, 필요한 데이터인 “무엇”에 대해 집중할 수 있다
2. 상태를 변경하는 지점들이 map, reduce 내부로 추상화 되었기 때문에 클라이언트 코드에서 직접 상태를 변경하지 않아도 된다
3. map과 reduce 함수에만 익숙하다면 가독성이 높다

> 📚**핵심 정리**
>
>
> 익명 클래스는 함수형 인터페이스가 아닌 타입의 인스턴스를 만들 때만 사용하고, 작은 함수 객체를 쉽게 표현할 수 있는 람다를 사용하는 것이 좋다.
>

> 💡 Item 43: 람다보다는 메서드 참조를 사용하라
>

메서드 참조란, 함수 객체를 람다보다 간결하게 만드는 방법이다

람다

```java
map.merge(key, 1, (count,incr) -> count + incr);
```

- 키, 값, 함수를 인자로 받으며 주어진 키가 맵 안에 아직 없다면 주어진 키,값 쌍을 그대로 저장하고 키가 이미 있다면 키,인자로 받은 함수의 결과쌍을 저장한다
- 하지만 현재 람다는 단순 두 인수의 합을 반환할 뿐이기 때문에 불필요한 코드 부분(매개변수를 받는 부분)이 존재한다

메서드 참조

```java
map.merge(key,1,Integer::sum);
```

장점: 더 짧고 간결한 코드를 생성할 수 있다

즉, 람다로 구현했을 때 너무 길고 복잡하면 람다로 작성할 코드를 새로운 메서드에 담은 다음, 람다 대신 그 메서드 참조를 사용하면 된다

```java
메서드와 람다가 같은 클래스에 있는 경우 람다가 더 간결하다.

service.excecute(CoshThisClassNameIsHumongous::action);
service.execute(() -> action());
```

### 메서드 참조 유형

1. 정적 메서드 참조

```java
Integer::parseInt
//람다
str -> Integer.parseInt(str)
```

1. 한정적

```java
Instant.now()::isAfter
//람다
Instant then = Instant.now(); t -> then.isAfter(t)
```

1. 비한정적 인스턴스 메서드 참조

```java
String::toLowerCase
//람다
str -> str.toLowerCase()
```

1. 클래스 생성자

```java
TreeMap<K,V>::new
//람다
() -> new TreeMap<K,V>()
```

1. 배열 생성자

```java
int[]::new
//람다
len -> new int[len]
```

### 예외

람다로는 불가능하나 메서드 참조로는 가능한 유일한 예는 바로 제네릭 함수 타입 구현이다

```java
interface G1{
	<E extends Exception> Object m() throws E;
}
interface G extends G1, G2 {}
함수형 인터페이스로 F를 함수 타입으로 표현하면 다음과 같다
<F extends Exception> () -> String throws F
```

> 💡ITEM 44: 표준 함수형 인터페이스를 사용해라
>
- Map의새로운 키를 추가하는 put 메서드에서 removeEldestEntry메서드를 호출해 true가 반환되면 맵에서 가장 오래된 원소를 제거한다. LinkedHashMap에서 removeEldestEntry를 다음과 같이 재정의해서 캐시로 사용할 수 있다

```java
//기존
protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
  return false;
}
//변경 
@Override
protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
  return size() > 100; // 최근 원소 100개 유지
}
//람다로 구현한 함수형 인터페이스
@FunctionInterface interface EldestEntryRemovalFunction<K, V> {
    boolean remove(Map<K,V> map, Map.Entry<K, V> eldest);
}
```

위의 EldestEntryRemovalFunction도 동작하지만 자바 표준 라이브러리에서 이미 제공해주므로 굳이 사용할 필요가 없다

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cf3cd22c-4430-4410-ad02-80d2a1605c55/486832b9-551f-4c62-8aa9-d7942a25c075/Untitled.png)

### 직접 구현

표준 함수형인터페이스 중 필요한 용도에 맞는게 없다면 직접 구현해야한다

@FunctionInterface

- `@FunctionInterface` 어노테이션은 프로그래머의 의도를 명시하는 것으로 3가지 목적이 있다.
   1. 해당 인터페이스가 람다용으로 설계된 것임을 명시
   2. 해당 인터페이스가 추상 메서드를 오직 한개만 가지고 있어야 컴파일 가능
   3. 유지보수 과정에서 누군가 실수로 메서드를 추가하지 못하게 막아줌
- 주의점
   - 서로 다른 함수형 인터페이스를 같은 위치의 인수로 받는 메서드를 다중정의해서는 안된다. 클라이언트에게 불필요한 모호함만 주며, 다음과 같이 모호함으로 인해 문제가 발생할 수 있다

    ```java
    public interface ExecutorService extends Executor {
        <T> Future<T> submit(Callback<T> task);
        Future<?> submit(Runnable task);
    }
    //ExecutorService 인터페이스는 Callable<T>와 Runnable을 각각 인수로 하여 다중정의했다. 
    //올바른 메서드를 알려주기 위해서는 submit 메서드를 사용할 때마다 형변환을 해줘야한다
    ```


> 💡ITEM 45: 스트림은 주의해서 사용해라
>

스트림이란? 데이터 원소의 유한/무한 시퀀스를 뜻함

스트림 파이프라인이란? 원소들로 수행하는 연산 단계를 표현

- 스트림의 원소는 어디로부터든 올 수 있으며, 대표적으로 배열, 컬렉션, 파일 등을 통해서 만들 수 있다

### 스트림 파이프라인

스트림을 생성하는 연산으로 종단연산을 통해 끝나며 그 사이에 스트림을 변환하거나 계산하는 한 개 이상의 중간 연산이 포함 될 수 있다

- 지연 평가
   - 평가는 종단 연산이 호출될때 진행되며, 종단 연산에 사용되지 않은 데이터 원소는 계산에 쓰이지 않는다. 이러한 지연 평가가 무한 스트림을 다룰 수 있게 해주는 핵심이다.
   - 종단 연산이 없는 스트림 파이프라인은 아무 일도 하지 않는 명령어 no-op과 같으니 종단 연산을 뺴먹는 일이 절대 없도록 하자

스트림을 제대로 사용하면 프로그램이 짧고 깔끔해지만, 잘못 사용하면 읽기 어렵고 유지보수도 힘들어진다

```java
//스트림을 과하게 사용하는 예
public class Anagrams {
    public static void main(String[] args) throws IOException {
        Path dictionary = Paths.get(args[0]);
        int minGroupSize = Integer.parseInt(args[1]);

        try (Stream<String> words = Files.lines(dictionary)) {
            words.collect(groupingBy(word -> word.chars().sorted()
                    .collect(StringBuilder::new,
                        (sb, c) -> sb.append((char) c),
                        StringBuilder::append).toString()))
                .values().stream()
                .filter(group -> group.size() >= minGroupSize)
                .map(group -> group.size() + ": " + group)
                .forEach(System.out::println);
        }
    }
}
```

적절히 사용한 예

```java
public class Anagrams {
    public static void main(String[] args) {
        Path dictionary = Paths.get(args[0]);
        int minGroupSize = Integer.parseInt(args[1]);

        try (Stream<String> words = Files.lines(dictionary)) {
            words.collect(groupingBy(word -> alphabetize(word)))
                .values().stream()
                .filter(group -> group.size() >= minGroupSize)
                .forEach(g -> System.out.println(g.size() + ": " + g));
        }
    }

    private static String alphabetize(String s) {
        char[] a = s.toCharArray();
        Arrays.sort(a);
        return new String(a);
    }
}
```

**스트림을 사용하기 좋은 경우**

- 원소들의 시퀀스를 일관되게 변환
- 원소들의 시퀀스를 필터링
- 원소들의 시퀀스를 하나의 연산을 사용하여 결합(더하기, 최솟값 구하기 등)
- 원소들의 시퀀스를 컬렉션에 모으는 경우
- 원소들의 시퀀스에서 특정 조건을 만족하는 원소를 찾는 경우

**스트림으로 처리하기 어려운 일**

- 한 데이터가 파이프라인의 여러 단계를 통과할 때, 각 단계에서 값들에 동시에 접근하기 어려운 경우
   - 스트림은 한 값을 다른 값에 매핑하고 나면 원래의 값은 잃는 구조
- 매핑 객체가 필요한 단계가 여러 곳인 경우

## 결론

스트림으로 바꾸는게 가능하더라도 코드 가독성과 유지보수 측면에서 손해볼 수 있기때문에 기존 코드는 스트림을 사용하도록 리팩터링하되, 새 코드가 더 나아 보일때만 반영해야한다.

즉, **스트림과 반복 중 어느쪽이 나은지 확신하기 어렵다면, 둘다 구현해보고 더 나은 쪽을 정하는 것**을 권장한다.

> 💡 I****TEM 46: 스트림에서 부작용 없는 함수를 사용해라****
>

## 스트림 패러다임

스트림 패러다임의 핵심은 **계산을 일련의 변환으로 재구성하는 부분**이다. 각 변환 단계는 가능한 이전 단계의 결과를 받아 처리하는 **순수 함수**여야한다.

- 순수함수 : 오직 입력만이 결과에 영향을 주는 함수
   - 다른 가변 상태를 참조하지 않음
   - 함수 스스로 다른 상태를 변경하지 않음

### 스트림 패러다임은 이해하지 못한채 API만 사용한 예

```java
Map<String, Long> freq = new HashMap<>();
try(Stream<String> words = new Scanner(file).tokens()) {
  words.forEach(word -> {
    freq.merge(word.toLowerCase(), 1L, Long::sum);
  });
}
//forEach : 스트림이 수행한 연산 결과를 보여줄 때 사용하고, 계산할 때는 사용하지 말자.
//(스트림 계산 결과를 기존 컬렉션에 추가하는 등 다른 용도로도 쓸 수 있다.)

//올바르게 사용한 것
Map<String, Long> freq;
try(Stream<String> words = new Scanner(file).tokens()) {
  freq = words.collect(groupingBy(String::toLowerCase, counting()));
}
```

**Collector**

- `java.util.stream.Collectors`  : 자주 사용하는 API 제공`Collectors` 의 멤버를 정적 임포트(static import)해 사용하면, 스트림 가독성이 좋아짐
- 스트림의 원소를 손쉽게 컬렉션으로 생성 가능
- 최종 처리(스트림 종료 작업)

toList()

```java
List<String> topTen = freq.keySet().stream()
  .sorted(comparing(freq::get).reversed()) // Comparator.comparing
  .limit(10)
  .collect(toList()); // List 형태로 반환
```

toMap()

- `toMap(keyMapper, valueMapper)` : 각 원소가 고유한 키에 매핑되어 있을 때 적합

```java
private static final Map<String, Operation> stringToEnum = 
Stream.of(values()).collect(toMap(Object::toString, e -> e));
```

- 인수 3개 받는 `toMap` : 어떤 키와 그 키에 연관된 원소들 중 하나를 골라 연관 짓는 맵을 만들때 유용

```java
Map<Artist, Album> topHits = albums.collect(
    toMap(Album::artist, a->a, maxBy(comparing(Album::sales)))); //
```

- 마지막에 쓴 값을 취하는 수집기

```java
toMap(keyMapper, valueMapper, (oldVal, newVal) -> newVal);

Stream<String> s = Stream.of("apple", "banana", "apricot", "orange", "apple");
Map<Character, String> m = s.collect(Collectors.toMap(s1 -> s1.charAt(0), s1 -> s1, (oldVal, newVal) -> oldVal + "|" + newVal)); 
// {a=apple|apricot|apple, b=banana, o=orange}
```

- 네번째 인수로 맵 팩터리(`EnumMap`, `TreeMap`, `HashMap`)를 받는 toMap

```java
Stream<String> s = Stream.of("apple", "banana", "apricot", "orange", "apple");
  LinkedHashMap<Character, String> m = s.collect(
                 Collectors.toMap(s1 -> s1.charAt(0), s1 -> s1, (s1, s2) -> s1 + "|" + s2,
                                                  LinkedHashMap::new));
```

groupingBy()

입력으로 분류 함수(classifier)를 받고 출력으로 원소들을 카테고리별로 모아 놓은 맵을 담은 수집기 반환한다.

```java
L// 알파벳화한 단어를 알파벳화 결가가 같은 단어들의 리스트로 매핑하는 맵 생성
words.collect(groupingBy(word -> alphabetsize(word)))
```

> 💡 I****TEM 47: 반환 타입으로는 스트림보다 컬렉션이 낫다.****
>
- `Collection` 인터페이스는 `Iterable`의 하위 타입이고, `stream` 메서드도 제공하여 즉, 반복과 스트림을 동시에 지원한다.

스트림 반복문

- Stream -> Iterable 어댑터

```java
public static <E> Iterable<E> iterableOf(Stream<E> stream){
  return stream::iterator;
}
```

- `Stream<E>`를 `Iterable<E>`로 중개해주는 어댑터를 사용하면 어떠한 스트림도 for-each 반복문을 사용할 수 있다.

```java
for(ProcessHandle p : iterableOf(ProcessHandle.allProcesses())){
  // 프로세스 처리 로직
}
```

- Iterable -> Stream 어댑터

```java
public static <E> Stream<E> streamOf(Iterable<E> iterable){
  return StreamSupport.stream(iterable.spliterator(), false);
}
```

전용 컬렉션 구현

- **반환할 시퀀스가 크지만 표현을 간결하게 할 수 있다면 전용 컬렉션을 구현하는 방안을 검토**하는 것이 좋다

`AbstractCollection`을 활용해 `Collection` 구현체를 작성할때는 아래 3개 메서드는 반드시 구현해야한다.

- `Iterable`용 메서드
- `contains`
- `size`

- 만약, `contains`와 `size`를 구현하는게 불가능한 경우 `Stream` 이나 `Iterable`로 구현하는 것이 낫다.

> 💡 ****ITEM 48: 스트림 병렬화는 주의해서 사용해라****
>

안정성과 응답가능 상태 유지

- 동시성 프로그래밍을 할 때는 안정성(safety)과 응답 가능(liveness) 상태를 유지하기 위해 노력해야하는데, 병렬 스트림 파이프라인 프로그래밍에서도 동일하다.

예시)

- 스트림을 사용해 20개의 메르센 소수를 생성하는 프로그램이다.

```java
public static void main(String[] args) {
  primes().map(p -> TWO.pow(p.intValueExact()).subtract(ONE))
        .filter(mersenne -> mersenne.isProbablePrime(50))
        .limit(20)
        .forEach(System.out::println);
}

static Stream<BigInteger> primes() {
  return Stream.iterate(TWO, BigInteger::nextProbablePrime);
}
```

- . 데이터 소스가 `Stream.iterate()` 이거나 중간 연산으로 `limit()`을 사용하면 파이프라인 병렬화로는 성능 개선을 할 수 없다. 즉, **스트림 파이프라인을 마구잡이로 병렬화하면 안되며, 오히려 성능이 나빠질 수 있다.**

1. 병렬화 하기 좋은 경우: **참조 지역성이 뛰어난 경우**
- `ArrayList`
- `HashMap`
- `HashSet`
- `ConcurrentHashMap`
- 배열
- int 범위
- long 범위
- 위 자료구조들은 **모두 데이터를 원하는 크기로 정확하고 쉽게 나눌 수 있어, 일을 다수의 스레드에 분배하기 좋다**.
- 원소들을 순차적으로 실행할 때 **참조 지역성이 뛰어나다**. (*참조지역성 : 이웃한 원소의 참조들이 메모리에 연속해서 저장되어 있음.*)

1. 종단 연산 - 축소(reduction)
- 종단 연산에서 수행하는 작업량이 파이프라인 전체 작업에서 상당 비중으로 차지하며, 순차적인 연산이라면 파이프라인 병렬 수행의 효과는 제한될 수 밖에 없다
- 축소(reduction)는 파이프라인에서 만들어진 모든 원소를 하나로 합치는 작업이다.

- reduce 메서드
- min, max, count, sum 완성된 형태로 제공되는 메서드
- `anyMatch`, `allMatch`, `noneMatch` 와 같이 조건에 맞으면 바로 반환하는 메서드

위 메서드는 병렬화에 적합하지만, 가변 축소를 수행하는 Stream의 `collect` 메서드는 컬렉션들을 합치는 부담이 크기때문에 병렬화에 적합하지 않다.

```
Stream 타입이 나은 예: 부분 리스트를 스트림으로 변환하여 처리하기
```

출처:

https://jake-seo-dev.tistory.com/459

[제이크서 위키 블로그:티스토리]

마무리

- **스트림을 잘못 병렬화하면 성능이 나빠질 뿐만 아니라 결과 자체가 잘못되거나 예상 못한 동작(safety failure)이 발생할 수 있다.**
   - `Stream` 명세대로 동작하지 않을 때, 발생할 수 있음
   - 예를들어, Stream reduce 연산의 `accumulator`와 `combiner` 함수는 반드시 결합 법칙을 만족하고, 간섭받지 않고, 상태를 갖지 않아야한다.
- 위 조건을 다 만족하더라도, 병렬화에 드는 추가 비용을 상쇄하지 못한다면, 성능 향상이 미미할 수 있음.
   - 스트림 안의 원소 수와 원소당 수행되는 코드 줄 수를 곱해 수십만이 되어야 성능향상을 느낄 수 있다.
- 스트림 병렬화는 오직 성능 최적화 수단이다.
   - 변경 전후로 테스트해 병렬화 사용에 가치가 있는지 확인해야한다.
- 계산이 정확하고, 확실히 성능이 좋아졌을 경우에만 병렬화를 실 운영에 적용해야한다.
- 조건이 잘 갖춰지면, parallel 메서드 호출 하나로 프로세서 코어 수에 비례하는 성능 향상을 만끽할 수 있다.