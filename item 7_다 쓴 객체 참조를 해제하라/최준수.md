### 메모리 누수가 발생하는 위치?

```agsl
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_CAPACITY = 16;
    public Stack() {
        elements = new Object[DEFAULT_CAPACITY];
    }
    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }
    public Object pop() {
        if (size == 0) throw new EmptyStackException();
        return elements[--size];
    }
    private void ensureCapacity() { // 원소들이 들어갈 공간 확보
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }
}
```

언뜻 보면 특별한 문제가 없는 것 같지만, 이 코드에서는 `메모리 누수` 문제가 존재한다. pop 메서드에서 Object 객체들이 꺼내졌다고 하더라도 
단순히 [--size]를 통해서 인덱스만 감소시켜 줬을 뿐 실제 객체의 참조값은 스택이 가지고 있다. 

이렇게 스택이 다 쓴 참조 (obsolete reference)를 가지고 있기 때문에 해당 참조값들은 가비지 컬렉터가 회수 처리를 못하므로, 잠재적으로 성능에
큰 영향을 줄 수 있다.

### 해결 방법
```agsl
public Object pop() {
    if (size == 0) throw new EmptyStackException();
    Object popObject = elements[size];
    elements[size--] = null; // 참조 해제
    return popObject;
}
```

해결방법은, 다 쓴 참조를 `null` 로 변경을 해주어서, 더 이상 스택이 다 쓴 참조를 가지고 있지 않기 때문에 가비지 컬렉터가 회수 가능하다.

#### 하지만, null 체크는 예외적인 상황에만 처리해줘야 한다. 그런 케이스가 언제일까?

-> 자기 메모리를 직접 관리하는 클래스 일 때

* Stack 클래스
* 캐시
* 리스너 또는 콜백 