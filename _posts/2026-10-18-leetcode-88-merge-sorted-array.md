---
title: "[LeetCode] 88. Merge Sorted Array"
date: 2026-10-18 20:00:00 +0900
categories: [Algorithm, LeetCode]
tags: [top-interview-150, array, two-pointer, java]
---

문제 링크: [88. Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array/)

## Solve

```java
/*
## 단어 정리
- non-decreasing order : 오름차순?
- respectively
- to accommodate this
- denote

## 문제 분석
길이가 각각 m, n인 숫자 배열 num1, num2가 주어진다. (0은 제외한 길이임)
num1, num2를 오름차순으로 병합한다. 병합된 새로운 숫자 배열을 리턴하는 것이 아니라 nums1 배열에 병합해야 한다.

## 풀이
t = m + n, nums3[t] 배열 생성

idx가 t-1에 도달할 때 까지 반복, i = 0, j = 0
    nums1[i]가 0이거나, i가 nums1.size보다 크면 nums3[t] = nums2[j] i, j++
    nums2[j]가 0이거나, j가 nums2.size보다 크면 nums3[t] = nums1[i] i, j++
    nums1[i] <= nums2[j] 이면 nums3[t] = nums1[i] i++
    nums1[i] > nums2[j] 이면 nums3[t] = nums2[j] j++

nums2 = nums3
*/

class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int t = m + n;
        int nums3[t] = {0};

        int idx, i, j = 0;
        while (idx < t) {
            if (nums1[i] == 0 || i > m) {
                nums3[idx] = nums2[j];
                i++;
                j++;
            }
            if (nums2[j] == 0 || j > n) {
                nums3[idx] = nums1[i];
                i++;
                j++;
            }

            if (nums1[i] <= nums2[j]) {
                nums3[idx] = nums2[j];
                i++;
            }
            else {
                num3[idx] = nums1[i];
                j++;
            }

            idx++;
        }

        nums1 = nums3;
    }
}
```

## Review

### 해결하지 못한 이유

- nums1, 2, 3가 모두 오름차순 배열이라는 요구사항은 파악했지만 0이 배열의 가장 끝 자리에만 배치된다는 것인지? 구체적으로 파악하지 못했음
- 어떤 알고리즘을 사용해서 문제를 해결할 수 있는지 떠오르지 않음
- 자바 문법이 익숙하지 않아서 배열을 어떻게 생성해야 할지 모름

### 문제 해석

1. *You are given two integer arrays `nums1` and `nums2`, sorted in non-decreasing order, and two integers `m` and `n`, representing the number of elements in `nums1` and `nums2` respectively.*

   비감소 순서로 정렬된 두 정수 배열 `nums1`과 `nums2`, 그리고 각각 `nums1`과 `nums2`의 원소 개수를 나타내는 두 정수 `m`과 `n`이 주어진다.
2. *Merge `nums1` and `nums2` into a single array sorted in non-decreasing order.*

   `nums1`과 `nums2`를 비감소 순서로 정렬된 하나의 배열로 병합하라.
3. *The final sorted array should not be returned by the function, but instead be stored inside the array `nums1`.*

   최종 정렬된 배열은 함수에 의해 반환되어서는 안 되며, 대신 배열 `nums1` 안에 저장되어야 한다.
4. *To accommodate this, `nums1` has a length of `m + n`, where the first `m` elements denote the elements that should be merged, and the last `n` elements are set to `0` and should be ignored. `nums2` has a length of `n`.*

   이를 수용하기 위해 `nums1`은 `m + n`의 길이를 가지며, 처음 `m`개의 원소는 병합되어야 할 원소들을 나타내고, 마지막 `n`개의 원소는 `0`으로 설정되어 있으며 무시되어야 한다. `nums2`는 `n`의 길이를 가진다.
5. *Example 3: Note that because `m = 0`, there are no elements in `nums1`. The `0` is only there to ensure the merge result can fit in `nums1`.*

   `m = 0`이기 때문에 `nums1`에는 원소가 없다는 점에 주목하라. `0`은 오직 병합 결과가 `nums1`에 들어갈 수 있도록 보장하기 위해 존재한다.
6. *Constraints*

   `nums1.length == m + n`, `nums2.length == n`, `0 <= m, n <= 200`, `1 <= m + n <= 200`, `-10^9 <= nums1[i], nums2[j] <= 10^9`
7. *Follow up: Can you come up with an algorithm that runs in `O(m + n)` time?*

   `O(m + n)` 시간에 실행되는 알고리즘을 생각해낼 수 있는가?

> **non-decreasing order란?**  
> 감소하지 않는 순서다. 같은 값이 연속될 수 있는 오름차순이다 (예: `1, 2, 2, 3`).
{: .prompt-tip }

- `nums1` 끝의 `0`은 값이 아니라 병합 결과를 담기 위한 **빈 자리**이며, 정확히 뒤쪽 `n`칸에만 있다.
- 원소 값의 범위에 `0`과 음수가 포함된다. 따라서 유효 범위는 값이 아니라 **개수** `m`, `n`으로 판단해야 한다.

### 코드 수정

**1차 개선 - 정답 코드**

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int t = m + n;
        int[] nums3 = new int[t];

        int idx = 0, i = 0, j = 0;
        while (idx < t) {
            if (i >= m) {
                nums3[idx] = nums2[j];
                j++;
            }
            else if (j >= n) {
                nums3[idx] = nums1[i];
                i++;
            }
            else if (nums1[i] <= nums2[j]) {
                nums3[idx] = nums1[i];
                i++;
            }
            else {
                nums3[idx] = nums2[j];
                j++;
            }
            idx++;
        }

        System.arraycopy(nums3, 0, nums1, 0, t);
    }
}
```

**2차 개선 - 보조 배열 크기 축소 m+n -> m**

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        // 미리 nums1의 실제 값을 담기 위한 배열을 생성한 뒤 옮겨 놓으면 m + n 크기의 배열을 새로 만들 필요 없이 nums1을 그대로 재사용하면 됌
        int[] nums1Cp = new int[m];
        System.arraycopy(nums1, 0, nums1Cp, 0, m);

        int i = 0, j = 0, k = 0;
        while (i < m && j < n) {
            if (nums1Cp[i] <= nums2[j]) nums1[k++] = nums1Cp[i++];
            else nums1[k++] = nums2[j++];
        }

        while (i < m) nums1[k++] = nums1Cp[i++];
        while (j < n) nums1[k++] = nums2[j++];
    }
}
```

**3차 개선 - 보조 공간 제거, 쓰기 횟수 최소화**

`nums1`의 빈 자리는 뒤쪽에 있다. 뒤에서부터 큰 값을 채우면 쓰는 위치 `k`가 읽는 위치 `i`보다 항상 뒤에 있어 덮어쓸 일이 없다. -> 보조 배열이 필요 없다.

또한 `nums2`를 모두 옮기면 `nums1`에 남은 값은 이미 정렬된 채 제자리에 있으므로 반복을 바로 끝낸다. -> `nums1` 원소를 옮기는 횟수가 줄어든다 (예: `nums2`가 전부 작으면 `nums1` 원소는 한 번도 옮기지 않는다).

```java
// [5, 6], [1, 2, 3]
// [5, 6, 0, 0, 0]
// [5, 6, 0, 0, 6]
// [5, 6, 0, 5, 6]
// [5, 6, 3, 5, 6]
// [5, 2, 3, 5, 6]
// [1, 2, 3, 5, 6]
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int i = m - 1;
        int j = n - 1;
        int k = m + n - 1;

        while (j >= 0) {
            if (i >= 0 && nums1[i] > nums2[j]) nums1[k--] = nums1[i--];
            else nums1[k--] = nums2[j--];
        }
    }
}
```

**코드 개선 방법 요약**

| 풀이 | 시간 | 보조 공간 | 쓰기 횟수 |
| --- | --- | --- | --- |
| 1차 개선 - 정답 코드 | `O(m + n)` | `O(m + n)` | 병합 `m + n` + 복사 `m + n` |
| 2차 개선 - `m` 크기 보조 배열 | `O(m + n)` | `O(m)` | 복사 `m` + 병합 `m + n` |
| 3차 개선 - 뒤에서부터 채우기 | `O(m + n)` | `O(1)` | 최대 `m + n`, `nums2` 소진 시 종료 |

> **`System.arraycopy`**  
> 배열의 지정 구간을 다른 배열(또는 같은 배열)로 복사하는 자바 표준 메서드다. `System.arraycopy(src, srcPos, dest, destPos, length)`
>
> - 동작: 시작 전에 범위와 타입을 한 번만 검사한 뒤, JIT이 CPU에 최적화된 메모리 블록 복사(intrinsic)로 대체해 구간을 통째로 옮긴다. 원본과 대상 구간이 겹쳐도 올바르게 복사된다.
> - 성능: 시간 `O(length)`로 반복문과 빅오는 같다. 다만 원소별 범위 검사가 없고 SIMD로 여러 원소를 한 번에 옮겨 상수 배가 작다. 대상 배열은 미리 만들어 두므로 추가 공간은 들지 않는다.
> - 주의: 작은 배열에서는 반복문과 차이가 거의 없다. 타입이 다른 객체 배열 간 복사는 원소마다 타입 검사가 붙는다. `Arrays.copyOf`도 내부에서 이 메서드를 사용한다.
{: .prompt-tip }
