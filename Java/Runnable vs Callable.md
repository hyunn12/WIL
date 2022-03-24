# Runnable vs Callable


자바의 함수형 인터페이스인 Runnable, Callable

둘 다 구현된 함수를 실행한다는 점은 같지만

`Runnable`: return XX, Exception XX

`Callable`: 특정 타입 객체 return, Exception 발생

## Runnable

```java
package java.lang;

@FunctionalInterface
public interface Runnable {
    public abstract void run();
}
```

Runnable 인터페이스를 구현하는 객체가 thread를 생성하는데에 사용되면

해당 객체의 run method가 별개의 thread에서 호출됨

return XX (void)

Exception XX

### 구현예제

```java
public class RunnableExample {
    public static void main(String[] args) {
        Thread thread = new Thread(
            () -> System.out.println("Runnable at " + LocalTime.now()));
        thread.start();
    }
}
```

람다식으로 구현한 Runnable 예제

## Callabale

```java
package java.util.concurrent;

@FunctionalInterface
public interface Callable<V> {
    V call() throws Exception;
}
```

<V>로 들어오는 type에 대해 결과물을 return함

Exception 처리할 수  있음

### 구현예제

```java
public class CallableExample {

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService executor = Executors.newSingleThreadExecutor();
        Future<String> future = executor.submit(() -> "Called at " + LocalTime.now());

        System.out.println("result : " + future.get());
    }
}
```

Callable이 일종의 Runnable의 확장개념이라고 생각하면 됨




---

**참고**
[https://codechacha.com/ko/java-callable-vs-runnable/](https://codechacha.com/ko/java-callable-vs-runnable/)
