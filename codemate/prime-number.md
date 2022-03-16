# 소수
1보다 큰 자연수 중 1과 자기 자신만을 약수로 가지는 수

### 기본적인 소수 판별
1과 자기 자신만을 약수로 가짐 = 2부터 n-1까지의 수로 나뉘어서는 안됨

```java
public class PrimeNumber {
    public static boolean isPrime (int n) {
        for (int i = 2; i < n; i++) {
            if (n % i == 0) return false;
        }
        return true;
    }

    public static String printPrime (int n) {
        String s = isPrime(n) ? "은 소수입니다." : "은 소수가 아닙니다.";
        return n + s;
    }

    public static void main(String[] args) {
        System.out.println(printPrime(97));
        System.out.println(printPrime(100));
    }
}
```

### 소수찾기 예제
#### 입력
1000000 이하의 자연수 n
#### 출력
0부터 입력값 n까지의 수 중 모든 소수의 개수

```java
public class PrintPrimeByRange {
    public static boolean isPrime (int n) {
        for (int i = 2; i < n; i++) {
            if (n % i == 0) return false;
        }
        return true;
    }

    public static void printPrimeByRange (int n) {
        int cnt = 0;
        for (int i = 2; i <= n; i++) {
            if (isPrime(i)) cnt++;
        }
        System.out.println("소수의 개수: " + cnt);
    }

    public static void main(String[] args) {
        printPrimeByRange(50000);
    }
}
```
하지만 해당 코드는 시간복잡도가 O(n^2) 때문에 좋지않음
