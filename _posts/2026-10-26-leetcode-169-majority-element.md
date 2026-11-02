---
title: "[LeetCode] 169. Majority Element"
date: 2026-10-26 21:56:00 +0900
categories: [Algorithm, LeetCode]
tags: [top-interview-150, array, hash-table, boyer-moore, java, 다시 풀기]
---

문제 링크: [169. Majority Element](https://leetcode.com/problems/majority-element/)

## 문제 해석

길이가 `n`인 정수 배열 `nums`에서 과반수 원소를 반환한다. 과반수 원소는 `⌊n / 2⌋`번보다 많이 등장하는 값이다. 단순히 가장 많이 등장하는 값이라는 뜻이 아니라, 배열 전체의 절반을 초과해서 등장해야 한다.

이 문제에서는 과반수 원소가 반드시 존재한다고 보장한다. 배열은 정렬되어 있지 않으며, 원소의 순서를 바꾸거나 중복을 제거할 필요는 없다.

- `[3, 2, 3]`: `3`이 2번 등장하므로 정답은 `3`이다.
- `[2, 2, 1, 1, 1, 2, 2]`: `2`가 4번 등장하므로 정답은 `2`이다.

추가 질문은 시간 `O(n)`, 추가 공간 `O(1)`로 해결할 수 있는지다.

## Review

### 해결 여부

스스로 해결하지 못한 문제다. 나중에 풀이를 보지 않고 다시 풀어보기 위해 `다시 풀기`로 분류한다.

### 내 풀이 점검

`HashMap`으로 숫자별 등장 횟수를 세고, 횟수가 `nums.length / 2`보다 커지는 순간 해당 숫자를 반환하려고 했다. 풀이 방향은 맞았지만 `HashMap` 메서드가 기억나지 않아 조건문에서 멈췄다.

```java
class Solution {
    public int majorityElement(int[] nums) {
        HashMap<int, int> counter = new HashMap<>();

        for (num : nums) {
            if (counter.)
        }
    }
}
```

메서드 외에도 고쳐야 할 부분이 있었다.

- `HashMap<int, int>`: 제네릭에는 기본형을 쓸 수 없으므로 `HashMap<Integer, Integer>`로 선언한다.
- `for (num : nums)`: 향상된 `for` 문의 변수에도 타입이 필요하므로 `for (int num : nums)`로 쓴다.
- 반복문 뒤의 `return`: 반복문 안에서 항상 반환되더라도 컴파일러는 이를 알 수 없다. 메서드 끝에도 `return`이나 `throw`가 있어야 컴파일된다.

## 풀이 1 — HashMap

### 풀이 방법

의도했던 풀이를 그대로 완성하면 다음과 같다. 필요한 메서드는 `containsKey`, `get`, `put` 세 가지다.

```java
if (counter.containsKey(num)) {
    counter.put(num, counter.get(num) + 1);
} else {
    counter.put(num, 1);
}

if (counter.get(num) > nums.length / 2) {
    return num;
}
```

과반수 확인은 `else`까지 끝난 뒤에 해야 한다. `if` 블록 안에서만 확인하면 `[1]`처럼 처음 등장한 숫자가 바로 정답인 경우를 놓친다. `n = 1`이면 `n / 2 = 0`이므로 첫 등장에서 이미 조건을 만족하기 때문이다.

`getOrDefault`를 쓰면 키가 없을 때의 기본값을 지정할 수 있어서 `if`-`else` 분기가 사라진다.

| 메서드 | 동작 |
| --- | --- |
| `containsKey(key)` | 키가 있으면 `true`, 없으면 `false`를 반환한다. |
| `get(key)` | 키의 값을 반환한다. 키가 없으면 `null`을 반환한다. |
| `put(key, value)` | 키에 값을 저장한다. 이미 있는 키라면 값을 덮어쓴다. |
| `getOrDefault(key, defaultValue)` | 키의 값을 반환한다. 키가 없으면 `defaultValue`를 반환한다. |

`get`이 반환한 `null`에 `+ 1`을 하면 `NullPointerException`이 발생한다. 그래서 `containsKey`로 먼저 확인하거나 `getOrDefault`를 사용해야 한다.

### Java 코드

```java
class Solution {
    public int majorityElement(int[] nums) {
        HashMap<Integer, Integer> counter = new HashMap<>();

        for (int num : nums) {
            int count = counter.getOrDefault(num, 0) + 1;
            counter.put(num, count);

            if (count > nums.length / 2) {
                return num;
            }
        }

        throw new IllegalArgumentException("majority element does not exist");
    }
}
```

과반수 원소가 반드시 존재하므로 마지막 줄은 실행되지 않는다. `-1`도 입력으로 들어올 수 있는 값이라 `return -1` 대신 예외를 던졌다.

정수끼리의 나눗셈은 소수점 아래를 버리므로 `nums.length / 2`는 `⌊n / 2⌋`와 같다.

### 동작 예시

`nums = [2, 2, 1, 1, 1, 2, 2]`이면 `nums.length / 2 = 3`이므로 등장 횟수가 4가 되는 순간 반환한다.

| 현재 숫자 | `counter` | `count` | `count > 3` |
| --- | --- | --- | --- |
| 2 | `{2=1}` | 1 | 아니오 |
| 2 | `{2=2}` | 2 | 아니오 |
| 1 | `{2=2, 1=1}` | 1 | 아니오 |
| 1 | `{2=2, 1=2}` | 2 | 아니오 |
| 1 | `{2=2, 1=3}` | 3 | 아니오 |
| 2 | `{2=3, 1=3}` | 3 | 아니오 |
| 2 | `{2=4, 1=3}` | 4 | 예 → `2` 반환 |

정답이 앞쪽에서 정해지면 배열을 끝까지 보지 않는다. 예를 들어 `[1, 1, 1, 2, 3]`은 세 번째 원소에서 `3 > 2`가 되어 바로 `1`을 반환한다.

### 시간 및 공간 복잡도

- 시간 복잡도: **`O(n)`** — 배열을 한 번 순회하고, `HashMap` 조회와 저장은 평균 `O(1)`이다.
- 추가 공간 복잡도: **`O(n)`** — 서로 다른 숫자의 개수만큼 `HashMap`에 저장한다.

시간 조건은 만족하지만 공간이 `O(n)`이므로 추가 질문의 `O(1)` 조건은 만족하지 못한다. 이 조건까지 만족하려면 풀이 2가 필요하다.

## 풀이 2 — Boyer–Moore 투표 알고리즘

### 풀이 방법

핵심은 **서로 다른 숫자 두 개를 한 쌍으로 제거해도 과반수 원소는 남는다**는 점이다.

과반수 원소의 개수를 `M`, 나머지 원소의 개수를 `R`이라고 하면 `M > R`이다. 과반수 원소와 다른 원소를 하나씩 제거하면 `M - 1 > R - 1`이므로 과반수 원소 쪽이 여전히 더 많다. 나머지 원소끼리 제거하면 과반수 원소의 우위는 더 커진다.

따라서 서로 다른 값들을 계속 상쇄하면 마지막에 남는 값이 정답이다. 실제로 원소를 제거하지 않고 다음 두 변수로 상쇄 과정을 표현한다.

- `candidate`: 현재 남아 있는 숫자의 후보
- `count`: 상쇄되지 않고 남아 있는 후보의 개수

배열을 앞에서부터 읽으면서 다음과 같이 처리한다.

1. `count == 0`이면 남아 있는 후보가 없으므로 현재 숫자를 새 후보로 정한다.
2. 현재 숫자가 후보와 같으면 `count`를 1 증가시킨다.
3. 다르면 후보 하나와 현재 숫자 하나를 상쇄한 것으로 보고 `count`를 1 감소시킨다.

`count`는 후보가 배열 전체에서 등장한 횟수가 아니다. 서로 다른 숫자를 상쇄하고 남은 개수다.

### Java 코드

```java
class Solution {
    public int majorityElement(int[] nums) {
        int candidate = 0;
        int count = 0;

        for (int num : nums) {
            if (count == 0) {
                candidate = num;
            }

            if (num == candidate) {
                count++;
            } else {
                count--;
            }
        }

        return candidate;
    }
}
```

`candidate`의 초기값 `0`에는 특별한 의미가 없다. 첫 번째 원소를 처리할 때 `count == 0`이므로 실제 원소로 교체된다. 음수나 `0`이 입력되어도 같은 방식으로 동작한다.

### 동작 예시

`nums = [2, 2, 1, 1, 1, 2, 2]`를 순회해 보자. 표의 후보와 개수는 각 원소의 처리가 끝난 뒤의 값이다.

| 현재 숫자 | 처리 | `candidate` | `count` |
| --- | --- | --- | --- |
| 2 | 개수가 0이므로 후보를 2로 정하고 증가 | 2 | 1 |
| 2 | 후보와 같으므로 증가 | 2 | 2 |
| 1 | 후보와 다르므로 상쇄 | 2 | 1 |
| 1 | 후보와 다르므로 상쇄 | 2 | 0 |
| 1 | 개수가 0이므로 후보를 1로 정하고 증가 | 1 | 1 |
| 2 | 후보와 다르므로 상쇄 | 1 | 0 |
| 2 | 개수가 0이므로 후보를 2로 정하고 증가 | 2 | 1 |

최종 후보인 `2`를 반환한다. 중간에 후보가 `1`로 바뀌어도 괜찮다. 전체 배열을 처리한 후 남는 후보가 중요하다.

### 후보를 다시 검증하지 않아도 되는 이유

문제에서 과반수 원소가 반드시 존재한다고 보장하므로 마지막 후보를 바로 반환할 수 있다.

이 보장이 없다면 투표 과정만으로 과반수 원소의 존재를 판단할 수 없다. 예를 들어 `[1, 2, 3]`에서도 후보는 남지만 과반수 원소는 없다. 그런 경우에는 배열을 한 번 더 순회해 후보의 실제 등장 횟수가 `n / 2`를 초과하는지 확인해야 한다.

### 시간 및 공간 복잡도

- 시간 복잡도: **`O(n)`** — 배열을 한 번 순회한다.
- 추가 공간 복잡도: **`O(1)`** — 후보와 개수만 저장한다.

## 다시 풀 때 생각할 점

- `HashMap`의 키와 값에는 `int`가 아니라 `Integer`를 쓴다.
- 횟수를 셀 때는 `counter.getOrDefault(num, 0) + 1`을 `put`한다.
- 과반수 확인은 처음 등장한 숫자도 거치도록 분기 밖에서 한다.
- 과반수 원소는 나머지 모든 원소를 합친 것보다 많다.
- 서로 다른 숫자를 하나씩 상쇄하면 과반수 원소가 끝까지 남는다.
- `count == 0`이면 다음 숫자를 새 후보로 선택할 수 있다.
- `count`는 실제 등장 횟수가 아니라 상쇄 후 남은 개수다.
