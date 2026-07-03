### 🔹 디버깅이란?

프로그램에서 발생하는 문법 오류나 논리 오류를 찾아 바로잡는 과정이다.

### 🛠 디버깅 방법

1. 디버깅 라인에 중단점(breakpoint) 설정
2. IDE 디버깅 기능 실행
3. 디버깅 중 변수나 수식 값을 확인하며 중간 테스트

### ⚖️ 로그(print) 기반 디버깅 vs IDE 디버거

| 구분 | 로그(print) 기반 디버깅 | IDE 디버거 |
| --- | --- | --- |
| 방식 | 코드 곳곳에 `print`/`println`을 추가해 값을 출력 | 중단점을 설정하고 실행을 멈춰가며 상태 확인 |
| 장점 | 별도 설정 없이 어디서나 빠르게 사용 가능 | 변수/호출 스택을 실시간으로 확인, 코드 수정 불필요 |
| 단점 | 확인할 지점마다 코드를 추가/삭제해야 함, 코드가 지저분해짐 | IDE·환경 설정이 필요, 초기 학습 비용 존재 |
| 적합한 상황 | 간단한 스크립트, 원격/운영 환경처럼 디버거 연결이 어려운 경우 | 로컬 개발 중 복잡한 로직·반복문 내부 상태를 추적할 때 |

> 💡 실무에서는 두 방식을 상황에 맞게 함께 사용한다. 빠르게 값만 확인할 때는 로그, 원인을 깊이 추적할 때는 IDE 디버거가 효율적이다.

### 📝 디버깅 사례

#### 예시 코드

```java
import java.util.Scanner;

public class debugging_error {
    public static void main (String[] args) {
        // TODO: 배열에서 주어진 범위의 합을 구하는 프로그램
        Scanner sc = new Scanner(System.in);
        int testcase = sc.nextInt();
        int answer = 0;
        int A[] = new int[100001];
        int S[] = new int[100001];

        for (int i = 1; i < 10000; i++) {
            A[i] = (int) (Math.random() * Integer.MAX_VALUE);
            S[i] = S[i - 1] + A[i];
        }

        for (int t = 1; t < testcase; t++) {
            int query = sc.nextInt();
            for (int i = 0; i < query; i++) {
                int start = sc.nextInt();
                int end = sc.nextInt();

                answer += S[end] - S[start - 1];
                System.out.println(testcase + " " + answer);
            }
        }
    }
}
```

#### 오류 유형별 디버깅 과정

| # | 오류 유형 | 의도 | 디버깅으로 발견한 원인 | 해결 방법 |
| --- | --- | --- | --- | --- |
| 1 | 변수 초기화 오류 | `answer`가 각 testcase마다 0으로 초기화되어야 함 | Variables 탭에서 `answer` 값이 계속 누적/증가함을 확인 | `answer` 초기화 위치를 for문 안으로 이동 |
| 2 | 반복문 인덱스 범위 지정 오류 | `A`, `S` 배열의 10만 번째 index까지 값을 채움 | index 10000부터 `A`, `S` 값이 전부 0(미초기화 상태)임을 확인 | for문 조건절의 값과 부등호를 수정 |
| 3 | 잘못된 변수 사용 오류 | 각 테스트 케이스의 순서를 정답과 함께 출력 | 터미널에 순서 값(`testcase`)이 매번 고정되어 출력됨을 확인 | `testcase` 대신 반복 변수 `t`를 사용 |
| 4 | 자료형 범위 오류 | `S` 배열을 양수 값으로만 채움 | Variables 탭에서 `S` 배열 원소 값이 음수(overflow)로 보임을 확인 | `int` → `long` 자료형으로 변경 |

> 💡 4번처럼 누적 합/곱을 다루는 숫자 자료형은 overflow 가능성을 고려해 처음부터 `long`으로 선언하는 것을 권장한다.

#### 🔧 Before / After 전체 코드 비교

**Before (오류 코드)**

```java
import java.util.Scanner;

public class debugging_error {
    public static void main (String[] args) {
        Scanner sc = new Scanner(System.in);
        int testcase = sc.nextInt();
        int answer = 0;
        int A[] = new int[100001];
        int S[] = new int[100001];

        for (int i = 1; i < 10000; i++) {
            A[i] = (int) (Math.random() * Integer.MAX_VALUE);
            S[i] = S[i - 1] + A[i];
        }

        for (int t = 1; t < testcase; t++) {
            int query = sc.nextInt();
            for (int i = 0; i < query; i++) {
                int start = sc.nextInt();
                int end = sc.nextInt();

                answer += S[end] - S[start - 1];
                System.out.println(testcase + " " + answer);
            }
        }
    }
}
```

**After (수정 코드)**

```java
import java.util.Scanner;

public class debugging_fixed {
    public static void main (String[] args) {
        Scanner sc = new Scanner(System.in);
        int testcase = sc.nextInt();
        int A[] = new int[100001];
        long S[] = new long[100001]; // 4. int -> long (overflow 방지)

        for (int i = 1; i <= 100000; i++) { // 2. i < 10000 -> i <= 100000
            A[i] = (int) (Math.random() * Integer.MAX_VALUE);
            S[i] = S[i - 1] + A[i];
        }

        for (int t = 1; t < testcase; t++) {
            long answer = 0; // 1. testcase마다 초기화되도록 for문 안으로 이동
            int query = sc.nextInt();
            for (int i = 0; i < query; i++) {
                int start = sc.nextInt();
                int end = sc.nextInt();

                answer += S[end] - S[start - 1];
                System.out.println(t + " " + answer); // 3. testcase -> t
            }
        }
    }
}
```

#### ✅ 요약

- 디버깅 = 중단점 설정 → 디버깅 실행 → 변수/수식 값 확인의 반복 과정
- 로그 기반 디버깅은 간편하지만 코드가 지저분해지고, IDE 디버거는 상태 추적에 강하지만 환경 설정이 필요하다 → 상황에 맞게 병행
- IDE의 Variables 탭으로 변수 값의 변화를 직접 확인하며 원인을 추적한다
- 자주 발생하는 오류 유형: 초기화 누락, 반복문 범위 오류, 변수 오용, 자료형 범위(overflow) 오류
