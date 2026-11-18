---
title: "[Programmers] K번째수"
date: 2026-11-19 06:40:00 +0900
categories: [Algorithm, Programmers]
tags: [sorting, array, stream, java, 직접 해결]
---

문제 링크: [프로그래머스 코딩테스트 연습 - K번째수](https://school.programmers.co.kr/learn/courses/30/lessons/42748)

## 문제 해석

배열 `array`와 명령 배열 `commands`가 주어진다. 각 명령 `[i, j, k]`마다 `array`의 **i번째부터 j번째까지** 잘라 정렬한 뒤, **k번째 수**를 구해 결과 배열로 반환하는 문제다.

- `array` 길이는 최대 100, 원소는 1 이상 100 이하다.
- `commands` 길이는 최대 50이다.
- `i`, `j`, `k`는 모두 **1부터 세는 위치**다. Java 배열 인덱스는 0부터 시작하므로 변환이 필요하다.

입력이 작아서 명령마다 잘라서 정렬해도 충분하다. 이 문제의 핵심은 성능보다 **1부터 세는 구간을 0부터 세는 인덱스로 정확히 바꾸는 것**과 **원본 배열을 건드리지 않는 것**이다.

## Review

### 해결 여부

스스로 해결한 문제다.

### 내 풀이 점검

```java
import java.util.*;

class Solution {
    public int[] solution(int[] array, int[][] commands) {
        int[] answer = new int[commands.length];
        
        int idx = 0;
        for (int[] command : commands) {
            int[] copy = Arrays.copyOfRange(array, command[0] - 1, command[1]);
            Arrays.sort(copy);
            answer[idx++] = copy[command[2] - 1];
        }
        
        return answer;
    }
}
```

맞게 처리한 부분은 다음과 같다.

- **구간 변환이 정확하다.** 1부터 세는 `[i, j]`는 0부터 세는 인덱스로 `i - 1`부터 `j - 1`까지다. `copyOfRange`는 끝 인덱스를 포함하지 않으므로 `(i - 1, j)`를 넘기면 정확히 그 구간이 복사된다.
- **복사본을 정렬한다.** `array`를 직접 정렬하면 다음 명령에서 원래 순서가 사라져 틀린 답이 나온다. 매번 복사본을 만들어 정렬하므로 원본은 그대로다.
- **k번째도 0부터 세는 인덱스로 바꿨다.** `copy[command[2] - 1]`이다.

## Arrays.copyOfRange

`Arrays.copyOfRange(original, from, to)`는 `original`의 `from`부터 `to - 1`까지를 새 배열로 복사한다. **시작은 포함하고 끝은 포함하지 않는다.**

| 인자 | 의미 |
| --- | --- |
| `from` | 복사를 시작할 인덱스, 포함 |
| `to` | 복사를 끝낼 인덱스, **포함하지 않음** |
| 반환 | 길이가 `to - from`인 새 배열 |

```java
int[] array = {1, 5, 2, 6, 3, 7, 4};
Arrays.copyOfRange(array, 1, 5);  // [5, 2, 6, 3], 인덱스 1~4
```

> 1부터 세는 `i번째부터 j번째까지`는 `copyOfRange(array, i - 1, j)`가 된다. 시작은 0 기준으로 바꾸느라 1을 빼고, 끝은 "포함하지 않음" 규칙과 상쇄되어 `j` 그대로다. `substring(begin, end)`, `List.subList(from, to)`, `Arrays.stream(array, from, to)`도 같은 규칙이다.
{: .prompt-tip }

- `to`가 `original.length`보다 커도 예외가 나지 않는다. 넘치는 부분은 `0`으로 채워진다.
- `from`이 `to`보다 크면 `IllegalArgumentException`, `from`이 범위를 벗어나면 `ArrayIndexOutOfBoundsException`이 난다.

## 풀이 1 — 복사 후 정렬

### 풀이 방법

1. 명령마다 `copyOfRange`로 `i - 1`부터 `j - 1`까지 복사한다.
2. 복사본을 `Arrays.sort`로 정렬한다.
3. 정렬된 복사본의 `k - 1` 인덱스 값을 결과에 담는다.

### Java 코드

위의 내 풀이와 같다.

### 동작 예시

`array = [1, 5, 2, 6, 3, 7, 4]`, `commands = [[2, 5, 3], [4, 4, 1], [1, 7, 3]]`이다.

| 명령 | 복사 구간 | 자른 배열 | 정렬 후 | k번째 |
| --- | --- | --- | --- | --- |
| `[2, 5, 3]` | `copyOfRange(array, 1, 5)` | `[5, 2, 6, 3]` | `[2, 3, 5, 6]` | 5 |
| `[4, 4, 1]` | `copyOfRange(array, 3, 4)` | `[6]` | `[6]` | 6 |
| `[1, 7, 3]` | `copyOfRange(array, 0, 7)` | `[1, 5, 2, 6, 3, 7, 4]` | `[1, 2, 3, 4, 5, 6, 7]` | 3 |

결과는 `[5, 6, 3]`이고, 실행 후에도 `array`는 `[1, 5, 2, 6, 3, 7, 4]` 그대로다.

### 시간 및 공간 복잡도

배열 길이를 `N`, 명령 개수를 `C`라고 한다.

- 시간 복잡도: **`O(C · N log N)`** — 명령마다 최대 `N`개를 복사하고 정렬한다. `N ≤ 100`, `C ≤ 50`이라 매우 작다.
- 추가 공간 복잡도: **`O(N)`** — 명령마다 최대 `N` 길이의 복사본을 만든다. 결과 배열은 제외한다.

## 풀이 2 — 스트림

### 풀이 방법

`Arrays.stream(array, from, to)`는 배열의 일부 구간으로 `IntStream`을 만든다. 끝 인덱스를 포함하지 않는 규칙이 `copyOfRange`와 같으므로 같은 인자를 그대로 쓴다.

### Java 코드

```java
import java.util.Arrays;

class Solution {
    public int[] solution(int[] array, int[][] commands) {
        return Arrays.stream(commands)
                .mapToInt(c -> Arrays.stream(array, c[0] - 1, c[1])
                        .sorted()
                        .toArray()[c[2] - 1])
                .toArray();
    }
}
```

- `Arrays.stream(array, c[0] - 1, c[1])`: 구간을 스트림으로 만든다. 원본 배열은 바뀌지 않는다.
- `sorted().toArray()`: 정렬된 새 배열을 만든다. 풀이 1의 복사와 정렬을 한 번에 한다.
- 인덱스 관리용 `idx` 변수가 필요 없다.

`.toArray()[c[2] - 1]` 대신 `.skip(c[2] - 1).findFirst().getAsInt()`로 써도 된다. 정렬된 배열을 만들지 않아도 되지만, `sorted()`가 내부에서 전체를 정렬하는 것은 같으므로 복잡도는 달라지지 않는다.
{: .prompt-info }

### 시간 및 공간 복잡도

- 시간 복잡도: **`O(C · N log N)`**
- 추가 공간 복잡도: **`O(N)`**

## 풀이 비교

| 풀이 | 시간 | 추가 공간 | 특징 |
| --- | --- | --- | --- |
| 1. 복사 후 정렬 | `O(C · N log N)` | `O(N)` | 단계가 명확하고 디버깅이 쉽다 |
| 2. 스트림 | `O(C · N log N)` | `O(N)` | 한 식으로 끝나지만 스트림 객체 생성 비용이 있다 |

입력이 작아 두 풀이 모두 충분히 빠르다. 읽기 편한 쪽을 고르면 된다.

## 다시 풀 때 생각할 점

- 문제의 "i번째"가 1부터 세는지 0부터 세는지 먼저 확인한다.
- 1부터 세는 `[i, j]` 구간은 `copyOfRange(array, i - 1, j)`다. 끝을 포함하지 않는 API는 끝 인덱스에서 1을 빼지 않는다.
- 여러 명령이 같은 원본을 쓰면 원본을 정렬하지 말고 복사본을 정렬한다.
- `Arrays.stream(array, from, to)`로 구간 스트림을 바로 만들 수 있다.
