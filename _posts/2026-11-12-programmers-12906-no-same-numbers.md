---
title: "[Programmers] 같은 숫자는 싫어"
date: 2026-11-12 22:00:00 +0900
categories: [Algorithm, Programmers]
tags: [stack-queue, queue, deque, java, 다시 풀기]
---

문제 링크: [프로그래머스 코딩테스트 연습 - 같은 숫자는 싫어](https://school.programmers.co.kr/learn/courses/30/lessons/12906)

## 문제 해석

0부터 9까지의 숫자로 이루어진 배열에서 **연속으로** 나오는 같은 숫자를 하나만 남기고, 원래 순서를 유지한 배열을 반환하는 문제다. 배열 크기는 최대 1,000,000이다.

중복 전체를 없애는 것이 아니라 연속 중복만 없앤다는 점이 핵심이다.

- `[2, 2, 5, 5, 7, 2, 2]`의 답은 `[2, 5, 7, 2]`다. 마지막 `2`는 앞에서 이미 나왔지만 바로 앞 원소가 `7`이므로 남는다. `Set`으로 중복을 없애면 틀린다.
- 각 원소는 **바로 앞 원소**와만 비교하면 된다. 배열을 한 번 순회하는 `O(N)` 풀이가 필요하다.

## Review

### 해결 여부

스스로 해결하지 못한 문제다. 큐로 푸는 흐름은 세웠지만 Java 큐의 메서드 사용법을 몰라 구현하지 못했다. 나중에 풀이를 보지 않고 다시 풀어보기 위해 `다시 풀기`로 분류한다.

### 내 풀이 점검

처음 세운 흐름은 다음과 같다.

```java
import java.util.*;

public class Solution {
    public int[] solution(int []arr) {
        // 큐 생성
        // arr 순회 a
        //   큐의 가장 앞부분 원소와 동일하지 않으면 push
        // 큐의 원소를 순서대로 int[]로 만들어 반환
        return new int[];
    }
}
```

큐에 넣고 순서대로 꺼내 배열로 만드는 흐름은 맞다. 막힌 지점은 두 가지다.

- **큐 메서드를 몰랐다.** 넣기, 보기, 꺼내기에 어떤 메서드를 쓰는지 기억나지 않았다.
- **비교 대상을 잘못 잡았다.** 비교해야 하는 것은 큐의 맨 앞이 아니라 **바로 직전에 넣은 원소**, 즉 큐의 맨 뒤다.
- 그 밖에 `new int[]`는 크기가 없어 컴파일되지 않는다. `new int[q.size()]`처럼 크기를 정해야 한다.

## 큐 기본 메서드

> 큐는 먼저 넣은 원소가 먼저 나오는 FIFO(First In, First Out) 자료구조다. 원소는 뒤로 들어가고 앞에서 나온다. Java에서 `Queue`는 인터페이스이므로 `new Queue<>()`로 만들 수 없고, `Queue<Integer> q = new LinkedList<>();`처럼 구현체로 생성한다.
{: .prompt-tip }

| 메서드 | 동작 | 비었을 때 |
| --- | --- | --- |
| `offer(x)` | 맨 뒤에 넣기 | - |
| `poll()` | 맨 앞을 꺼내고 제거 | `null` 반환 |
| `peek()` | 맨 앞을 보기만 함 | `null` 반환 |
| `isEmpty()` | 비었는지 확인 | - |
| `size()` | 원소 개수 | - |

`add`, `remove`, `element`도 같은 동작을 하지만 비었을 때 `null` 대신 예외를 던진다. 코딩테스트에서는 보통 `offer`, `poll`, `peek`을 쓴다.

### 큐의 앞과 뒤

큐의 **앞은 가장 먼저 들어간 원소**, 다음에 꺼낼 원소다. **뒤는 가장 최근에 넣은 원소**다.

```text
offer(2) -> offer(5) -> offer(7)

앞 [2, 5, 7] 뒤
 ↑        ↑
peek()    가장 최근에 넣은 원소
poll()
```

### 앞과 비교하면 틀리는 이유

`[2, 2, 5, 5, 7, 2, 2]`로 두 비교 방식을 따라가면 차이가 드러난다.

| 현재 원소 | 앞과 비교 | 뒤와 비교 |
| --- | --- | --- |
| 2 | 비어 있음, 넣음 `[2]` | 비어 있음, 넣음 `[2]` |
| 2 | 앞 2와 같음, 버림 | 뒤 2와 같음, 버림 |
| 5 | 앞 2와 다름, 넣음 `[2, 5]` | 뒤 2와 다름, 넣음 `[2, 5]` |
| 5 | 앞 2와 다름, 넣음 `[2, 5, 5]` ❌ | 뒤 5와 같음, 버림 |
| 7 | 넣음 `[2, 5, 5, 7]` | 넣음 `[2, 5, 7]` |
| 2 | 앞 2와 같음, 버림 ❌ | 뒤 7과 다름, 넣음 `[2, 5, 7, 2]` |
| 2 | 버림 | 뒤 2와 같음, 버림 |

앞과 비교하면 `[2, 5, 5, 7]`이 나온다. 연속 중복인 `5`를 거르지 못하고, 다시 나온 `2`는 빠뜨린다. 뒤와 비교해야 정답 `[2, 5, 7, 2]`가 나온다.

`Queue`의 `peek()`은 앞만 보여주므로 뒤를 볼 방법이 없다. 그래서 풀이 1은 직전 값을 변수에 따로 저장하고, 풀이 2는 뒤를 볼 수 있는 `Deque`를 쓴다.

## 풀이 1 — Queue + 직전 값 변수

### 풀이 방법

`Queue`에는 맨 뒤 원소를 보는 메서드가 없으므로 직전에 넣은 값을 `prev`에 저장해 비교한다.

1. 배열을 순회하며 현재 원소가 `prev`와 다르면 큐에 넣고 `prev`를 갱신한다.
2. 큐의 크기로 결과 배열을 만들고, 앞에서부터 꺼내 채운다.

### Java 코드

```java
import java.util.*;

public class Solution {
    public int[] solution(int[] arr) {
        Queue<Integer> q = new LinkedList<>();
        int prev = -1; // 원소는 0~9라서 -1은 절대 겹치지 않는다

        for (int a : arr) {
            if (a != prev) {
                q.offer(a);
                prev = a;
            }
        }

        int[] answer = new int[q.size()];
        int i = 0;
        while (!q.isEmpty()) {
            answer[i++] = q.poll();
        }
        return answer;
    }
}
```

`prev`의 초기값 `-1`은 원소 범위 밖의 값이라 첫 원소는 항상 들어간다. 큐를 배열로 바꿀 때는 `poll()`로 앞에서부터 꺼내므로 넣은 순서가 그대로 유지된다.

### 시간 및 공간 복잡도

- 시간 복잡도: **`O(N)`** — 배열을 한 번 순회하고, 큐의 `offer`와 `poll`은 `O(1)`이다.
- 추가 공간 복잡도: **`O(N)`** — 연속 중복이 없으면 모든 원소가 큐에 들어간다.

## 풀이 2 — Deque로 맨 뒤 원소 보기

### 풀이 방법

`Deque`는 양쪽 끝을 모두 다룰 수 있어서, 처음 생각한 "큐 안의 원소와 비교" 구조를 그대로 쓸 수 있다. `peekLast()`로 직전에 넣은 원소를 바로 확인한다.

### Java 코드

```java
import java.util.*;

public class Solution {
    public int[] solution(int[] arr) {
        Deque<Integer> dq = new ArrayDeque<>();

        for (int a : arr) {
            if (dq.isEmpty() || dq.peekLast() != a) {
                dq.offerLast(a);
            }
        }

        int[] answer = new int[dq.size()];
        int i = 0;
        while (!dq.isEmpty()) {
            answer[i++] = dq.pollFirst();
        }
        return answer;
    }
}
```

주의할 점이 두 가지 있다.

- 빈 덱에서 `peekLast()`는 `null`을 반환한다. 이 값을 `int`와 비교하면 언박싱 중 `NullPointerException`이 나므로 `isEmpty()`를 먼저 확인한다.
- `dq.peekLast() != a`는 한쪽이 `int`라서 값 비교가 된다. 둘 다 `Integer`라면 참조를 비교해 128 이상에서 틀리므로 `equals`를 써야 한다.

큐를 배열로 바꿀 때 `q.stream().mapToInt(Integer::intValue).toArray()`를 쓰면 `while` 루프 없이 `int[]`를 만들 수 있다.
{: .prompt-info }

### 시간 및 공간 복잡도

- 시간 복잡도: **`O(N)`**
- 추가 공간 복잡도: **`O(N)`**

## 다시 풀 때 생각할 점

- 비교 대상은 큐의 앞이 아니라 직전에 넣은 원소다. 큐의 앞은 가장 먼저 들어간 원소라서 연속 여부를 판단할 수 없다.
- `Queue`는 앞만 볼 수 있다. 뒤를 봐야 하면 변수로 따로 들고 있거나 `Deque`의 `peekLast()`를 쓴다.
- 큐 기본 메서드는 `offer`(넣기), `peek`(보기), `poll`(꺼내기), `isEmpty`, `size`다.
- 배열은 크기를 정해서 만든다. `new int[]`가 아니라 `new int[q.size()]`다.
