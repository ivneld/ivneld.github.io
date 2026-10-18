---
title: "[LeetCode] 27. Remove Element"
date: 2026-10-18 22:30:00 +0900
categories: [Algorithm, LeetCode]
tags: [top-interview-150, array, two-pointer, java]
---

문제 링크: [27. Remove Element](https://leetcode.com/problems/remove-element/?envType=study-plan-v2&envId=top-interview-150)

## Solve

```java
/*
## 요구사항
숫자 배열 nums와 배열에서 제거할 숫자 val이 주어진다.
enums 내 val가 일치하지 않는 원소는 제거한다. 제거된 enums 배열의 유효한 원소 개수를 k라고 한다.
결론적으로 k 값이 무엇인지가 중요하고, enums 배열 내부가 어떻게 수정 되었는지는 중요하지 않다.

0 <= nums.length <= 100
0 <= nums[i] <= 50
0 <= val <= 100

## 풀이
int result = nums.length

nums 배열 순회
    각 요소가 val과 일치하는지 확인
        요소가 val와 일치한다면 -> result--;

result 반환
*/

class Solution {
    public int removeElement(int[] nums, int val) {
        int size = nums.length;
        int result = size;

        for (int i = 0; i < size; i++) {
            if (nums[i] == val) --result;
        }

        int idx = 0;
        int[] removedNums = new int[result];
        for (int i = 0; i < size; i++) {
            if (nums[i] != val) removedNums[idx++] = nums[i];
        }

        System.arraycopy(removedNums, 0, nums, 0, result);
        
        return result;
    }
}
```

## Review

### 해결하지 못한 이유
- -

### 문제 해석

1. *Given an integer array `nums` and an integer `val`, remove all occurrences of `val` in `nums` in-place.*

   정수 배열 `nums`와 정수 `val`이 주어질 때, `nums`에 있는 `val`의 모든 등장을 제자리에서(in-place) 제거하라.
2. *The order of the elements may be changed.*

   원소들의 순서는 바뀌어도 된다.
3. *Then return the number of elements in `nums` which are not equal to `val`.*

   그런 다음 `nums`에서 `val`과 같지 않은 원소의 개수를 반환하라.
4. *Consider the number of elements in `nums` which are not equal to `val` be `k`, to get accepted, you need to do the following things:*

   `nums`에서 `val`과 같지 않은 원소의 개수를 `k`라고 할 때, 정답으로 인정받으려면 다음을 해야 한다.
5. *Change the array `nums` such that the first `k` elements of `nums` contain the elements which are not equal to `val`. The remaining elements of `nums` are not important as well as the size of `nums`.*

   `nums`의 처음 `k`개 원소가 `val`과 같지 않은 원소들을 담도록 배열 `nums`를 변경하라. `nums`의 나머지 원소들은 `nums`의 크기와 마찬가지로 중요하지 않다.
6. *Return `k`.*

   `k`를 반환하라.
7. *Custom Judge: `sort(nums, 0, k);` `assert nums[i] == expectedNums[i];` If all assertions pass, then your solution will be accepted.*

   채점기는 반환값이 `k`인지 확인한 뒤, `nums`의 처음 `k`개 원소를 정렬해 기대값과 하나씩 비교한다. 모든 검증을 통과하면 정답으로 인정된다.
8. *Example 2: It does not matter what you leave beyond the returned `k`. Note that the five elements can be returned in any order.*

   반환된 `k` 이후에 무엇을 남기든 상관없다. 다섯 개의 원소는 어떤 순서로 반환되어도 된다는 점에 주목하라.
9. *Constraints*

   `0 <= nums.length <= 100`, `0 <= nums[i] <= 50`, `0 <= val <= 100`

> **in-place란?**  
> 새 배열을 만들지 않고 입력 배열 자체를 수정해 결과를 만드는 방식이다. 보통 추가 공간 `O(1)`을 기대한다.
{: .prompt-tip }

- 반환값 `k`만 맞으면 되는 문제가 아니다. 채점기가 `nums`의 앞 `k`칸을 직접 검사하므로 **`nums`를 수정하는 것까지가 요구사항**이다.
- 대신 검사 범위는 **앞 `k`칸**뿐이다. `k` 이후의 값과 앞 `k`칸 안의 **순서**는 상관없다. 이 두 가지 자유가 개선의 근거가 된다.

### 코드 수정

**1차 개선 - 투 포인터로 한 번에 처리**

내 코드는 배열을 두 번 순회하고(개수 세기, 복사), `k` 크기의 보조 배열을 만든 뒤 `nums`로 다시 복사한다.

읽는 위치 `i`와 쓸 위치 `k`를 따로 두면 `nums`에 바로 쓸 수 있다. `k`는 항상 `i`보다 작거나 같으므로 아직 읽지 않은 값을 덮어쓰지 않는다. -> 순회 1번, 보조 배열과 복사 제거, 추가 공간 `O(1)`.

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        int k = 0; // 다음에 val이 아닌 값을 쓸 위치 = 지금까지 찾은 val이 아닌 원소 수

        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != val) nums[k++] = nums[i]; // val이 아니면 앞으로 당겨 쓴다
        }

        return k; // 개수를 따로 세지 않아도 k가 곧 정답이다
    }
}
```

**2차 개선 - 마지막 원소와 교체해 쓰기 횟수 줄이기**

1차 개선은 `val`이 아닌 원소를 모두 다시 쓴다. `val`이 드물면 대부분의 쓰기가 같은 값을 제자리에 다시 쓰는 낭비가 된다 (예: `[1, 2, 3, 4, 5]`, `val = 5` -> 4번 쓰기).

순서는 바뀌어도 되므로 `val`을 만났을 때 배열의 마지막 유효 원소로 덮어쓰고 유효 범위를 한 칸 줄이면 된다. -> 쓰기 횟수가 `val`의 개수만큼으로 줄어든다.

```java
// nums = [3, 2, 2, 3], val = 3
// i=0, n=4: nums[0]=3 == val -> nums[0] = nums[3] -> [3, 2, 2, 3], n=3 (가져온 값도 3이므로 다시 검사)
// i=0, n=3: nums[0]=3 == val -> nums[0] = nums[2] -> [2, 2, 2, 3], n=2
// i=0, n=2: 2 != val -> i=1
// i=1, n=2: 2 != val -> i=2, 종료 -> k = 2, 앞 2칸 [2, 2]
class Solution {
    public int removeElement(int[] nums, int val) {
        int i = 0;
        int n = nums.length; // 유효 범위의 끝 (n 이후는 버린 영역)

        while (i < n) {
            if (nums[i] == val) {
                nums[i] = nums[n - 1]; // 마지막 유효 원소로 덮어쓴다 (순서가 바뀌어도 된다)
                n--;                   // 유효 범위를 줄인다. 가져온 값도 val일 수 있으므로 i는 그대로 둔다
            } else {
                i++;
            }
        }

        return n;
    }
}
```

> **이 개선 방법은 아직 이해하지 못했음**
{: .prompt-danger }

**코드 개선 방법 요약**

| 풀이 | 시간 | 보조 공간 | 쓰기 횟수 |
| --- | --- | --- | --- |
| 내 코드 | `O(n)`, 순회 2번 | `O(k)` | 보조 배열 `k` + 복사 `k` |
| 1차 개선 - 투 포인터 | `O(n)`, 순회 1번 | `O(1)` | `k` (`val`이 아닌 원소 수) |
| 2차 개선 - 마지막 원소와 교체 | `O(n)`, 순회 1번 | `O(1)` | `n - k` (`val`의 개수) |

- `val`이 많으면 1차, 적으면 2차가 쓰기 횟수가 적다. 원소 순서를 유지해야 하는 문제라면 1차만 쓸 수 있다.
- 제약이 `nums.length <= 100`이라 실행 시간 차이는 없다. 2차는 문제 조건(순서 무관)을 활용하는 연습으로 의미가 있다.
