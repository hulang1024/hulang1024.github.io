---
title: 在clojure中用函数式方法计算1+2+..10
---
1+2+..10代表了一类问题。在面向过程语言中，通常这样来计算1+2+..10：
```c
// for
int acc = 0;
for (int i = 1; i <= 10; i++) {
    acc += i;
}
// while
int acc = 0;
int i = 1;
while (i <= 10) {
    acc += i;
    i += 1;
}
```
执行流程是，首先执行初始化`acc = 0, i = 1`，然后检查迭代/循环结束条件`i <= 10`，如果没有结束，每次循环都会执行不变式`acc += i; i += 1`，直到`i > 10`。
在每次循环中，我们用`i+1`和`acc+i`计算出一个新值再通过赋值去修改`i`和`acc`。
这里的`i`和`acc`就像在草稿纸上的两个数，我们先拿到纸上“当前”`acc`和`i`的值，然后计算新的值，再擦除旧值，把新的数写上去。


## 递归
```clojure
(defn sum [acc i]
  (println `(sum ~acc ~i))
  (if (<= i 10)
    (recur (+ acc i) (inc i))
    acc))
(sum 0 1)
```
我们定义了一个函数`sum`，可以被复用（在这里是被自身调用），在函数体中，`recur`关键字等同于`sum`（就像对象方法中的`this`）。
同样，我们定义了一个不变式`(if ...)`，在`(<= i 10)`为真时，则执行调用`sum`，于是形成了循环的效果。如果`i`大于`10`，将直接返回`acc`的值并不再调用`sum`，则循环结束。
初始化的地方是在第一次调用`(sum 0 1)`，它让`acc=0,i=1`。在每次迭代中，我们计算出新的`acc`和`i`的值，但这里我们这里没有去“修改”变量的值。
而是就像第一次调用`sum`那样，进行一次新的调用：`(sum 1 2)`，来初始化一次局部变量`acc`和`i`，后续不会修改它们。
对于上面描述的这个过程，可以想象有10张纸，我们不会擦除每一张纸上的`acc`和`i`旁边写着的数，而是在新的纸上写下新的`acc`和`i`的数。（实际上，尾递归优化会复用旧的纸，但不妨碍我们进行那样的推理）
注意到sum函数中有一行``(println `(sum ~acc ~i))``，`(sum 0 1)`执行结果如下：
```clojure
(sum 0  1)
(sum 1  2)
(sum 3  3)
(sum 6  4)
(sum 10 5)
(sum 15 6)
(sum 21 7)
(sum 28 8)
(sum 36 9)
(sum 45 10)
(sum 55 11)
55
```

对于这类迭代计算过程，在SICP课程中，是用一个叫`add`的函数来解释的，在计算的方式上，它实际上与我们的`sum`做的事情是相同的：
```clojure
(defn add [x y]
 (println `(add ~x ~y))
  (if (= x 0)
    y
    (recur (dec x) (inc y))))
(add 3 4)
```
<img src="{% asset_path sicp-iteration.png %}" width="50%" title="迭代">

`(add 3 4)`执行结果如下：
```clojure
(add 3 4)
(add 2 5)
(add 1 6)
(add 0 7)
7
```

## loop
```clojure
(loop [acc 0 i 1]
 (if (<= i 10)
  (recur (+ acc i) (inc i))
  acc))
```
loop 是上面的`sum`的简化写法。`(sum 0 1)`和定义参数放在一起写变成了`[acc 0 i 1]`。


## loop 集合
```clojure
(loop [acc 0 i (list 1 2 3 4 5 6 7 8 9 10)]
 (if (not (empty? i))
  (recur (+ acc (first i)) (rest i))
  acc))
```
我们这里只是将`i`的历史值序列替换成了集合。


## reduce
```clojure
(reduce + (range 1 11))
```
就对于本问题的类似问题而言，不需要写模板代码，而是通过调用库函数`reduce`，`reduce`抽象了`loop`、遍历集合，初始化、不变式。需要填空reduce参数即可，只需要极少的代码。

至此为止，我们消除了`for`、`while`、变量`acc`和`i`，和对它们的*mutation*。
这样做的好处是，避免关注繁多细节，从而避免犯错的机会。
