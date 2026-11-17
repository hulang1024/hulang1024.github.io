---
title: 我为什么使用Clojure
---
它是**Lisp**。Clojure是一种实用的Lisp方言，对于已经用过Scheme，Racket等Lisp的人来说，它是一个可以尝试的选项。

Clojure基于**JVM**，Clojure的兄弟-ClojureScript，则可以编译为**JavaScript**。两者分别利用Java和JavaScript**生态**，不用担心其语言小众生态也小，你可以调用很多库，利用很多工具。两者(以后简称clj)语法相同，对于熟悉Java和JavaScript的人来说，它值得去尝试。

**函数式**语言。clj与Java和JavaScript等语言不同的是，它是一个函数式语言，因为它的默认选项是：数据默认不可变、有一个完善的函数式核心标准库等。强迫你在这个框架下思考问题与解决问题，因此它是一个训练场，通过它学习到真正的函数式范式。在这之前，我仅仅把Scheme和Racket当成有着简洁语法的Lisp语言，去编写过程式和面向对象结构的程序。

**实用**主义。与Haskell相比，它不太纯粹，为了实用性而妥协，实用性也是我选择它的原因之一。

**语法简洁**。跟所有Lisp方言一样，它的语法特别简洁，写出的程序噪音特别少，clj学习起来也尤其简单。

**丰富的数据集合类型**。clj有着丰富的数据集合，而不用重复发明，减少了社区方案分裂带来的麻烦。它有：list、vector、set、map等。与scheme不同，clj多了vector、set和map，它们通常用来表示应用程序数据模型，list则通常只用来表示语法结构，来供宏来操作。

还有其他好处，例如为并发设计。更多信息推荐你到官网中了解。
clj官网：https://clojure.org/about/rationale
