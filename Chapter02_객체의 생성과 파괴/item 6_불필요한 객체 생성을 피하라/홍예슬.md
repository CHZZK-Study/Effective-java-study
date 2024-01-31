# 6. 불필요한 객체 생성을 피하라

``` java
1. String s = new String("Yeseul");
2. String s = "Yeseul";
```

두 개의 문장은 같은 일을 하지만, 완전히 다른 문장이다.

1번 문장은 실행될 때마다 String 인스턴스를 새로 만든다. 이 문장이 반복문 안에 있다면 쓸데없는 String 인스턴스가 수백만개 만들어질 수도 있다.

2번 문장은 새로운 인스턴스를 매번 만드는 대신 하나의 String 인스턴스를 사용하여 모든 코드가 같은 객체를 재사용함이 보장된다.

만약 생성 비용이 아주 비싼 객체일 경우 1번 문장과 같은 방법으로 사용 해버리면 자원 낭비가 심할 것이다.
이럴 때, 캐싱하여 재사용하길 권한다.

비싼 객체의 대표적인 예로, 정규표현식을 사용하는 경우가 있다.

``` java
static boolean isDateNumeral(String input) {
    /**
     * 숫자에 대한 범위를 나타내는 정규표현식
     * 결과: 1~39까지의 정수(true)
     */
    return input.matches("([1-3]\\d)|(^[1-9])");
}
```

이 방식의 문제는 ```String.matches``` 메서드를 사용하는 것이다. 이 메서드 내부에서 사용하는 정규표현식용 ```Patterns``` 인스턴스는 인스턴스 생성 비용이 높다.

때문에 성능 개선을 위해 (불변인) ```Pattern``` 인스턴스를 클래스 초기화 과정에서 직접 생성해 캐싱해둔 뒤, 사용하는 메서드가 호출될 때마다 이 인스턴스를 재사용해야한다.

``` java
public class Date {
    private static final String REGEX_DATE_NUMERIC
                        = Pattern.complie("([1-3]\\d)|(^[1-9])");
    
    static boolean isDateNumeral(String input) {
        return REGEX_DATE_NUMERIC.matches(input).matches();
    }
}
```

이렇게 개선하면 ```isDateNumeral```이 빈번히 호출되는 상황에서 성능을 상당히 개선할 수 있다.

불필요한 객체를 만들어내는 다른 예로 오토 박싱(auto boxing)을 들 수 있다.

``` java
private static long sum() {
    Long sum = 0L;
    for (long i = 0; i < Integer.MAX_VALUE; i++)
        sum += i;
    
    return sum;
}
```

해당 코드는 모든 양의 정수 총합을 구하는 메서드로 ```long```을 사용하여 반환한다.

이 프로그램은 정확한 답을 내긴 한다. 그러나 ```sum``` 변수를 ```long```이 아닌 ```Long```으로 선언하여 ```long``` 타입인 ```i```가 ```Long``` 타입인 ```sum```에 더해질 때마다 새로운 인스턴스를 만들어버려 자원 낭비를 하고 있다.

그러므로 박싱된 기본 타입보다는 기본타입을 사용하고, 의도치 않은 오토박싱을 사용하지 않도록 주의하자.

> '객체 생성은 비싸니 피해야 한다'로 오해하면 안 된다.

일반적으로 자체 객체 풀은 코드를 헷갈리게 만들고 메모리 사용량을 늘리며 성능을 떨어뜨린다.
요즘 JVM의 가비지 컬렉터는 상당히 잘 최적화되어서 가벼운 객체를 다룰 때는 직접 만든 객체 풀보다 훨씬 빠를 수 있으므로 잘 고민해서 사용해보자.