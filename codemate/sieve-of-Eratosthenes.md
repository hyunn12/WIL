# 에라토스테네스의 체

### 기본적인 소수 판별법
시간복잡도: O(n)

√N 값을 지나는 순간 몫과 나누는 값이 반대가 되는 약수의 중심 기법 이용

=> _**N이 합성수라면, √N보다 작은 약수는 무조건 존재한다._**

```java
public static boolean isPrime (int n) {
    for (int i = 2; i < Math.sqrt(n); i++) {
        if (n % i == 0) return false;
    }
    return true;
}
```

기존엔 n까지 반복문을 돌았지만 이젠 √N까지만 돌면 됨


### 에라토스테네스의 체
N 이하의 소수를 찾는 방법

N보다 작거나 같은 수 중 N의 제곱근보다 작은 소수의 배수들을 지우고 남은 수가 소수라는 것

```java
public class Eratosthenes {
    static int getPrimeList1(int n) {
        int sqrt = (int)(Math.sqrt(n)) + 1;
        int cnt = 0;

        boolean[] primeList = new boolean[range];
        
        // 배열 초기화
        for (int i = 0; i <= n; i++) primeList[i] = true;

        // 2부터 제곱근 범위까지 소수의 배수들을 제외
        for (int i = 2; i < sqrt; i++) {
            if (primeList[i]) {
                for (int j = i * i; j < range; j += i)
                    primeList[j] = false;
            }
        }

        for (int i = 2; i <= n; i++){
            if (primeList[i]) cnt ++;
        }
        return cnt;
    }
    
    static int getPrimeList2(int n) {
        int cnt = 0;

        int[] num = new int[n+1];

        // 2부터 n 까지의 수를 배열에 담음
        for (int i = 2; i <= n; i++) num[i] = i;

        // 2부터 시작해서 그 배수를 다 0으로 만듦
        for (int i = 2; i <= Math.sqrt(n); i++) {
            // 나중에 바뀐 수의 차례일때 해당 수가 0일 경우 넘어가도록 설정
            if (num[i] == 0) continue;

            for (int j = 2*i; j <= n; j += i) num[j] = 0;
        }

        // 배열에서 0이 아닌 것들 세기
        for (int i : num) if (i != 0) cnt++;

        return cnt;
    }
    
    public static void main(String[] args) {
        System.out.println("소수의 개수: " + getPrimeList1(50000));
        System.out.println("소수의 개수: " + getPrimeList2(50000));
    }
}
```
