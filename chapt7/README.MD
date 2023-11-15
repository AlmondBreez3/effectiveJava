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