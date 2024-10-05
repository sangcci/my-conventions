# 😊 @Value 사용 시 생성자 주입 방식을 고려하자.

### `@Value` 어노테이션의 주입 시점

> Note that actual processing of the @Value annotation is performed by a **BeanPostProcessor** which in turn means that you cannot use @Value within **BeanPostProcessor** or **BeanFactoryPostProcessor** types. Please consult the javadoc for the **AutowiredAnnotationBeanPostProcessor** class (which, by default, checks for the presence of this annotation).

`Bean`의 생성 시점이 아닌, `BeanPostProcessor`에서 초기화 된다. 그래서 `Bean`이 생성된 직후에 초기화 되므로 서로 생성 시점이 다르다.

메서드로 참조하는 것은 괜찮지만, 필드에 생성자 시점에 초기화되는 변수일 경우 조심해서 사용하자.

`JwtProvider`만들 때 예시를 들어보자.

다음과 같이 `String` 값이 아닌 `SecretKey`를 직접 만들려고 한다면, 생성자 주입을 통해 생성자 내에서 직접 객체를 만들자.
필드로 주입하려는 경우, 생성자 주입보다 더 늦게 초기화되기 때문에 `null`값이 주입되어 `SecretKey`가 제대로 생성되지 못한다. 

```java
@Service
public class JwtProvider {

    private final long accessTokenExpirationDate;

    private final SecretKey secretKey;

    public JwtProvider(
            @Value("${jwt.secret-key}") String secretKey,
            @Value("${jwt.access.expiration}") long accessTokenExpirationDate
    ) {
        this.secretKey = Keys.hmacShaKeyFor(Decoders.BASE64URL.decode(secretKey));
        this.accessTokenExpirationDate = accessTokenExpirationDate;
    }
```
