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

스스로 해결하지 못한 문제다. 나중에 풀이를 보지 않고 다시 풀어보기 위해 `다시 풀기`로 분류한다.

| 시도 | 결과 | 원인 |
| --- | --- | --- |
| 1차 | 해결하지 못함 | 큐로 푸는 흐름은 세웠지만 Java 큐의 메서드 사용법을 몰라 구현하지 못했다. |
| 2차 | 해결하지 못함 | 풀이를 본 뒤 `Deque`로 다시 작성했지만 `peek()`으로 맨 앞과 비교했고, 반복 조건에 줄어드는 `dq.size()`를 썼다. 아래 2차 시도 점검 단원에서 다룬다. |

### 1차 시도 점검

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

## Queue와 Deque 메서드 비교

**`Deque`는 `Queue`를 상속한 인터페이스다.** `Queue`의 메서드를 모두 가지고 있고, 양쪽 끝을 다루는 메서드가 더해진 구조다.

> `Deque`는 Double-Ended Queue의 줄임말로 "덱"이라고 읽는다. 양쪽 끝에서 모두 넣고 꺼낼 수 있는 큐다. 철자가 비슷한 **dequeue**는 자료구조 이름이 아니라 큐에서 원소를 꺼내는 동작을 말하며, Java에서는 `poll()`이 그 동작이다.
{: .prompt-tip }

```java
public interface Queue<E> extends Collection<E> { ... }
public interface Deque<E> extends Queue<E> { ... }
```

`Queue`는 한쪽으로만 들어가고 반대쪽으로만 나오므로 메서드 이름에 방향이 없다. `Deque`는 양쪽을 모두 다루므로 메서드 이름에 `First`, `Last`가 붙는다.

```text
Queue   나감 <- [앞 ... 뒤] <- 들어옴

Deque   <-> [앞 ... 뒤] <->
            First    Last
```

### 공통점: Queue의 메서드는 Deque에도 있다

아래 메서드는 두 인터페이스 모두에서 쓸 수 있다. `isEmpty`와 `size`는 그 위의 `Collection`에서 물려받은 것이다.

| 메서드 | Queue에서의 의미 | Deque에서 같은 동작 |
| --- | --- | --- |
| `offer(x)` | 뒤에 넣기 | `offerLast(x)` |
| `poll()` | 앞에서 꺼내기 | `pollFirst()` |
| `peek()` | 앞을 보기 | `peekFirst()` |
| `isEmpty()` | 비었는지 확인 | 같음 |
| `size()` | 원소 개수 | 같음 |

`Deque`에서 `offer`와 `offerLast`는 이름만 다른 같은 메서드다. **`peek()`은 `Queue`에서도 `Deque`에서도 맨 앞을 본다.** `Deque`가 `Queue`의 `peek()`을 물려받았으므로 동작이 달라지지 않는다.

### 차이점: Deque에만 있는 메서드

| 동작 | 앞(First) | 뒤(Last) |
| --- | --- | --- |
| 넣기 | `offerFirst(x)` | `offerLast(x)` |
| 꺼내기 | `pollFirst()` | `pollLast()` |
| 보기 | `peekFirst()` | `peekLast()` |

- **Queue에도 있는 동작**: 뒤에 넣기, 앞에서 꺼내기, 앞을 보기. `offer`, `poll`, `peek`으로도 부를 수 있다.
- **Deque에만 있는 동작**: 앞에 넣기, 뒤에서 꺼내기, 뒤를 보기. `offerFirst`, `pollLast`, `peekLast`로만 부를 수 있다.

이 문제에서 필요한 "직전에 넣은 원소 보기"가 **뒤를 보기**다. `Queue`에는 뒤를 보는 메서드가 아예 없으므로 `Deque`를 쓴다.

```text
앞 [2, 5, 7] 뒤
 ↑        ↑
peek()    peekLast()  <- Deque에만 있음
```

### 쓸 수 있는 메서드는 변수 타입이 정한다

구현체 `ArrayDeque`는 두 인터페이스를 모두 구현하지만, 부를 수 있는 메서드는 변수의 선언 타입이 정한다.

```java
Queue<Integer> q = new ArrayDeque<>();
q.offer(1);     // 가능
q.peekLast();   // 컴파일 에러. Queue 타입에는 이 메서드가 없다

Deque<Integer> dq = new ArrayDeque<>();
dq.offer(1);    // 가능. Queue에서 물려받은 메서드
dq.peekLast();  // 가능. Deque에 추가된 메서드
```

### 풀이 2에서 offerLast, pollFirst를 쓴 이유

기능상 이유는 없고 방향을 코드에 드러내기 위해서다. `offer`, `poll`로 바꿔도 결과는 같다. 꼭 필요한 Deque 전용 메서드는 `peekLast()` 하나다.

- `peekLast()`가 이미 "뒤"를 명시하므로 `offerLast`와 함께 쓰면 "뒤에 넣고, 뒤를 본다"가 바로 보인다.
- `pollFirst`를 쓰면 "앞에서 꺼낸다"가 보여 순서가 유지된다는 점이 드러난다.

> `Deque`의 스택용 메서드 `push(x)`는 앞에 넣고(`offerFirst`와 같은 위치) `pop()`은 앞에서 꺼낸다(`pollFirst`와 같은 위치). `offer`와 `push`를 섞으면 순서가 꼬인다. 큐로 쓸 때는 `offer`와 `poll`, 스택으로 쓸 때는 `push`와 `pop`만 쓰거나, `First`와 `Last`를 붙인 이름으로 통일한다.
{: .prompt-warning }

## 2차 시도 점검

풀이를 본 뒤 `Deque`로 다시 작성한 코드다. `[2, 2, 5, 5, 7, 2, 2]`에 대해 정답 `[2, 5, 7, 2]` 대신 `[2, 5, 0, 0]`을 반환한다.

```java
import java.util.*;

public class Solution {
    public int[] solution(int []arr) {
        Deque<Integer> dq = new ArrayDeque<>();
        
        for (int a : arr) {
            if (dq.isEmpty() || dq.peek() != a) dq.offer(a);
        }
        
        int[] result = new int[dq.size()];
        for (int i = 0; i < dq.size(); i++) {
            result[i] = dq.poll();
        }
        
        return result;
    }
}
```

### peek()은 맨 앞을 본다

`Deque`의 `peek()`은 `peekFirst()`와 같다. 1차 시도와 같은 실수, 즉 **큐의 맨 앞과 비교**를 하고 있다. 직전에 넣은 원소를 보려면 `peekLast()`를 써야 한다. `offer`는 뒤에 넣는 메서드이므로 그대로 둬도 된다.

```java
if (dq.isEmpty() || dq.peekLast() != a) dq.offer(a);
```

### 반복 중에 dq.size()가 줄어든다

`poll()`은 원소를 꺼내면서 제거한다. 반복할 때마다 `i`는 커지고 `dq.size()`는 작아져서 절반쯤에서 반복이 끝난다. 원소가 4개일 때의 흐름은 다음과 같다.

| `i` | 조건 검사 시 `dq.size()` | `i < size` | 동작 |
| --- | --- | --- | --- |
| 0 | 4 | 참 | 꺼내서 `result[0]`에 저장 |
| 1 | 3 | 참 | 꺼내서 `result[1]`에 저장 |
| 2 | 2 | 거짓 | 반복 종료 |

`result[2]`, `result[3]`은 채워지지 않아 기본값 `0`으로 남는다. 반복 조건을 배열을 만들 때 고정되는 `result.length`로 바꾸면 된다. `while (!dq.isEmpty())`로 비우거나, 반복 전에 크기를 변수에 저장해도 된다.

> `poll()`, `pop()`처럼 원소를 제거하는 메서드를 반복 안에서 부르면 `size()`가 매번 줄어 반복이 일찍 끝난다. 큐나 스택을 비울 때는 `while (!q.isEmpty())`나 미리 고정한 크기를 쓴다.
{: .prompt-warning }

### 두 버그가 함께 있을 때

1. 앞과 비교해서 덱에 `[2, 5, 5, 7]`이 쌓인다. 연속된 `5`를 거르지 못했고 마지막 `2`는 빠졌다.
2. 반복문이 2번만 돌아서 `[2, 5, 0, 0]`이 반환된다.

### 고친 코드

```java
import java.util.*;

public class Solution {
    public int[] solution(int[] arr) {
        Deque<Integer> dq = new ArrayDeque<>();

        for (int a : arr) {
            if (dq.isEmpty() || dq.peekLast() != a) dq.offer(a);
        }

        int[] result = new int[dq.size()];
        for (int i = 0; i < result.length; i++) {
            result[i] = dq.poll();
        }

        return result;
    }
}
```

## 다시 풀 때 생각할 점

- 비교 대상은 큐의 앞이 아니라 직전에 넣은 원소다. 큐의 앞은 가장 먼저 들어간 원소라서 연속 여부를 판단할 수 없다.
- `Queue`는 앞만 볼 수 있다. 뒤를 봐야 하면 변수로 따로 들고 있거나 `Deque`의 `peekLast()`를 쓴다.
- 큐 기본 메서드는 `offer`(넣기), `peek`(보기), `poll`(꺼내기), `isEmpty`, `size`다.
- `peek()`은 `Queue`와 `Deque` 모두에서 맨 앞을 본다. 뒤를 보려면 `peekLast()`다.
- `Deque`는 `Queue`를 상속한다. 부를 수 있는 메서드는 변수의 선언 타입이 정한다.
- 반복하면서 꺼내는 컬렉션의 `size()`를 반복 조건에 쓰지 않는다. `result.length`나 `while (!q.isEmpty())`를 쓴다.
- 배열은 크기를 정해서 만든다. `new int[]`가 아니라 `new int[q.size()]`다.
