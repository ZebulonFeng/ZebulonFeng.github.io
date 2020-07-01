简单来说，假设我们以1做索引的起点，那么当我们要计算某个数组元素的内存地址时，就要做如下操作

```c
*(array_addr_start + index - 1)
```

如果我们以0做索引的起点，那么操作如下

```c
*(array_addr_start + index)
```

比较两种操作，以0做起点可以少一次减法运算。别小看这一次运算，“计算地址”作为高频操作，在最底层它的开销会被极度放大。计算一个内存地址少一次运算，计算一万个内存地址就可以少一万次运算，节省的CPU时间相当可观。

这是从实用角度来考虑问题的，**下面我们来从数学角度考虑**。

引用Dijkstra的一篇随笔

http://www.cs.utexas.edu/users/EWD/transcriptions/EWD08xx/EWD831.html

> To denote the subsequence of natural numbers 2, 3, ..., 12 without the pernicious three dots, four conventions are open to us.

为了表示自然数序列 2, 3, ... , 12 时不使用碍眼的3个点号（表示省略），给出下面4个约定。


|    |  |
| - | - |
| a)           | 2 ≤ i < 13     |
| b)           |   1 < i ≤ 12   |
| c)           |   2 ≤ i ≤ 12   |
| d)           |    1 < i < 13  |

> Are there reasons to prefer one convention to the other? Yes, there are. The observation that conventions a) and b) have the advantage that the difference between the bounds as mentioned equals the length of the subsequence is valid. So is the observation that, as a consequence, in either convention two subsequences are adjacent means that the upper bound of the one equals the lower bound of the other. Valid as these observations are, they don't enable us to choose between a) and b); so let us start afresh.

我们有理由选择其中一个而放弃其它吗？答案是肯定的。观察得出，约定 a） 和 b） 具有前述”边界之间的差值等于子序列的长度“这个有效的优点（上界和下界相减等于11，省略号代表的子序列长度为11）。因此，在任一约定中，两个子序列相邻，表示一个子序列的上限等于另一个子序列的下限。尽管这些观测值有效，但它们不能让我们在 a） 和 b 之间进行选择;让我们重新开始吧。

> There is a smallest natural number. Exclusion of the lower bound —as in b) and d)— forces for a subsequence starting at the smallest natural number the lower bound as mentioned into the realm of the unnatural numbers. That is ugly, so for the lower bound we prefer the ≤ as in a) and c). Consider now the subsequences starting at the smallest natural number: inclusion of the upper bound would then force the latter to be unnatural by the time the sequence has shrunk to the empty one. That is ugly, so for the upper bound we prefer < as in a) and d). We conclude that convention a) is to be preferred.

有一个最小的自然数。排除下限 - 如b）和d）中所示，强制从最小自然数开始的子序列将会把下限引入非自然数域中。这很丑陋，因此对于下限，我们更喜欢a）和c）中的≤。现在考虑从最小自然数开始的子序列：包含上限将在序列缩小为空数时迫使上界成为非自然数。这很丑陋，因此对于上限，我们更喜欢<，如a）和d）。
我们得出结论，约定a）是首选。

*********
相比较 1 ≤ i < n+1 ， 0 ≤ i < n 是更优雅的表示法，它能直接、形象地描述数组为空的情况，0才是最自然的数字。