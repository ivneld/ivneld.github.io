---
title: "[LeetCode] 26. Remove Duplicates from Sorted Array"
date: 2026-10-23 21:00:00 +0900
categories: [Algorithm, LeetCode]
tags: [top-interview-150, array, two-pointer, java, 직접 해결]
---

문제 링크: [26. Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/description/?envType=study-plan-v2&envId=top-interview-150)

## Solve

```java
/*
## 요구사항
증가하는 숫자 배열 nums가 주어진다. 배열에서 중복 값들은 제거해라. 순서는 그대로 유지되어야 한다.
k는 배열에서 중복 값들을 제거한 후 배열의 길이다.
중복을 제거한 숫자 배열 원소의 앞에서부터 k개는 정렬된 고유한 값이다.
removeDuplicates 함수를 검증할 때는 k-1 인덱스 뒤의 원소들은 무시하고 검증한다.

## 풀이
nums = [1, 1, 2] -> nums = [1, 2]
count = 0
i = 0, j = i + 1
nums[i] 보다 nums[j]는 항상 값이 커야 한다.
크지 않으면 j++ -> 반복, 크다면 nums[i+1] = nums[j], i = j + 1, ++count / nums = [1, 2, 2]
j가 nums의 사이즈보다 작을때 까지만 반복
*/

class Solution {
    public int removeDuplicates(int[] nums) {
        int size = nums.length;
        int count = 1;

        int i = 0, j = 1;
        while (j < size && i < size) {
            if (nums[i] == nums[j]) {
                j++;
            }
            else if (nums[i] < nums[j]) {
                ++i;
                nums[i] = nums[j];
                ++count;
                ++j;
            }
            else throw new IllegalArgumentException("");
        }

        return count;
    }
}
```

## Review

### 해결하지 못한 이유

- -

### 문제 해석

1. *Given an integer array `nums` sorted in non-decreasing order, remove the duplicates in-place such that each unique element appears only once.*

   비감소 순서로 정렬된 정수 배열 `nums`가 주어질 때, 각 고유한 원소가 한 번만 나타나도록 중복을 제자리에서(in-place) 제거하라.
2. *The relative order of the elements should be kept the same.*

   원소들의 상대적인 순서는 그대로 유지되어야 한다.
3. *Consider the number of unique elements in `nums` to be `k`. After removing duplicates, return the number of unique elements `k`.*

   `nums`의 고유한 원소 개수를 `k`라고 하자. 중복을 제거한 뒤 고유한 원소의 개수 `k`를 반환하라.
4. *The first `k` elements of `nums` should contain the unique numbers in sorted order. The remaining elements beyond index `k - 1` can be ignored.*

   `nums`의 처음 `k`개 원소는 고유한 숫자들을 정렬된 순서로 담고 있어야 한다. 인덱스 `k - 1` 이후의 나머지 원소들은 무시해도 된다.
5. *Custom Judge: `assert k == expectedNums.length;` `assert nums[i] == expectedNums[i];` If all assertions pass, then your solution will be accepted.*

   채점기는 반환값이 기대 배열의 길이와 같은지 확인한 뒤, `nums`의 처음 `k`개 원소를 **정렬하지 않고** 기대값과 하나씩 비교한다. 모든 검증을 통과하면 정답으로 인정된다.
6. *Example 1: It does not matter what you leave beyond the returned `k` (hence they are underscores).*

   반환된 `k` 이후에 무엇을 남기든 상관없다 (그래서 밑줄로 표시했다).
7. *Constraints*

   `1 <= nums.length <= 3 * 10^4`, `-100 <= nums[i] <= 100`, `nums`는 비감소 순서로 정렬되어 있다.

> **non-decreasing order란?**  
> 감소하지 않는 순서다. 같은 값이 연속될 수 있는 오름차순이다 (예: `0, 0, 1, 1, 1, 2`). "증가하는 배열"이라고 읽으면 중복이 없다는 뜻이 되므로, 중복이 있을 수 있다는 점이 이 표현의 핵심이다.
{: .prompt-tip }

- 배열이 정렬되어 있으므로 **같은 값은 항상 붙어 있다.** 따라서 바로 앞의 고유 값과만 비교하면 중복인지 알 수 있다.
- 자바 배열은 길이를 바꿀 수 없다. `[1, 1, 2]`가 `[1, 2]`가 되는 것이 아니라 `[1, 2, 2]`처럼 **앞 `k`칸만 의미 있는 값**이 되고, 길이는 반환값 `k`로 전달한다.
- [27. Remove Element](/posts/leetcode-27-remove-element/)와 달리 채점기가 앞 `k`칸을 정렬하지 않고 비교한다. 즉 **순서 유지가 요구사항**이라 마지막 원소와 바꿔 쓰는 방식은 쓸 수 없다.

### 내 풀이 점검

`i`는 마지막으로 확정한 고유 값의 위치, `j`는 지금 읽는 위치인 투 포인터 풀이다. 정답으로 통과하며 시간 `O(n)`, 추가 공간 `O(1)`로 이미 최적 복잡도다.

```java
// nums = [0, 0, 1, 1, 2]
// i=0, j=1: 0 == 0 -> j=2
// i=0, j=2: 0 <  1 -> i=1, nums[1]=1 -> [0, 1, 1, 1, 2], count=2, j=3
// i=1, j=3: 1 == 1 -> j=4
// i=1, j=4: 1 <  2 -> i=2, nums[2]=2 -> [0, 1, 2, 1, 2], count=3, j=5, 종료
// k = 3, 앞 3칸 [0, 1, 2]
```

다만 코드를 다듬을 여지가 있다.

- **`count`는 항상 `i + 1`이다.** `i`가 증가할 때마다 `count`도 함께 증가하므로 같은 정보를 두 변수가 들고 있다. `i + 1`을 반환하면 된다.
- **`i < size` 조건은 필요 없다.** `i`는 항상 `j`보다 작으므로 `j < size`이면 `i < size`도 참이다.
- **`else throw` 분기는 도달하지 않는다.** 정렬된 입력이 보장되므로 `nums[i] > nums[j]`인 경우가 없다. 입력 검증 용도라면 두 가지가 아쉽다.
  - 메시지가 비어 있어 어떤 입력이 왜 잘못됐는지 알 수 없다.
  - 예외가 나기 전에 `nums`가 이미 수정될 수 있다. 예: `[1, 1, 3, 2]` -> `nums[1] = 3`을 쓴 뒤 `3 > 2`에서 예외 -> 호출자는 `[1, 3, 3, 2]`로 망가진 배열을 받는다. 검증이 필요하다면 수정 전에 따로 하는 편이 안전하다.
- **빈 배열이면 `1`을 반환한다.** `count`를 `1`로 시작하기 때문이다. 제약상 `nums.length >= 1`이라 문제는 없지만, 일반 함수로 쓰려면 `0`을 반환해야 한다.
- **주석의 풀이와 코드가 다르다.** 주석은 `count = 0`, `i = j + 1`이지만 코드는 `count = 1`, `++i`다. 코드가 맞고 주석이 틀렸다. `i = j + 1`로 구현했다면 고유 값이 연속으로 나올 때 칸을 건너뛰어 오답이 된다.

### 코드 수정

**1차 개선 - 중복 변수와 조건 제거**

`count`, `i < size`, `else throw`를 정리하면 비교 한 번으로 줄어든다. `nums[j] != nums[i]`이면 새 고유 값이므로 `i` 다음 칸에 쓴다.

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        int i = 0; // 마지막으로 확정한 고유 값의 위치

        for (int j = 1; j < nums.length; j++) {
            if (nums[j] != nums[i]) nums[++i] = nums[j]; // 새 고유 값이면 다음 칸에 쓴다
        }

        return i + 1; // 고유 값 개수 = 마지막 위치 + 1
    }
}
```

**2차 개선 - 쓸 위치 `k` 기준으로 바꿔 빈 배열 처리와 일반화**

`i`를 "마지막으로 쓴 위치"가 아니라 "다음에 쓸 위치 `k`"로 바꾸면 [27번 1차 개선](/posts/leetcode-27-remove-element/)과 같은 형태가 된다.

- `k`가 곧 고유 값 개수라서 `+ 1` 보정이 필요 없고, 빈 배열이면 반복을 돌지 않아 `0`을 반환한다.
- 비교 대상이 "이미 쓴 마지막 값 `nums[k - 1]`"이 되어 조건을 일반화하기 쉽다.

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        int k = 0; // 다음에 고유 값을 쓸 위치 = 지금까지 확정한 고유 값 수

        for (int num : nums) {
            if (k == 0 || num != nums[k - 1]) nums[k++] = num; // 첫 원소이거나 마지막으로 쓴 값과 다르면 쓴다
        }

        return k;
    }
}
```

`k - 1`을 `k - m`으로 바꾸면 "같은 값을 최대 `m`개까지 남기기"가 된다. 다음 문제인 [80. Remove Duplicates from Sorted Array II](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/?envType=study-plan-v2&envId=top-interview-150)가 `m = 2`인 경우다.

```java
// m = 1이면 26번, m = 2이면 80번
int k = 0;
for (int num : nums) {
    if (k < m || num != nums[k - m]) nums[k++] = num;
}
return k;
```

> **쓰기 횟수를 더 줄일 수 있을까?**  
> 중복이 하나도 없는 구간에서는 `i + 1 == j`라서 `nums[j]`를 자기 자리에 다시 쓴다 (예: `[1, 2, 3]` -> 2번 쓰기). `if (i + 1 != j)`로 막을 수 있지만, 쓰기를 줄이는 대신 비교가 하나 늘어나고 배열 쓰기 자체가 매우 싸므로 실익이 거의 없다. 27번 2차 개선처럼 순서를 바꿔 쓰기를 줄이는 방법은 순서 유지 요구사항 때문에 쓸 수 없다.
{: .prompt-info }

**코드 개선 방법 요약**

| 풀이 | 시간 | 보조 공간 | 빈 배열 | 비고 |
| --- | --- | --- | --- | --- |
| 내 코드 | `O(n)`, 순회 1번 | `O(1)` | `1` 반환 | `count`, `i < size`, `throw` 분기가 불필요 |
| 1차 개선 - 중복 변수와 조건 제거 | `O(n)`, 순회 1번 | `O(1)` | `1` 반환 | 비교 1번, 반환 시 `+ 1` |
| 2차 개선 - 쓸 위치 `k` 기준 | `O(n)`, 순회 1번 | `O(1)` | `0` 반환 | 80번으로 바로 일반화 가능 |

- 세 풀이 모두 복잡도는 같다. 내 풀이도 이미 최적이며, 개선은 코드를 단순하게 만들고 경계 조건과 확장성을 챙기는 방향이다.
- 정렬된 배열에서 같은 값이 붙어 있다는 성질 덕분에 `HashSet` 없이 바로 앞 값과의 비교만으로 중복을 판단할 수 있다. 정렬되지 않은 배열이었다면 `O(n)` 추가 공간의 `HashSet`이나 `O(n log n)` 정렬이 필요하다.
