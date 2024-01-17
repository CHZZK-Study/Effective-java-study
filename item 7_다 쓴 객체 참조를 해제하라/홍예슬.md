# 7. 다 쓴 객체 참조를 해제하라

자바에서는 가비지 컬렉터가 있어 메모리 관리에 더 이상 신경 쓰지 않아도 된다고 오해할 수 있는데, 절대 아니다.

스택을 간단히 구현한 다음 코드를 보자.

``` java
public class Stack {
    private Integer[] elements;
    private int size = 0;
    private static final int INITIAL_CAPACITY = 10;

    public Stack() {
        elements = new Integer[INITIAL_CAPACITY];
    }

    public void push(Integer e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public Integer pop() {
        if (size == 0)
            throw new EmptyStackException();
        return elements[--size];
    }

    private void ensureCapacity() {
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }
}
```

해당 코드는 특별한 문제는 없다.
하지만 스택에 객체를 저장하고 꺼내면 그 꺼내진 객체의 참조(다 쓴 참조)들은 스택이 여전히 가지고 있기 때문에 메모리 누수가 일어난다.

해법은 간단한다. 해당 참조를 다 썼을 때 ```null``` 처리(참조 해제)만 하면 된다.

``` java
public Integer pop() {
    if (size == 0)
        throw new EmptyStackException();
    Integer result = elements[--size];
    elements[size] = null;
    return result;
}
```

그렇다고해서 모든 경우에 대해 ```null``` 처리 하는 것은 바람직하지 않고, ```Stack```과 같이 직접 자신의 메모리를 관리하는 경우에는 메모리 누수에 주의하도록 하자.

캐시 역시 메모리 누수를 일으키는 주범이다.</p> 객체 참조를 캐시에 넣고 나서, 이 사실을 까맣게 잊은 채 한참을 그냥 놔두는 일을 자주 접할 수 있다.

만약 키를 참조하는 동안만 엔트리가 살아 있는 캐시가 필요한 상황이라면 ```WeakHashMap```을 사용해 캐시를 만들자. 다 쓴 엔트리는 그 즉시 자동으로 제거될 것이다.(단, ```WeakHashMap```은 이러한 상황에서만 유용하다.)

리스너(Listener) 혹은 콜백(Callback)도 메모리 누수의 주범이다. 클라이언트가 콜백을 등록만 하고 명확히 해지하지 않는다면, 콜백은 계속 쌓여갈 것이다. 이때 콜백을 약한 참조(weak reference)로 저장하면 가비지 컬렉터가 수거해간다. 예를 들어 ```WeakHashMap```에 키로 저장하면 되는 것이다.