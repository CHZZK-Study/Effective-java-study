## 싱글턴

싱글턴이란, 인스턴스를 오직 하나만 생성할 수 있는 클래스이다.

```agsl
public Class Singleton {
    
    public static final Singleton instance = new Singleton();
    
    // 정적 팩토리 메서드를 이용한 방식
    private static final Singleton instance = new Singleton();
    
    private Elvis() {
    
    }
    
    public static Singleton getInstance() {
        return instance;
    }
}
```

싱글톤 패턴의 장점은, 객체가 한번만 만들어지기 때문에 메모리 측면에서 많은 이점을 얻을 수 있다. 또한, 이미 생성된 객체를 사용하므로 속도 측면에서도 이점이 있다.

또한, static으로 선언했으므로 아무 클래스에서도 접근이 쉽기 때문에 데이터 공유가 쉽다.


### Enum 클래스 사용


```agsl
public enum Singleton {
    INSTANCE;
    
    public void ~~()
}
```

enum 클래스에서 선언한 상수들은 클래스가 실행될 떄 하나의 인스턴스로 생성되어서 싱글톤 형태로 사용된다. 이는 직렬화 상황이나 리플렉션 공격에도 제 2의 인스턴스 생성을 방지해준다.

### 단점
* 동시성 문제

멀티 스레딩 환경에서 발생할 수 있는 동시성 문제 때문에 `syncronized` 키워드를 사용해야 함.

* 테스트 하기 힘들다

싱글턴은 자원을 공유하고 있기 때문에, 테스트같이 격리된 환경에서는 매번 인스턴스의 상태를 초기화 해주어야 한다. 그리고 생성 방식 때문에 동적으로 객체를 주입할 수 없기도 하다.

* 유연하지 않은 방식

클래스 안에서 직접 객체를 new 메서드를 통해서 직접 생성해주기 때문에, 클라이언트가 추상화에 의존하는 것이 아닌 구체 클래스에 의존하게 된다. 이는 `DIP (의존성 역전 원칙)` 에 위배된다.

참고자료

* https://tecoble.techcourse.co.kr/post/2020-11-07-singleton/

* https://loosie.tistory.com/849#:~:text=%EC%8B%B1%EA%B8%80%ED%86%A4%20%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%EB%8A%94%20%ED%85%8C%EC%8A%A4%ED%8A%B8,%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5%EA%B3%BC%20%EB%A9%80%EC%96%B4%EC%A7%84%EB%8B%A4.