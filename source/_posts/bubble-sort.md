---
title: 冒泡排序算法的函数式实现
---

如何实现冒泡排序算法？如何用函数式范式来实现。最重要的不是这个简单算法本身，而在于思考这类问题带来的收获。

## 函数式
在仔细回忆冒泡算法的基本过程之后，我有信心写出一个过程式的命令式的程序：一个双层循环，在内层循环中将较大的数到移到最右边（假如是从小到大）。
但既然正在学习函数式，如何用函数式的视角来解？对于我来说，使用函数式，意味着一种不同的思维，不再通过去修改状态的方式来达成预期结果，而是直接想象一个结果。
通过观察命令式版本的双层循环，我们可以发现，它是为了构造一个循环（废话）：生成一个`j`的序列，然后用这个`j`去遍历数组，更改数组。也就是说，循环在这里的作用有两个：生成`j`序列，原地更改数组。    

我们看如何生成`j`序列。

## 生成遍历索引序列
```clojure
(defn bubble-seq [n]
  (for [i (range (dec n))
        j (range (- n i 1))]
    j))
```
这里`n`代表要排序的序列或数组的长度。我们测试一下
```clojure
(def xs (range 7 0 -1)) ; 通过range生成一个测试数组
xs ; => (7 6 5 4 3 2 1)
(bubble-seq xs) ; => (0 1 2 3 4 5 0 1 2 3 4 0 1 2 3 0 1 2 0 1 0)
```
这样我们便得到了一个序列，这个序列是我们将用来比较交换数组的索引组成的序列。

## 交换数组中两个数
```clojure
(defn swap-elem [xs j k]
  (-> xs
      (assoc j (xs k))
      (assoc k (xs j))))
```
很简单，有意思而且值得注意的是，这里我们没有中间变量，因为在命令式程序中，赋值的过程中会修改数组中的元素，但这里我们没有覆盖原数组，而是会产生新的数组，`xs`始终引用的是原参数数组。


## 最终整合 reduce


```clojure
(defn bubble-sort [xs]
  (reduce (fn [xs j]
            (if (> (xs j) (xs (inc j)))
              (swap-elem xs j (inc j))
              xs))
          xs
          (bubble-seq (count xs))))
```
最后我们用reduce来描述算法的核心过程：一个循环，每次循环我们取`bubble-seq`的每一项，去比较元素大小，并交换，即产生一个新的数组或保持，重复`(count (bubble-seq xs))`次，得到最后的数组。

测试：
```clojure
(bubble-sort []) ; => []
(bubble-sort [1]) ; => [1]
(bubble-sort [3 2 1]) ; => [3 2 1]
(bubble-sort [3 2 8 12 76 1 3 5 69 88]) ; => [1 2 3 3 5 8 12 69 76 88]
```


### 最初的版本
```clojure
(defn bubble-sort [cmp xs]
  (reduce (fn [xs j]
            (if (> (cmp (xs j) (xs (inc j))) 0)
              (-> (assoc xs (inc j) (xs j))
                  (assoc j (xs (inc j))))
              xs))
          xs
          (for [i (range (dec (count xs)))
                j (range (- (count xs) i 1))]
            j)))
```
这实际上不是最终版本，而是最初版本，并且增加了参数`cmp`。测试：
```clojure
(bubble-sort - [3 2 8 12 76 1 3 5 690 88]) ; => [1 2 3 3 5 8 12 76 88 690]
```
