# 아이템 15. 클래스와 멤버의 접근 권한을 최소화하라

**정보은닉**
- 모든 내부 구현을 완벽히 숨겨, 구현과 API를 깔끔히 분리하는 것
- 오직 API를 통해서만 다른 컴포넌트와 소통하며 서로의 내부 동작 방식은 별개로 한다.

- 장점
  - 시스템 개발 속도를 높인다.
  - 시스템 관리 비용을 낮춘다.
  - 정보 은닉 자체가 성능을 높여주지는 않지만, 성능 최적화에 도움을 준다.
  - 소프트웨어 재사용성을 높인다.
  - 큰 시스템을 제작하는 난이도를 낮춰준다.

- 자바의 접근 수준
  - private
    - 멤버를 선언한 클래스에서만 접근할 수 있다.
  - package-private(default)
    - 멤버가 소속된 패키지 안의 모든 클래스에서 접근할 수 있다. (클래스 멤버의 기본 접근 수준)
  - protected
    - 멤버를 선언한 클래스의 하위 클래스에서도 접근할 수 있다.
  - public
    - 모든 곳에서 접근할 수 있다. (인터페이스의 멤버 기본 접근 수준)
  - private ⊂ package-private(default) ⊂ protected ⊂ public
  
- public 클래스의 인스턴스 필드는 되도록 public이 아니여야 한다.
  - public 가변 필드를 갖는 클래스는 일반적으로 Thread-Safe 하지 않다.

- 클래스에서 public static final 배열 필드를 두거나 이 필드를 반환하는 접근자 메서드를 제공해서는 안된다.
  - 길이가 0이 아닌 배열은 모두 변경이 가능하다.
  ```
  public static final T[] VALUES = {...};
  ```
  - 해결방법 1) public 불변 리스트를 추가한다.
    ```
    private static final T[] PRIVATE_VALUES = {...};
    public static final List<T> VALUES = Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES));
    ```
  - 해결방법 2) 복사본을 반환하는 public 메서드를 추가한다. (방어적 복사)
    ```
    private static final T[] PRIVATE_VALUES = {...};
    public static final T[] values() {
      return PRIVATE_VALUES.clone();
    }
    ```
    
**프로그램 요소의 접근은 가능한 최소화 하자<br>**
**최소한의 public API를 설계하자<br>**
**public 클래스는 상수용 public static final 필드 외에는 어떠한 public 필드를 가져서는 안 된다<br>**
**public static final 필드가 참조하는 객체가 불변인지 확인하자<br>**
    
# 아이템 16. public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라

- 데이터 필드에 직접 접근할 수 있으니 캡슐화의 이점을 제공하지 못한다.
```
class Point {
  public double x;
  public double y;
}
```
- 필드를 모두 private로 바꾸고 public 접근자(getter / setter)를 추가한다.
```
class Point {
  private double x;
  private double y;
  
  public Point(double x, double y) {
    this.x = x;
    this.y = y;
  }
  
  public double getX() { return x; }
  public double getY() { return y; }
  public void setX(double x) { this.x = x; }
  public void setY(double y) { this.y = y; }
}
```
- public 클래스라면 접근자를 제공해야 하지만, pakage-private 클래스 혹은 private 중첨 클래스라면 데이터 필드를 노출한다 해도 문제가 없다.

**public 클래스는 절대 가변 필드를 직접 노출해서는 안 된다<br>**
**불변 필드라면 노출해도 덜 위험하지만 완전히 안심할 수는 없다<br>**
**package-private 클래스나 private 중첩 클래스에서는 종종 (불변이든 가변이든) 필드를 노출하는 편이 나을 때도 있다**