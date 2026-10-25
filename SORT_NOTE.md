# 二分插入排序

二分插入排序是插入排序的改进版本。它把数组分成“左侧已排序部分”和“右侧未排序部分”，依次取出未排序部分的第一个元素作为 `key`。与普通插入排序从头或尾逐项比较不同，二分插入排序先用二分查找，在左侧有序部分中找到 `key` 应插入的位置，再把该位置及其后的元素整体向右移动一格，最后放入 `key`。

## 算法原理

设当前待插入元素的下标为 `i`，下标 `0` 到 `i - 1` 已经有序。二分查找的目标是找到第一个严格大于 `key` 的下标 `insertPos`：

- 若 `arr[mid] <= key`，插入位置在 `mid` 右侧，令 `left = mid + 1`。
- 若 `arr[mid] > key`，插入位置不晚于 `mid`，令 `right = mid`。
- 查找结束时 `left == right == insertPos`。

确定 `insertPos` 后，先将区间 `[insertPos, i - 1]` 的元素依次向右移动一格，再将 `key` 放到 `arr[insertPos]`。因为二分查找定位很快，但区间整体后移仍需逐个移动元素，所以这种优化主要减少了比较次数，并没有改变最坏情况下的平方级数据移动量。

使用“第一个严格大于 `key` 的位置”作为插入位置，可以让值相等的元素保持原来的相对顺序，因此该实现是稳定的。

## 复杂度

| 指标 | 复杂度 | 说明 |
| --- | --- | --- |
| 比较次数 | `O(n log n)` | 每轮在长度为 `i` 的有序前缀中二分查找，复杂度为 `O(log i)` |
| 元素移动次数 | `O(n²)` | 最坏情况下每轮都要移动几乎整个有序前缀 |
| 最坏时间复杂度 | `O(n²)` | 逆序输入时移动量达到最大 |
| 平均时间复杂度 | `O(n²)` | 总体耗时由平方级的元素移动主导 |
| 最好时间复杂度 | `O(n log n)` | 已有序时无需移动，但仍要为每个元素进行二分比较 |
| 额外空间复杂度 | `O(1)` | 只使用数组本身和常数个辅助变量 |
| 稳定性 | 稳定 | 相等元素不会越过先出现的元素 |

## Java 可运行示例

创建文件 `BinaryInsertionSortDemo.java`，写入以下代码：

```java
import java.util.Arrays;

public class BinaryInsertionSortDemo {
    public static void binaryInsertionSort(int[] arr) {
        if (arr == null || arr.length < 2) {
            return;
        }

        for (int i = 1; i < arr.length; i++) {
            int key = arr[i];

            // 在有序区间 [0, i) 中找到第一个严格大于 key 的位置。
            int left = 0;
            int right = i;
            while (left < right) {
                int mid = left + (right - left) / 2;
                if (arr[mid] <= key) {
                    left = mid + 1;
                } else {
                    right = mid;
                }
            }
            int insertPos = left;

            // 将 [insertPos, i) 整体右移，为 key 腾出位置。
            for (int j = i; j > insertPos; j--) {
                arr[j] = arr[j - 1];
            }
            arr[insertPos] = key;
        }
    }

    public static void main(String[] args) {
        int[] values = {5, 1, 4, 2, 8, 4, 0, 9, 3};
        System.out.println("排序前: " + Arrays.toString(values));

        binaryInsertionSort(values);

        System.out.println("排序后: " + Arrays.toString(values));
    }
}
```

在包含上述文件的目录中执行：

```bash
javac BinaryInsertionSortDemo.java
java BinaryInsertionSortDemo
```

程序输出：

```text
排序前: [5, 1, 4, 2, 8, 4, 0, 9, 3]
排序后: [0, 1, 2, 3, 4, 4, 5, 8, 9]
```
