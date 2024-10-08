# 서비스 코드 Null 처리

서비스 로직 내에 `repository`에서 `entity`를 가져올 때, `Optional`로 감싸서 `orElseThrow()`를 기계적으로 작성한 순간들이 많았다.



# NullPointException

자바는 객체의 주소값이 `null`인 상태를 가질 수 있다. 아직 객체가 할당되기 전을 의미한다.

```java
public static void main(String[] args) throws IOException {
    Apple apple = null;
    System.out.println(apple.a);
  }

  static class Apple {
    int a;
    int b;

    public Apple(int a, int b) {
      this.a = a;
      this.b = b;
    }
  }
}
```

단순히 null값을 가진 객체 주소값 `apple`을 참조하는 것은 상관없다.
하지만 `apple`의 필드 값인 `a`, `b`를 참조할 때 `NullPointException`이 발생한다. 

이처럼 자바는 `null`참조에 취약한 부분들을 가지고 있어 개발자의 실수가 잦게 발생한다.

그래서 `Optional`이 등장하였고, 

# 서비스 코드 처리

그러면 언제 처리해야 할까?

repository에서 가져오는 값은 `null`이 발생할 수 있다.

- 무조건 1개 이상을 반환받는 것을 보장하도록 구현
- `Opational`로 감싼 후 서비스에서 null 처리

`null`처리는 `NPE`발생의 방지를 위함이기 때문에 서비스 로직에서 이를 처리하는 것이 가장 현명하다는 생각.
