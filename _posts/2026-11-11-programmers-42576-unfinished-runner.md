---
title: "[Programmers] 완주하지 못한 선수"
date: 2026-11-11 22:00:00 +0900
categories: [Algorithm, Programmers]
tags: [hash-table, sorting, stream, java, 다시 풀기]
---

문제 링크: [프로그래머스 코딩테스트 연습 - 완주하지 못한 선수](https://school.programmers.co.kr/learn/courses/30/lessons/42576)

## 문제 해석

참가자 이름 배열과 완주자 이름 배열을 비교해 완주하지 못한 한 명의 이름을 찾는 문제다. 완주자 배열은 참가자 배열보다 정확히 1 짧고, 참가자는 최대 100,000명이다.

핵심 조건은 **동명이인이 있을 수 있다**는 점이다.

- 동명이인 때문에 `Set`으로 이름의 존재 여부만 확인하면 풀 수 없다. 이름별 **개수**를 세어야 한다.
- 참가자 수가 많으므로 이름마다 완주자 배열을 선형 탐색하는 `O(N^2)` 풀이는 피한다.

## Review

### 해결 여부

스스로 해결하지 못한 문제다. 풀이 방법은 떠올렸지만 Java `HashMap`의 메서드가 기억나지 않아 구현하지 못했다. 나중에 풀이를 보지 않고 다시 풀어보기 위해 `다시 풀기`로 분류한다.

### 내 풀이 점검

참가자 배열을 `<이름, 개수>` 맵으로 바꾸고, 완주자 배열을 돌면서 개수를 줄인 뒤, 값이 남은 키를 반환하려고 했다. 방향은 맞았지만 맵에 값을 넣는 부분에서 멈췄다.

```java
HashMap<String, Integer> toMap = new HashMap<>();
for (participant : p) {
    if (toMap.)
}
```

메서드 외에도 고쳐야 할 부분이 있었다.

- `for (participant : p)`: 향상된 `for` 문은 `for (타입 변수 : 배열)` 순서다. `for (String p : participant)`로 쓴다.
- `HashMap` import 누락: `java.util.HashMap`을 import 해야 한다.
- 완주자 이름이 맵에 없으면 예외를 던지려 했지만, 완주자는 항상 참가자의 부분집합이므로 그런 이름은 들어오지 않는다. 검사 없이 개수만 줄이면 된다.

## 풀이 1 — HashMap 카운트

### 풀이 방법

1. 참가자 배열을 순회하며 이름별 등장 횟수를 센다.
2. 완주자 배열을 순회하며 해당 이름의 횟수를 1 줄인다.
3. 맵을 순회해 값이 0보다 큰 키를 반환한다.

완주하지 못한 선수는 정확히 한 명이므로 값이 남는 키는 반드시 하나다.

### Java 코드

```java
import java.util.*;

class Solution {
    public String solution(String[] participant, String[] completion) {
        Map<String, Integer> cnt = new HashMap<>();

        for (String p : participant) {
            cnt.put(p, cnt.getOrDefault(p, 0) + 1);
        }

        for (String c : completion) {
            cnt.put(c, cnt.get(c) - 1);
        }

        for (Map.Entry<String, Integer> e : cnt.entrySet()) {
            if (e.getValue() > 0) return e.getKey();
        }

        throw new IllegalArgumentException("Invalid Input");
    }
}
```

`getOrDefault(p, 0) + 1`로 처음 나온 이름과 동명이인을 한 줄로 처리한다. 값이 남는 키가 없는 경우는 문제 조건상 일어나지 않으므로, 빈 문자열을 반환하는 대신 예외로 잘못된 입력임을 드러냈다.

### 동작 예시

참가자가 `["kim", "lee", "kim", "choi"]`, 완주자가 `["choi", "kim", "lee"]`인 경우다.

| 단계 | `cnt` |
| --- | --- |
| 참가자 카운트 | `{kim=2, lee=1, choi=1}` |
| 완주자 차감 | `{kim=1, lee=0, choi=0}` |
| 값이 남은 키 | `kim` |

### 시간 및 공간 복잡도

- 시간 복잡도: **`O(N)`** — 배열 두 개와 맵을 한 번씩 순회하고, `HashMap` 조회와 저장은 평균 `O(1)`이다.
- 추가 공간 복잡도: **`O(N)`** — 서로 다른 이름의 개수만큼 맵에 저장한다.

## 풀이 2 — 스트림으로 완주자 카운트 차감

### 풀이 방법

반대로 **완주자**의 이름별 횟수를 먼저 만들고, 참가자를 돌면서 하나씩 뺀다. 횟수가 음수가 되는 첫 이름이 완주하지 못한 선수다.

- 완주자 명단에 없는 이름은 맵에 없으므로 `merge` 결과가 `-1`이 된다.
- 동명이인은 완주한 수보다 한 번 더 등장하는 순간 음수가 된다.

### Java 코드

```java
import java.util.Arrays;
import java.util.Map;
import java.util.function.Function;
import java.util.stream.Collectors;

class Solution {
    public String solution(String[] participant, String[] completion) {
        Map<String, Long> done = Arrays.stream(completion)
                .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));

        return Arrays.stream(participant)
                .filter(p -> done.merge(p, -1L, Long::sum) < 0)
                .findFirst()
                .orElseThrow();
    }
}
```

`groupingBy(Function.identity(), counting())`은 이름별 등장 횟수 맵을 만드는 관용구다. 값 타입이 `Integer`가 아니라 `Long`이라는 점에 주의한다.

풀이 1의 맵 순회가 없어지고 `findFirst`로 찾는 즉시 끝난다. 다만 `filter`의 조건식이 `merge`로 맵을 바꾸는 부수효과를 가진다. 코드는 짧지만 스트림 스타일에서는 지적받을 수 있는 형태이고, 병렬 스트림에서는 쓸 수 없다.

### 시간 및 공간 복잡도

- 시간 복잡도: **`O(N)`**
- 추가 공간 복잡도: **`O(N)`**

## 풀이 3 — 정렬 후 같은 인덱스 비교

### 풀이 방법

두 배열을 정렬하면 같은 인덱스의 이름이 일치해야 한다. 처음으로 달라지는 위치의 참가자가 완주하지 못한 선수다. 끝까지 같으면 참가자 배열의 마지막 원소가 정답이다.

### Java 코드

```java
import java.util.Arrays;
import java.util.stream.IntStream;

class Solution {
    public String solution(String[] participant, String[] completion) {
        Arrays.sort(participant);
        Arrays.sort(completion);

        return IntStream.range(0, completion.length)
                .filter(i -> !participant[i].equals(completion[i]))
                .mapToObj(i -> participant[i])
                .findFirst()
                .orElse(participant[participant.length - 1]);
    }
}
```

### 동작 예시

같은 입력을 정렬하면 다음과 같다.

| `i` | 참가자 | 완주자 | 일치 |
| --- | --- | --- | --- |
| 0 | `choi` | `choi` | 예 |
| 1 | `kim` | `kim` | 예 |
| 2 | `kim` | `lee` | 아니오 → `kim` 반환 |

### 시간 및 공간 복잡도

- 시간 복잡도: **`O(N log N)`** — 정렬이 지배한다.
- 추가 공간 복잡도: **`O(1)`** — 정렬에 쓰는 공간을 제외하면 맵이 필요 없다. 대신 입력 배열의 순서를 바꾼다.

## 풀이 비교

| 풀이 | 시간 | 추가 공간 | 특징 |
| --- | --- | --- | --- |
| 1. HashMap 카운트 | `O(N)` | `O(N)` | 순회 3번, 가장 정석적인 형태 |
| 2. 스트림 카운트 차감 | `O(N)` | `O(N)` | 찾는 즉시 종료, `filter` 안에 부수효과 |
| 3. 정렬 후 인덱스 비교 | `O(N log N)` | `O(1)` | 맵 불필요, 입력 배열 순서 변경 |

## 다시 풀 때 생각할 점

- 동명이인이 있으면 존재 여부(`Set`)가 아니라 개수(`Map<이름, 개수>`)를 본다.
- 횟수를 셀 때는 `map.getOrDefault(key, 0) + 1`을 `put`한다.
- 맵을 순회할 때는 `Map.Entry`와 `entrySet()`을 쓴다.
- 문제 조건이 보장하는 것은 검사하지 않는다.
- `import java.util.*;`는 하위 패키지를 포함하지 않는다. `Collectors`는 `java.util.stream`, `Function`은 `java.util.function`에서 따로 import 한다.
