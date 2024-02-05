## 매개 변수

정적 팩터리 메서드나 생성자에서 매개변수가 많을 때 똑같이 대응하기 힘들다는 애로 사항이 있다.

- 점층적 생성자 패턴

```agsl
public class Test {
    private final int a;
    private final int b;
    private final int c;
    
    public Test(int a, int b, int c) {
        this.a = a;
        this.b = b;
        this.c = c;
    }
    
    // 정적 팩터리 메서드
    public static titleFrom(int a, int b, int c) {
        return new Test(a,b,c);
    }
}
```

위 방법은 매개변수의 숫자가 많아지면 많아질수록 가독성이 떨어지고, 또 값을 할당할 때 다른 매개변수에
넣는 실수를 할 가능성도 있다.

그래서 등장한 다음 대안이 자바 빈즈 패턴이다. 

```
public class Test {
    private int a = 0;
    private int b = 0;
    private int c = 0;
    
    public Test() {
        public void setA(int a) {
            this.a = a;
        }
        public void setB(int b) {
            this.b = b;
        }
        public void setC(int c) {
            this.c = c;
        }
    }
}
```

위 방법은 점층적 생성자 패턴에 비해서 인스턴스를 만들기 쉽고 (매개 변수를 가지고 있지 않기 때문에)
가독성 측면에서도 더 낫다.

- 일관성 문제

만약에 Test 인스턴스를 만들고 setB() 메서드나 setC() 메서드를 호출하지 않았다면, 일관성이 무너진 상태가 되고 이는 런타임 에러로 이어질 수 있다.

- 불변성 문제

setter를 외부로 노출하고 있으므로 외부에서 변경이 가능하다. 이러한 방식은 불변성을 보장할 수 없다

## Builder 패턴
앞서 말한 점층적 생성자 패턴과 자바 빈즈 패턴의 장점만 규합한 방식이다.

```agsl
//기존 방식
Test test = new Test(0, 0, 0);

Test test = Test.builder()
              .a(0)
              .b(0)
              .c(0).build();
```

* 필요하지 않은 데이터는 생략 가능

만약에 c라는 매개변수의 값이 필요없다고 가정하자. 생성자를 이용하는 방식은 필요없다고 하더라도 반드시 값을 보내주어야 한다.

하지만, 빌더 패턴에서는 생략할 수 있다.

* 파라미터 순서가 중요하지 않음

앞선 생성자 방식에서는, 파라미터의 순서가 바뀐다면 치명적인 문제가 발생하지만, 빌더 패턴은 파라미터 순서대로 값을 넣어주지 않아도 된다.