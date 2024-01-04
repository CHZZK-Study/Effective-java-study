# 2. 생성자에 매개변수가 많다면 빌더를 고려하라.

정적 팩터리와 생성자는 선택적 매개변수가 많을 때 적절히 대응하기 어렵다.

여기서 __선택적__ 매개변수란 여러가지 매개변수를 메서드에 전달할 수 있지만, default 값이 존재하여 선택적으로 객체를 만들 수 있는 경우를 말한다.

유저의 정보를 저장한다고 가정하자. 유저에는 <이름, 나이, 전화번호, 성별, 주소>라는 5가지 필드가 들어갈 수 있다. 하지만 이름을 제외한 다른 정보들은 선택적으로 취할 수 있다고 했을 때 생성자 혹은 정적 팩터리의 모습은 필수 매개변수(이름)만 받는 생성자, 필수 매개변수와 선택 매개변수 1개를 받는 생성자, 선택 매개변수 2개를 받는 생성자, ... 등의 형태로 많은 양의 코드를 만들어야 한다.

선택 매개변수가 많을 때 활용할 수 있는 두 번째 대안으로 __자바빈즈 패턴__ 이 있다.
매개변수가 없는 생성자로 객체를 만든 후, 세터(setter) 메서드들을 호출해 원하는 매개변수의 값을 설정하는 방식이다.

``` java
public class User {
    // 매개변수들은 (기본값이 있다면) 기본값으로 초기화된다.
    private String name;
    private int age = 0;
    private String phoneNumber = "010-0000-0000";
    private int gender = 0;
    private String address = "";

    public User() {}
    // Setter Methods
    public void setName(int val) { name = val; }
    public void setAge(int val) { age = val; }
    public void setPhoneNumber(String val) {
        phoneNumber = val;
    }
    public void setGender(int val) { gender = val; }
    public void setAddress(int val) { address = val; }
}
```

인스턴스를 만들기 쉽고, 그 결과 더 읽기 쉬운 코드가 되었다.

하지만 자바빈즈는 심각한 단점을 지니고 있다. 
사용 방법을 보자.
``` java
User user = new User();
user.setName("홍길동");
user.setAge(25);
user.setPhoneNumber("010-1234-1234");
user.setGender(1);
user.setAddress("주소");
```
자바빈즈 패턴에서는 객체 하나를 만들려면 메서드를 여러 개 호출해야 하고, 객체가 완전히 생성되기 전까지는 일관성이 깨진 상태로 존재한다.
때문에 크랠스를 불변으로 만들 수 없으며 안전성을 얻으려면 추가 작업이 필요하다.

이 때, 세 번째 사용할 수 있는 대안이 __빌더 패턴__ 이다.
어떻게 사용하는지 코드로 살펴보자.

``` java
public class User {
    // 매개변수들은 (기본값이 있다면) 기본값으로 초기화된다.
    private final String name;
    private final int age;
    private final String phoneNumber;
    private final int gender;
    private final String address;

    public static class Builder{
        // 필수 매개변수
        private final String name;

        // 선택 매개변수 - 기본값 초기화
        private int age = 0;
        private String phoneNumber = "010-0000-0000";
        private int gender = 0;
        private String address = "";

        public Builder(String name) {
            this.name = name;
        }

        public Builder age(int val) {
            age = val;
            return this;
        }
        public Builder phoneNumber(String val) {
            phoneNumber = val;
            return this;
        }
        public Builder gender(int val) {
            gender = val;
            return this;
        }
        public Builder address(int val) {
            address = val;
            return this;
        }

        public User build() {
            return new User(this);
        }
    }
    
    private User(Builder builder) {
        name    = builder.name;
        age     = builder.age;
        phoneNumber = builder.phoneNumber;
        gender  = builder.gender;
        address = builder.address;
    }
} 
```

빌더의 setter 메서드들은 빌더 자신을 반환하기 때문에 연쇄적으로 호출된다.
이를 사용할 때는 다음과 같은 코드로 사용하면 된다.
```java
User user = new User.Builder("홍길동")
                    .age(25).gender(1).build();
```

빌더 패턴은 사용도 쉽고, 읽기도 쉽다. 참고로 해당 패턴은 파이썬과 스칼라에 있는 명명된 선택적 매개변수(named optional parameters)를 흉내 낸 것이다.

특히 빌더 패턴은 계층적으로 설계된 클래스와 함께 쓰기에 좋다.
피자라는 기본 클래스가 존재하고 일반적인 '치즈 피자'와 소스를 선택 가능한 '칼초네 피자'라는 클래스를 만들 때 하위 클래스에서 재정의하여 쉽게 이용할 수 있게 된다.

물론 빌더 패턴은 성능이 민감할 경우 빌더 생성 비용 때문에 매개변수가 4개 이상이었을 때 사용하는 것을 권장한다. 하지만 대개 API는 시간이 지날수록 매개변수가 많아지지 않는가. 그러므로 빌더 패턴을 고려하여 설계하는 것이 좋다.