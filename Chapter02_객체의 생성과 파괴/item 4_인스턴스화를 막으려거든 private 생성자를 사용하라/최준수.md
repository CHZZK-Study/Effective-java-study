
static 변수들을 모아놓은 유틸리티 클래스 같은 경우는, 인스턴스를 만들기 위해서 설계된 클래스가 아니다.

그렇기 때문에 인스턴스화를 막아주는 것이 좋다.

* 추상 클래스로 인스턴스화를 막기?

-> 하위 클래스를 만들어서 인스턴스 화를 한다면, 막을 수 없음

### private 생성자

private 생성자를 통해 인스턴스화를 막을 수 있다.

* 하위 클래스에서 상위 클래스를 호출할 수 없으므로 상속 불

```agsl
private Class UtilityClass {
    
    private Utility Class() {
        throw new AssertionError();
    }
    
    public static String printMessage() {
        return "message";
    }
}
```

```
public static void main(String[] args) {    
    // 생성자 호출 불가능
    // UtilityClass uc = new UtilityClass();
    UtilityClass.printMessage();
}
```