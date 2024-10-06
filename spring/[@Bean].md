# 메서드 파라미터를 통해 의존성을 주입하자.

2가지 방법 존재
- 직접 메서드 호출
- 메서드 파라미터 의존성 주입

```java
@Bean
public ABean aBean() {
    return new ABean(bBean());
}

@Bean
public BBean bBean() {
    return new BBean();
}
```

```java
@Bean
public ABean aBean(BBean bBean) {
    return new ABean(bBean);
}

@Bean
public BBean bBean() {
    return new BBean();
}
```

실제로 `@Configuration`의 `@Bean` 메서드들은 등록될 때 

`BeanMethodInterceptor` 메서드가 intercept 하고, beanFactory에 존재하는 빈을 리턴한다.

여기서 `useArgs`에 따라 메서드 파라미터가 존재하는지 판단 후, 파라미터가 존재한다면 해당 Bean을 주입해준다.


```java
private static class BeanMethodInterceptor implements MethodInterceptor, ConditionalCallback {
		@Override
		@Nullable
		public Object intercept(Object enhancedConfigInstance, Method beanMethod, Object[] beanMethodArgs,
					MethodProxy cglibMethodProxy) throws Throwable {

        ConfigurableBeanFactory beanFactory = getBeanFactory(enhancedConfigInstance);
			String beanName = BeanAnnotationHelper.determineBeanNameFor(beanMethod);

			// Determine whether this bean is a scoped-proxy
			if (BeanAnnotationHelper.isScopedProxy(beanMethod)) {
				String scopedBeanName = ScopedProxyCreator.getTargetBeanName(beanName);
				if (beanFactory.isCurrentlyInCreation(scopedBeanName)) {
					beanName = scopedBeanName;
				}
			}
        ....
	       return resolveBeanReference(beanMethod, beanMethodArgs, beanFactory, beanName);
        }

		private Object resolveBeanReference(Method beanMethod, Object[] beanMethodArgs,
				ConfigurableBeanFactory beanFactory, String beanName) {
         Object beanInstance = (useArgs ? beanFactory.getBean(beanName, beanMethodArgs) :
						beanFactory.getBean(beanName));
       
       }   
}
```

그래서 이것은 성능 차이에 영향을 미치진 않고, 둘 다 똑같이 작동하기 때문에
자신의 코드 스타일에 맞게 작성하는 것이 좋다고 생각한다.
