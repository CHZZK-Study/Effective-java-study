# 3. private 생성자나 열거 타입으로 싱글턴임을 보증하라

싱글턴(singleton)이란 인스턴스를 오직 하나만 생성할 수 있는 클래스를 말한다.

싱글턴을 만드는 대표적인 방식에는 2가지가 있다.(해당 글에서는 총 3가지의 싱글턴 방식을 소개한다.) 두 방식 모두 생성자는 private으로 감춰두고, 유일한 인스턴스에 접근할 수 있는 수단으로 public static 멤버를 하나 마련해둔다. 두가지 방법은 public static 멤버가 어떤식으로 제공되느냐에 차이가 존재한다.


### 1. public static 멤버가 final 필드인 방식
``` java
public class Yeseul {
    public static final Yeseul INSTANCE = new Yeseul();
    private Yeseul() { ... }

    public void readingTheBook() { ... }
}
```

해당 코드에서 private 생성자는 public static final 필드인 ```Yeseul.INSTANCE```를 초기화할 때 딱 한 번만 호출된다. 그러므로 전체 시스템에서 하나뿐임이 보장된다.

해당 방식의 장점은 해당 클래스가 싱글턴임이 API에 명백히 드러난다는 점과 간결함이다.

### 2. 정적 팩터리 메서드를 public static 멤버로 제공하는 방식
``` java
public class Yeseul {
    private static final Yeseul INSTANCE = new Yeseul();
    private Yeseul() { ... }
    public static Yeseul getInstance() { return INSTANCE; }

    public void readingTheBook() { ... }
}
```

해당 코드는 항상 ```Yeseul.getInstance()```로 같은 객체의 참조를 반환하는 방식으로 사용된다.

해당 방식의 장점은
1. API를 바꾸지 않고도 싱글턴이 아니게 변경 가능
2. 원한다면 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있음
3. 정적 팩터리의 메서드 참조를 공급자로 사용할 수 있음

해당 방식들이 굳이 필요없다면 1번 방식인 public 필드 방식이 좋다.

둘 중 하나의 방식으로 구현된 싱글턴 클래스를 직렬화하려면 readResolve 메서드를 제공해주어야 하며, 그렇지 않으면 직렬화된 인스턴스를 역직렬화 할 때마다 새로운 인스턴스가 만들어진다.

``` java
private Object readResolve() { // 싱글턴 보장하기
    // 진짜 Yeseul을 반환하고, 새로 생성되는 가짜 Yeseul은 가비지 컬렉터에 맡긴다.
    return INSTANCE;
}
```

### 3. 열거 타입 방식

``` java
public enum Yeseul {
    INSTANCE;

    public void readingTheBook() { ... }
}
```

해당 싱글턴이 Enum 외의 클래스를 상속해야 한다면 해당 방법을 사용할 수는 없겠지만, 다른 방식에 비해 더 간결하고 추가 노력 없이 직렬화 가능하기 때문에 대부분의 상황에서는 열거 타입이 싱글턴을 만드는 가장 좋은 방법일 것이다.