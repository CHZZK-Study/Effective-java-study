```
private static void validateNonNumber(String input) {
    String regExp = "^[0-9-]+$";
    if (!input.matches(regExp)){
        throw new IllegalArgumentException("[ERROR] 입력 값에 숫자 이외의 값이 들어오면 안됩니다.");
    }
}
```

위는 우테코 미션을 하다가 작성한 코드인데, 리뷰를 받던 도중에 String.matches() 메서드를 사용하는 것에 대해 문제가 있었다.

Pattern 인스턴스는 생성 비용이 높은데, 한번 쓰고 버려지기 때문에 굉장히 비효율적이다.

```agsl
private static final Pattern NUMBER = Pattern.compile(NUMBER_REG_EXP);

public static void validateNonNumber(String input) {
    Matcher matcher = NUMBER.matcher(input);
    if (!matcher.find()) {
        throw new IllegalArgumentException(ErrorMessage.INVALID_NUMBER_FORMAT.getErrorMessage());
    }
}
```

그래서 크리스마스 미션을 진행하면서 바꿨던 방식은, 불변 (final) Pattern 인스턴스를 직접 생성해 캐싱해두고,
validateNonNumber 메서드가 호출될  때 마다 재사용을 함으로써 성능을 효율적으로 끌어올릴 수 있다.

## long vs Long?
````agsl
private static long sum() {
    Long sum = 0L;
    for(long i = 0; i <= Integer.MAX_VALUE; i++){
        sum += i;
    }
    return sum;
}
````

위에서 Long으로 sum 변수를 선언한 탓에 실행 결과가 굉장히 느려지고, long으로 선언했을 때는 시간이 적게 걸렸다. 
6.3초 vs 0.59초

Long과 long에 대해서 명확한 차이를 잘 몰라서 찾아보게 되었다.

### Long
* 원시값인 long의 래퍼 클래스이기 때문에 추가적인 기능을 사용 가능
* null 값을 허용

마찬가지로 int <-> Integer 관계도 Integer가 원시값 int를 래핑하고 있는 상태이기 때문에 null 값이 존재하는지에 따라서 사용을 해주면 될 것 같다.

