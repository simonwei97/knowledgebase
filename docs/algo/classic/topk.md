---
date: 2024-03-28
categories:
  - Algo
search:
  boost: 2
hide:
  - footer
---

## 快排

- [X] 时间复杂度为 $O(n \log n)$。
    - 在平均情况下，哨兵划分的递归层数为 $log n$，每层中的总循环数为 $n$，总体使用 $O(n \log n)$ 时间。
- [X] 空间复杂度为 $O(n)$

```go title="Go"
/* 哨兵划分 */
func partition(nums []int, left, right int) int {
    // 以 nums[left] 为基准数
    i, j := left, right
    for i < j {
        for i < j && nums[j] >= nums[left] {
            j-- // 从右向左找首个小于基准数的元素
        }
        for i < j && nums[i] <= nums[left] {
            i++ // 从左向右找首个大于基准数的元素
        }
        // 元素交换
        nums[i], nums[j] = nums[j], nums[i]
    }
    // 将基准数交换至两子数组的分界线
    nums[i], nums[left] = nums[left], nums[i]
    return i // 返回基准数的索引
}

/* 快速排序 */
func quickSort(nums []int, left, right int) {
    // 子数组长度为 1 时终止递归
    if left >= right {
        return
    }
    // 哨兵划分
    pivot := partition(nums, left, right)
    // 递归左子数组、右子数组
    quickSort(nums, left, pivot-1)
    quickSort(nums, pivot+1, right)
}
```

```py title="Python"
def partition(self, nums: list[int], left: int, right: int) -> int:
    """哨兵划分"""
    # 以 nums[left] 为基准数
    i, j = left, right
    while i < j:
        while i < j and nums[j] >= nums[left]:
            j -= 1  # 从右向左找首个小于基准数的元素
        while i < j and nums[i] <= nums[left]:
            i += 1  # 从左向右找首个大于基准数的元素
        # 元素交换
        nums[i], nums[j] = nums[j], nums[i]
    # 将基准数交换至两子数组的分界线
    nums[i], nums[left] = nums[left], nums[i]
    return i  # 返回基准数的索引

def quick_sort(self, nums: list[int], left: int, right: int):
    """快速排序"""
    # 子数组长度为 1 时终止递归
    if left >= right:
        return
    # 哨兵划分
    pivot = self.partition(nums, left, right)
    # 递归左子数组、右子数组
    self.quick_sort(nums, left, pivot - 1)
    self.quick_sort(nums, pivot + 1, right)

```

- [https://www.hello-algo.com/chapter_sorting/quick_sort/](https://www.hello-algo.com/chapter_sorting/quick_sort/)
