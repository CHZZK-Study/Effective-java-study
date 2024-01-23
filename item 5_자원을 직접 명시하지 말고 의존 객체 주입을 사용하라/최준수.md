## 의존성 주입

클래스가 내부적으로 하나 이상의 자원에 의존할 때, 싱글턴과 정적 유틸리티 클래스는 사용하지 않는 것이 좋다.

* 다른 상태를 가질 때, 멀티 스레딩 환경에서 오류 발생 가능


### 정적 유틸리티
```agsl
public class SpellChecker{
	private static final Lexicon dictionary = ...;
    
    private SpellChecker(){} // 인스턴스화 방지
    
    public boolean isValid(String word){...}
}

```

### 싱글턴
```agsl
public class SpellChecker {
	private final Lexicon dictionary = ...;
    
    private SpellChecker() {} // 인스턴스화 방지 (아이템 4 참고)
    public static SpellChecker INSTANCE = new SpellChecker(...);
    
    public static boolean isVaild(String word) {...}
}

```

위 두가지 방법 모두 word 말고 다른 단어를 테스트 할 때 문제가 발생한다.

```agsl
public static void main String[] args {
    String word = "apple";
    String word2 = "banana";
    
    SpellChecker.isValid(word);
    SpellChecker.isValid(word2);
 
    ...   
}
```

이렇게 상태에 따라 달라지는 상황에서는, 의존 객체 주입 (DI)를 통해서 인스턴스가 생성될 때 생성자에 필요한 자원을 넘겨줄 수 있다.

```agsl
public class SpellChecker{
	private final Lexicon dictionary;
    
    public SpellChecker(Lexcion dictionary){
    	this.dictionary = Objects.requireNotNull(dictionary);
    }
    
    public static boolean isValid(String word){...};
}
```

* dictionary의 값이 달라지더라도 유연하게 대처가 가능하다.