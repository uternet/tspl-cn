##第二章. 入门

这一章是为刚刚接触 Scheme 的程序员准备的，建议你运行 Scheme 解释器，边看书边试验书中的代码，这样会学到更多。

读完这一章，并且完成了练习之后，你就可以开始使用 Scheme，你将学到 Scheme 的语法以及它们怎么样运行，还有如何使用简单的数据结构及控制机制。

###2.1.同 Scheme 交互

大多数 Scheme 系统提供了一个交互式的编程环境，极大地简化了程序开发与试验。一个最简单的交互环境叫做 "read-evaluate-print-loop"，简称 repl。它读取你输入的每一个表达式，并对它求值，然后返回结果。

在一个交互式 Scheme 系统里，你可以从键盘输入一个表达式，并且立刻看到它的结果。你可以定义一个函数，将它应用到具体的参数上，观察它如何工作。你甚至可以输入包含着多个函数定义的一整个程序，并且直接测试它。当你的程序变得越来越长，你可以将它输入一个文件当中，然后在 Scheme 里载入它，然后就可以交互地测试它。在大多数 Scheme 系统里，可以通过非标准的 `load` 过程来载入一个文件，它接受一个字符串形式的文件名作为参数。将你的程序写进一个文件有几个好处：你有机会更仔细地编写程序，你可以改正错误而不需要重新输入整个程序，你可以保存以一个拷贝以便将来使用。大多数 Scheme 实现将载入保存在文件中的表达式视为与你从键盘输入等价。

另外，Scheme 提供了各种输入输出过程，REPL 接手了怎么读取表达式以及怎么输出值的工作，这使得可以将精力集中在怎么写程序上，而不用为怎么显示结果操心。

本书中的示例代码遵循一个简单的规则，首先给出你可能需要从键盘输入的表达式，也许会跨越多行。而返回值的表达式跟随在符号“ => ”后面，读作“求值为”。当输入一个定义(define)，或者表达式的值为未定义时省略 "=>" 符号。

示例代码按照一种“好看”的格式书写，代码容易阅读，因为每一个表达式及子表达式的关系清晰可见。没有哪一种风格是标准的，重要的是建立一种风格，并一直使用它。

如果你可以访问一个交互式 Scheme 系统，现在打开它并输入你读到的代码会是个不错的主意。一个最简单的 Scheme 表达式是一个字符串常量。尝试在提示符后面输入 `"Hi Mom!"` (包括双引号在内)，然后敲回车，系统会回应同样的 "Hi Mom!"; 一个常量的值就是它自己。

```
"Hi Mom!" => "Hi Mom!"
```

下面是一个表达式集合，以及它们的返回值。本章后面的部分会解释它们，不过，现在请输入它们，练习与 Scheme 交互。

```
"hello" => "hello"
42 => 42
22/7 => 22/7
3.141592653 => 3.141592653
+ => #<procedure>
(+ 76 31) => 107
(* -12 10) => -120
'(a b c d) => (a b c d)
```

小心不要丢掉任何单引号('), 双引号，或者括号。

这里是更多可以尝试的表达式，你可以尝试自己揣摩它们的意思，或者看完本章你就知道了。

```
(car '(a b c)) => a
(cdr '(a b c)) => (b c)
(cons 'a '(b c)) => (a b c)
(cons (car '(a b c))
      (cdr '(d e f))) => (a e f)
```

如你所见，Scheme 表达式可以跨越多行。Scheme 系统可以识别出没有闭合的双引号，括号。

下面，我们尝试定义一个函数

```
(define square
  (lambda (n)
    (* n n)))
```

这个函数计算 `n` 的平方。`define` 建立一个新的变量绑定，`lambda`创建了一个函数，`*`是乘法函数的名字。注意这些表达式的形式。所有结构形式包含在括号里面，并且写作“前缀”形式，即，操作符写在参数前面。如你所见，这是真实的，即使是最简单的算术运算，比如 *.

尝试使用刚刚定义的 `square` 函数

```
(square 5) => 25
(square -200) => 40000
(square 0.5) => 0.25
(square -1/2) => 1/4
```

尽管下面的定义很短，你也可以将它输入一个文件，假设文件名叫做 "reciprocal.ss"

```
(define reciprocal
  (lambda (n)
    (if (= n 0)
        "oops!"
        (/ 1 n))))
```

`reciprocal` 函数计算 `n` 的倒数，返回 `1/n`，如果 n == 0,则返回一个字符串 "oops!"

现在，回到 Scheme 中，尝试载入刚才的文件

(load "reciprocal.ss")

最后，尝试使用我们刚刚定义的函数

```
(reciprocal 10) => 1/10
(reciprocal 1/10) => 10
(reciprocal 0) => "oops!"
(reciprocal (reciprocal 1/10)) => 1/10
```

###2.2.简单的表达式

最简单的 Scheme 表达式是常量对象，比如字符串，数字，符号及列表。Scheme 还支持别的对象类型，不过对大多数程序，这四种就足够了。

数字是常量，如果你输入了一个数字，Scheme 将把它显示回来给你。下面的例子显示了 Scheme 支持的几种数字类型

```
123456789987654321 => 123456789987654321
3/4 => 3/4
2.718281828 => 2.718281828
2.2+1.1i => 2.2+1.1i
```

Scheme 中的数字包括了精确或非精确的整数，有理数，实数及复数。精确的整数及有理数具有任意的精度。它们可以是任意大小。非精确数通表示浮点数，遵循 IEEE 的浮点数标准。

Scheme 提供了 + - * / 等算术运算过程。每一个过程接受两个数字参数（实际上可以接受多个参数）。下面的表达式我们称之为“过程应用”(函数调用).

```
(+ 1/2 1/2) => 1
(- 1.5 1/2) => 1.0
(* 3 1/2) => 3/2
(/ 1.5 3/4) => 2.0
```

Scheme 使用前缀表示法，甚至包括最常用的算术操作符在内，统统使用前缀表达式。任何的函数调用，不论函数接受0个，1个，2个，或者更多个参数，写为`(procedure arg ...)`。

函数调用可以嵌套，在这种情况下，内部的表达式先求值。我们可以通过嵌套来组合成更复杂的算术运算

```
(+ (+ 2 2) (+ 2 2)) => 8
(- 2 (* 4 1/3)) => 2/3
(* 2 (* 2 (* 2 (* 2 2)))) => 32
(/ (* 6/7 7/2) (- 4.5 1.5)) => 1.0
```

这些例子展示了你可以将 Scheme 用作四则运算的计算器。但是我们在这里不会讨论这些。Scheme 支持相当多的算术函数。现在你可以跳转到 6.4 节去一一尝试它们。

对许多任务而方，简单的数字对象就够用了，但是有时候，需要将多个值作为一个独立的对象还操作。在大多数其它语言里，最基本的数据结构是数组。在 Scheme 里，使用最多的是列表 (list)。列表是由括号包围起来的对象序列，例如， `(1 2 3 4 5)` 是一个数字的列表， `("this" "is" "a" "list")`是一个字符串列表。列表里可以包含不同类型的对象， `(4.2 "hi")`; 列表还可以嵌套 `((1 2) (3 4))`.

你可能注意到了，列表看起来很象函数调用，你可能会奇怪：Scheme 怎么区别它们？

在一些情况下，区别很明显。以 `(1 2 3 4 5)` 为例，开头的是数字 1, 1 显然不可能是一个函数。所以，可能的回答是 Scheme 查看列表的第一个元素，判断它是不是一个函数。这个回答并不够好,我们甚至可以将一个函数调用处理成一个普通的列表，比如 `(+ 3 4)`. 正确的回答是，我们必须明确地告诉 Scheme 将一个列表视为数据，而不是函数调用。我们可以使用“引用”(quote) 来做到这个点。

```
(quote (1 2 3 4 5)) => (1 2 3 4 5)
(quote ("this" "is" "a" "list")) => ("this" "is" "a" "list")
(quote (+ 3 4)) => (+ 3 4)
```

`quote` 强制地将列表视为数据，你可以尝试去掉上面的表达式中的 quote。任何没有`quote` 的列表会被视为函数调用，Scheme 会尝试对它求值。

因为`quote`很常用，Scheme 提供了另外一种简短的缩写，就是单引号 (')，

```
'(1 2 3 4) => (1 2 3 4)
'((1 2) (3 4)) => ((1 2) (3 4))
'(/ (* 2 -1) 3) => (/ (* 2 -1) 3)
```

两种写法都是合法的。

`quote` 表达式并非函数调用，它会阻止对它的子表达式进行求值。这是一种与函数调用完全不同的语法形式。除了函数调用及`quote`，Scheme 还支持另外几种语法形式。每一种语法形式的求值规则都不一样。幸运的是，这些特殊的语法形式并不多，在本章的后面部分将会看到更多的介绍。

并不是只能 `quote` 列表，尝试下面的表达式，然后去掉 `quote` ，试试直接输入`hello`会发生什么：

```
(quote hello) => hello
```

符号`hello`必须被引用起来，不然 Scheme 会认为它是个变量，并尝试对它求值（读取变量的值）。Scheme 的符号和变量与数学表达式里的符号和变量相同或类似。当我们计算数学表达式`1 - x`的时候，我们认为`x`是一个变量。当我们思考代数方程式 x^2 - 1 = (x - 1) (x + 1) ,我们认为 `x` 是一个符号。`quote`一个列表，相当于告诉 Scheme 不要把它当作函数调用。类似地，`quote` 一个符号，相当于告诉 Scheme 不要把它当作变量。符号 (symbol)在程序或方程式中通常就是用来表示变量。

你可能会觉得奇怪，为什么要使用相同的方式来表示列表与函数调用，以及变量与符号。相同的表示法允许将 Scheme 代码表示为数据，可以简化解释器、编译器、编辑器以及其它工具的编写。12.7节里用 Scheme本身写成的 Scheme 解释器将会展示这个特性。很多人认为这是 Scheme 最重要的特性。

数字和字符串也可以引用

```
'2 => 2
'2/3 => 2/3
(quote "Hi Mom!") => "Hi Mom!"
```

任何情况下，数字和字符串都被视为常量，所以没有必要引用它们。当然，引用了也不会有什么作用。

现在，我们来学习几个 Scheme 中用来操作列表的过程。有两个过程分别用来获取列表的不同部分：`car`及`cdr`。`car`返回列表的第一个元素，而`cdr`返回列表中剩下的所有元素。（car 及 cdr 的名称起源自第一台在硬件上实现 Lisp 语言的机器，IBM 704）。它们都需要一个非空列表作为参数。

```
(car '(a b c)) => a
(cdr '(a b c)) => (b c)
(cdr '(a)) => (a)

(car (cdr '(a b c))) => b
(cdr (cdr '(a b c))) => (c)

(car '((a b) (c d))) => (a b)
(cdr '((a b) (c d))) => ((c d))
```

列表的第一个元素通常被称作列表的 "car", 列表中除了第一个元素外剩下的部分通常被称为列表的 "cdr"。只有一个元素的列表，其 cdr 是空列表 `()`。

`cons`过程用来构造列表，它接受两个参数，第二个参数通常是一个列表。如果第二个参数是一个列表，那么 cons 也返回一个列表，它将第一个参数添加到第二个参数的头部，形成一个新的列表。

```
(cons 'a '()) => (a)
(cons 'a '(b c)) => (a b c)
(cons 'a (cons 'b (cons 'c '()))) => (a b c)
(cons '(a b) '(c d)) => ((a b) c d)

(car (cons 'a '(b c))) => a
(cdr (cons 'a '(b c))) => (b c)
(cons (car '(a b c))
      (cdr '(d e f))) => (a e f)
(cons (car '(a b c))
      (cdr '(a b c))) => (a b c)
```

"car" 及 "cdr" 通常被用于名词，而 "cons" 通常被用作动词。将一个元素添加到一个列表的开头，形成一个新的列表，称作：将某个元素 cons 到列表里。

上面提到，`cons`的第二个参数“通常”是一个列表，请注意这个“通常”。过程`cons`事实上用来构建一个“对偶”(pair)，一个对偶的 cdr 部分不一定必须是一个列表，列表事实上是一个对偶（节点）的序列，序列中，每一个对偶的 cdr 部分实际上是指向序列中的下一个对偶的指针。

![List](img/1.gif)

学过数据结构的童子应该看出来了，Scheme 的列表本质上就是链表。最后一个对偶的 cdr 指向一个空列表（相当于null）。如果最后一个 cdr 不是空列表，那么这就不是一个合法的列表。空列表是一个合法的列表。任何列表，只要它的 cdr 部分是一个合法的列表，那么它就是一个合法的列表。

一个不合法的列表用点对来表示，元素之间用一个句点来隔开：

```
(cons 'a 'b) => (a . b)
(cdr '(a . b)) => b
(cons 'a '(b . c)) => (a b . c)
```

因为这种表示法，一个对偶经常被称作“点对”。甚至于，一个合法的列表也可以用点对来表示

```
'(a . (b . (c . ()))) => (a b c)
```

`list`过程类似于`cons`，只不过它接受任意数量的参数，并且总是返回一个列表

```
(list 'a 'b 'c) => (a b c)
(list 'a) => (a)
(list) => ()
```

6.3节提供了更多关于列表及操作列表的过程的信息，现在可以跳转过去，自己先行熟悉。

### 2.3.Scheme 表达式求值。

我们已经知道了常量对象的求值规则，比如数字和字符串。常量的值就是它自己。你可能自己脑补了函数调用的求值规则。(procedure arg<sub>1</sub> ... arg<sub>n</sub>)，这儿，`procedure`是一个表达式，用来表示一个 Scheme 过程（函数），*arg<sub>1</sub> ... arg<sub>n</sub>* 同样是表达式，用来表示参数。一个可能的求值规则是：

* 找到 `procedure` 的值
* 找到 *arg<sub>1</sub>*的值
* ......
* 找到 *arg<sub>n</sub>*的值
* 将 `procedure` 的值应用到 *arg<sub>1</sub> ... arg<sub>n</sub>* 的值上面

思考简单的函数调用`(+ 3 4)`，`+`的值是一个加法函数，3 的值就是 3，4 的值就是 4, 将 3 和 4 作为参数传递给函数 `+` 得到返回值 7, 所以 `(+ 3 4)`的个表达式的值就是 7 这个对象。

重复这个过程，我们就可以得到嵌套的表达式 `(* (+ 3 4) 2)` 的值。`*`的值是一个乘法函数，`(+ 3 4)`的值上面已经得到了 7, 2 的值是2. 将乘法函数应用于 7 和 2，我们得到最终的值是 14.

这个求值规则对函数调用有效，对`quote`表达式无效，因为`quote`不对它的子表达式求值。对`quote`表达式的求值类似于对常量的求值。

常量对象（字面量），过程（函数）以及引用（quote）是 Scheme 提供的众多语法形式中的三种。幸运的是，只有很少一部分其它语法形式需要 Scheme 直接理解，这些被称为“核心”语法形式，剩下的那些是“扩展”的语法定义。在本章的剩下的内容里，我们将要介绍剩下的那部分核心语法，以及少量的扩展语法。3.1节概括了核心语法并且介绍了语法扩展的机制。

在我们学习更多的语法及函数之前，关于函数调用的求值有两点值得注意。首先，上面给出的求值顺序是特定的，函数调用的子表达式从左到右依次求值，也就是 *procedure* 在 *arg<sub>1</sub>* 之前求值，而 *arg<sub>1</sub>* 在 *arg<sub>2</sub>* 之前求值。其实这不是强制的。一个 Scheme 求值器可以自由决定求值的顺序，从左到右或者从右到左，或者以别的任何顺序进行求值。甚至在同一个 Scheme 实现里，也会对不同的过程使用不同的求值顺序。

第二点要注意的是，`procedure` 采用与 *arg<sub>1</sub> ... arg<sub>n</sub>* 相同的方式求值。`procedure` 是一个变量名，用来命名一个过程。然而处在 `procedure`位置上的不一定必须是一个变量，也可以是另外一个表达式。在练习 2.2.3 里，要求你判断这个表达式的值`((car (list + - * /)) 2 3)`，在这里，作为函数被调用的并不是某个被命名的函数，而是这个表达式`(car (list + - * /))`，它的值就是加法函数`+`。

#### 练习 2.3.1

写出下面的表达式的求值步骤

    ((car (cdr (list + - * /))) 17 5)

###2.4.变量绑定及 let 表达式

假设`expr`是一个表达式，其中包含一个变量`var`，进一步，假设当我们对表达式`expr`求值的时候，`var`的值是`val`。比如，当我们计算 `(+ x 3)` 的时候，我们希望 `x` 的值为2, 或者，当我们计算 `(+ 2 y)` 的时候，希望 `y` 的值为 3. 下面的示例展示了如何利用 Scheme 的 `let` 语法形式做到这一点。

```
(let ((x 2))
  (+ x 3)) => 5

(let ((y 3))
  (+ 2 y)) => 5

(let ((x 2) (y 3))
  (+ x y)) => 5
```

`let` 语法结构包含一个由“变量-表达式”对构成的列表，后面跟着一系列的表达式，我们称之为 `let` 的 `body`。通用的 `let` 语法结构如下：

    (let ((var expr) ...) body1 body2 ...)

我们称之为，变量被 `let` 绑定到了值上. We refer to variables bound by let as let-bound variables.

`let` 表达式经常用来简化包含两个相同子表达式的表达式。这样也保证了公共的子表达式只被计算一次。

```
(+ (* 4 4) (* 4 4)) => 32

(let ((a (* 4 4))) (+ a a)) => 32
```

经常用方括号代替括号来分隔 `let` 表达式之中的多个绑定

```
(let ([list1 '(a b c)] [list2 '(d e f)])
  (cons (cons (car list1)
              (car list2))
        (cons (car (cdr list1))
              (car (cdr list2))))) => ((a . d) b . e)
```

Scheme 将方括号视为与圆括号相同，唯一的要求是它们必须配对。以方括号开始的列表必须以方括号闭合。我们会将方括号使用在`let`及其它几个语法结构中，以改善可读性。

Since expressions in the first position of a procedure application are evaluated no differently from other expressions, a let-bound variable may be used there as well.

```
(let ([f +])
  (f 2 3)) => 5

(let ([f +] [x 2])
  (f x 3)) => 5

(let ([f +] [x 2] [y 3])
  (f x y)) => 5
```

由 `let` 绑定的变量仅仅在 *body* 部分可见

```
(let ([+ *])
  (+ 2 3)) => 6

(+ 2 3) => 5
```

这是幸运的，我们可不想在全局范围内将加法过程 `+` 给想修改成乘法 `*`

`let`表达式是可以嵌套的

```
(let ([a 4] [b 3])
  (let ([a-squared (* a a)]
        [b-squared (* b b)])
    (+ a-squared b-squared))) => 25
```

当我们在嵌套的`let`表达式里对相同的变更重复绑定，内在层的*body*里，只有内层的绑定是可见的。

```
(let ([x 1])
  (let ([x (+ x 1)])
    (+ x x))) => 4
```

在外层的 let 表达式的 *body* 里，x 的值是 1, 而它的*body*，就是第二个let表达式。而第二个`let`表达式又将 x 绑定为`(+ x 1)`，于是，在内层的 `let` 表达式的*body*里，x 的值现在是 2。内层`let`的*body*只有一个表达式，就是 `(+ x x)`，于是我们得到 4 这个结果。

我们说，内层的 `x` 遮蔽了外层的绑定，一个`let`绑定，在它的整个 *body* 里是完全可见的，除非它被遮蔽起来。变量可见的范围称作它的作用域，在上面的代码里，第一个 x 的作用域是外层 let 的 body，它的 body 即内层的 let 表达式。在内层的 let 表达式里, 第一个 x 被第二个 x 给遮蔽了。这种作用域形式叫做“词法作用域”。每一个绑定的作用域可以从程序的文本直接了当地分析出来。

多说一句，与之对应的是“动态作用域”，早期的 Lisp 语言与现在的 Emacs Lisp 采用的就是动态作用域。

通过使用不同的变量名，可以避免遮蔽的发生。上面的表达式可以将内部的 x 重写为 `new-x`

```
(let ([x 1])
  (let ([new-x (+ x 1)])
    (+ new-x new-x))) => 4
```

虽然使用不同的变量可以避免混淆，然而，遮蔽则可以避免意外地使用“旧”的值。

#### 练习 2.4.1
重写下面的表达式，使用 let 去除共同的子表达式，以改善代码结构。注意，不要使用任何代数简化。

a. `(+ (- (* 3 a) b) (+ (* 3 a) b))`

b. `(cons (car (list a b c)) (cdr (list a b c)))`

#### 练习 2.4.2
判断下面的表达式的值，解释你是怎么推导出值的

```
(let ([x 9])
  (* x
     (let ([x (/ x 3)])
       (+ x x))))
```

#### 练习 2.4.3
重写下面的表达式，给每个不同的 let 绑定变量不同的名字，使得没有变量被遮蔽，确认修改后的表达式的值与原来的表达式相同。

a.
```
(let ([x 'a] [y 'b])
  (list (let ([x 'c]) (cons x y))
        (let ([y 'd]) (cons x y))))
```

b.
```
(let ([x '((a b) c)])
  (cons (let ([x (cdr x)])
          (car x))
        (let ([x (car x)])
          (cons (let ([x (cdr x)])
                  (car x))
                (cons (let ([x (car x)])
                        x)
                      (cdr x))))))
```


###2.5.Lambda 表达式

在表达式 `(let ((x (* 3 4))) (+ x x))` 里，变量 x 绑定到值 `(* 3 4)`。如果我们喜欢，我们可以把它绑定为 `(/ 99 11)`,也可以绑定为`(- 2 7)`？在每一种情况下，我们都需要一个猜有不同的单独的 let 表达式，这很不方便。

我们可以使用 `lambda` 语法形式来创建一个函数，它有一个参数 `x` ，并且 body 部分与 let 表达式相同。

    (lambda (x) (+ x x)) => #<procedure>

lambda 表达式的通用形式是

    (lambda (var ...) body1 body2 ...)

变量 `var ...` 是一个函数的形式参数，而`body1 body2...`为函数体。

函数是与数字、字符串、符号或者 pair 一样的对象。它没有任何有意义的打印表示法，所以，本书使用`#<procedure>`来表示一个函数的值。

使用一个函数最常见的方式是将它应用到一个或多个值上。

    ((lambda (x) (+ x x)) (* 3 4)) => 24

这与普通的函数调用没有任何区别。函数是表达式`(lambda (x) (+ x x))`的值，唯一的参数是`(* 3 4)`，即12.与 let 绑定的方式相同，参数值(又叫实参)被绑定到 lambda 表达式里的形式参数上。在这种情况下，`x`绑定到12,`(+ x x)`的值是24。因此，将值 12 应用到函数的结果就是 24.

因为函数是一个对象，我们可以将函数绑定为一个变量，并且多次使用它。

```
(let ((double (lambda (x) (+ x x))))
   (list (double (* 3 4))
         (double (/ 99 11))
         (double (- 2 7))))
=> (24 18 -10)
```

这里，我们将 double 绑定为一个函数，然后使用这个函数应用到3个不同的值上面。

该函数预期它的实参是一个数字，然后将该实参再传递给`+`。大多数情况下，实参可以是任意的对象。比如，有一个类似的函数，使用 `cons` 来代替 `+`

```
(let ((double-cons (lambda (x) (cons x x))))
  (double-cons 'a)) => (a . a)
```

请注意到 `double` 与 `double-cons` 之间的相似性，通过增加一个参数，可以将这两个函数合二为一，你不应该对此感到惊讶。

```
(let ((double-any (lambda (f x) (f x x))))
  (list (double-any + 13)
        (double-any cons 'a))) => (26 (a . a))
```

这里演示了一个函数可以接受一个以上的参数，而传递给函数的参数本身也可以是一个函数。

当一个 `lambda` 表达式嵌套在另一个 `lambda` 或者 `let` 表达式中，情况变得有趣起来。

```
(let ((x 'a))
  (let ((f (lambda (y) (list x y))))
    (f 'b))) => (a b)
```

出现在 `lambda` 表达式内部的 `x` 引用了由外部的 `let` 表达式绑定的变量 `x`。对 `lambda` 表达式而言，变量`x`被称作自由变量. 变量`y`并非自由变量，它被绑定在 `lambda` 内部. A variable that occurs free in a lambda expression should be bound, e.g., by an enclosing lambda or let expression, unless the variable is (like the names of primitive procedures) bound outside of the expression, as we discuss in the following section.

当一个函数应用到绑定作用域之外的某个变量会发生什么？比如下面的表达式：

```
(let ((f (let ((x 'sam))
           (lambda (y z) (list x y z)))))
  (f 'i 'am)) => (sam i am)
```

答案是， the same bindings that were in effect when the procedure was created are in effect again when the procedure is applied. This is true even if another binding for x is visible where the procedure is applied.

```
(let ((f (let ((x 'sam))
           (lambda (y z) (list x y z)))))
  (let ((x 'not-sam))
    (f 'i 'am))) => (sam i am)
```

注:这里演示的是词法闭包。闭包内部的变量 `x` (值为 'sam) 在函数被创建的时候值就确定了，当在另外的环境中使用闭包时，虽然当前环境中有一个同名的 `x`（值为 'not-sam），起作用的依然是闭包内部的`x`。

顺便说一下，`let`表达式只不过是将一个`lambda`直接应用到一组参数上。例如，下面的两个表达式完全等价。

    (let ((x 'a)) (cons x x))  =  ((lambda (x) (cons x x)) 'a)

事实上，`let`表达式只是一个由`lambda`来定义的语法扩展。这两者都是核心语法形式。一般来说，任何下面的表达式：

    (let ((var expr) ...) body_1 body_2)

等价于：

```
((lambda (var ...) body_1 body_2)
  expr ...)
```
如上所述，lambda的一般形式比我们先前看到的形式有点复杂，在正式的参数规范里，（VAR...），不一定是一个固定的列表，甚至连列表成员都可以没有。形式参数可以是下面三种形式中的任意一种：

+ 一个固定的变量列表， (var_1 ... var_n)，就如同我们看到过的
+ 一个单独的变量，var_r 或者是
+ 一个不规范的列表, (var_1 ... var_n . var_r).

第一种情况下，必须精确地传递n个实参，而每一个变量（形参）按照参数传递的顺序绑定到对应的实参上。

第二种情况下，可以传递任意下参数，所有的参数作为一个列表被传递进函数，形参var_r就绑定到这个列表上

第三种情况是第一种和第二种形式的混合，必须传递不少于 n 个实参，传递的实参依次与 var_1 ... var_n 绑定，而多于 n 个的实参，作为一个列表绑定到 var_r 上。

第二三种情况中，var_r 有时被称作 `rest` 参数。

我们用几个小例子来搞清 `lambda` 表达式的常规语法。

```
(let ((f (lambda x x)))
  (f 1 2 3 4)) => (1 2 3 4)

(let ((f (lambda x x)))
  (f)) => ()

(let ((g (lambda (x . y) (list x y))))
  (g 1 2 3 4)) => (1 (2 3 4))

(let ((h (lambda (x y . z) (list x y z))))
  (h 'a 'b 'c 'd)) => (a b (c d))
```

###2.6.顶级定义

由 `let`和`lambda`创建的变量绑定在表达式外部是不可见的。假设想创建一个对象，也许是一个函数，想要在任意地方访问，比如`+`和`cons`。你需要的是通过`define`形式进来一个顶级定义 (top-level definition)。顶级定义被大多数交互式 Scheme 系统所支持，在你输入的任何表达式中是可见的，除非它被另一个绑定所遮蔽。

我们把上面一节中的`double-any`实现为一个顶级定义。

```
(define double-any
  (lambda (f x)
    (f x x)))
```

现在，`double-any`拥有了和`cons`这样的内置函数一样的地位。我们可以使用像调用一个内置函数一样来调用它

```
(double-any + 10) => 20
(double-any cons 'a) => (a . a)
```

一个顶级定义可以使用任意对象来建立，并不仅仅是函数。

顶级定义可以被`let`或`lambda`绑定所遮蔽。

```
(define xyz '(x y z))
(let ((xyz '(z y x)))
  xyz) => (z y x)
```

Variables with top-level definitions act almost as if they were bound by a let expression enclosing all of the expressions you type.

到此为止，你仅仅了解到一些简单的工具，然而，已经可以通过它们来定义一些Scheme的内置函数，后面将要讲到。如果你完成了上一节的练习，你已经知道如何定义`list`函数了

    (define list (lambda x x))

同样，Scheme 提供了缩写的 `cadr`及`cddr`， `(cadr list)` 相当于 `(car (cdr list))`,`(cddr list)`相当于`(cdr (cdr list))`，它们可以很容易地定义出来

```
(define cadr
  (lambda (x)
    (car (cdr x))))

(define cddr
  (lambda (x)
    (cdr (cdr x))))

(cadr '(a b c)) => b
(cddr '(a b c)) => (c)
```

当定义一个函数的时候`(define var expr)`，当`expr`是一个`lambda`表达式，可以使用一种简短的写法来省略掉 `lambda`。

```
(define var0
  (lambda (var1 ... varn)
    e1 e2 ...))

可以缩写为

(define (var0 var1 ... varn)
  e1 e2 ...)


(define var0
  (lambda varr
    e1 e2 ...))

可以缩写为

(define (var0 . varr)
  e1 e2 ...)

还有

(define var0
  (lambda (var1 ... varn . varr)
    e1 e2 ...))

可以缩写为

(define (var0 var1 ... varn . varr)
  e1 e2 ...)
```

上面的 `cadr` 函数及`list`函数可以写为：

```
(define (cadr x)
  (car (cdr x)))

(define (list . x) x)
```

本书并不经常使用这种缩写，虽然它更短一点，但是它往往倾向于掩盖掉函数并不总是与其它语言当中的变量、名字联系在一起这个事实。（完全不知道怎么翻译，大概意思也许是:Scheme的函数与其它语言的函数并不等价，在Scheme中，可以把一个普通的值赋给一个变量，也可以把一个函数赋值给一个变量，函数与普通的对象没有本质的区别）.This syntax is often referred to, somewhat pejoratively, as the "defun" syntax for define, after the defun form provided by Lisp languages in which procedures are more closely tied to their names.

看来，Dybvig 更推崇 Lisp 的 defun 语法。

顶级定义使用得交互地测试一个函数变得简单，因为我们不需要每次都重新输入函数的完整定义。我们来定义一个复杂一点的`double-any`，将一个普通的两个参数的函数转变为一个倍增的单参数函数。

```
(define doubler
  (lambda (f)
    (lambda (x) (f x x))))
```

`doubler`接受一个参数，该参数必须是一个函数。然后 doubler 返回另一个函数，该函数接受两个参数，然后应用到 f 上面。通过`doubler`函数，`double`和`double-cons`函数可以简单地制造出来

```
(define double (doubler +))
(double 13/2) => 13

(define double-cons (doubler cons))
(double-cons 'a) => (a . a)

我们也可以通过 doubler 定义出 double-any:

(define double-any
  (lambda (f x)
    ((doubler f) x)))
```

当你尝试使用一个未通过`define`或者`let && lambda` 绑定的变量时会发生什么？

```
(i-am-not-defined 3)
```

大多数的 Scheme 实现会抛出一个异常，提示变量未绑定。

```
(define proc1
  (lambda (x y)
    (proc2 y x)))
```

上面的定义引用了一个未定义的函数 `proc2`，在定义中引用其它未定义的函数不是一个错误，除非你调用了它。

之后再补上`proc2`的定义就OK了。

```
(define proc2 cons)
(proc1 'a 'b) => (b . a)
```

在定义 proc1 的时候，系统接受你稍后会对 proc2 进行定义的承诺，不会引发任何异常，除非你在定义 proc2 之前就调用了 proc1.

这个特性允许你以任意你喜欢的顺序来定义函数，这使得可以以一种更容易阅读的方式在一个文件中组织代码。

###2.7.条件表达式

到此为止，我们学习了无条件地执行一个给定的任务。假设我们希望写一个函数`abs`求一个数的绝对值，如果参数`x`是负数，返回`-x`,否则就返回`x`。

```
(define abs
  (lambda (n)
    (if (< n 0)
        (- 0 n)
        n)))
(abs 77) => 77
(abs -77) => 77
```

`if`表达式的形式为`(if test consequent alternative)`，如果`test`表达式的结果为真，则对`consequent`表达式求值，如果为假，则对`alternative`表达式求值。

`if`是特殊形式，它并不是一个函数。为什么呢？让我们重新审视第一章中的 `reciprocal`函数。

```
(define reciprocal
  (lambda (n)
    (if (= n 0)
        "oops!"
        (/ 1 n))))
```

在第二个子句中，n 不能是 0, 如果 n 的值为零，在数学上是无意义的。如果 `if` 是一个函数，它的参数（包括`(/ 1 n)`）在选择要执行 consequent 或者 alternative 之前都会被求值。对`(/ 1 0)`进行求值显然是一个错误。与`quote`并不对它唯一的子表达式求值类似，`if`并不对它的所有子表达式求值，所以它不能是一个函数。

`or`语法形式在操作上与`if`类似，常见的`or`表达式为`(or expr ...)`。如果没有子表达式，返回`#f`. 否则，依次对所有子表达式求值。这里有两种情况: a.如果发现其中一个子表达式的结果不是`#f`，立即停止对后面的子表达式求值，返回该子表达式的值作为整个`or`表达式的值; b.如果所有的子表达式的值都是`#f`，则返回最后一个子表达式的值，也就是`#f`。

每一个Scheme对象，在条件表达式中都有隐含的布尔值，只有`#f`被看作是“假”，所有其它对象都被当作`#t`对待。

```
(if #t 'true 'false) => true
(if #f 'true 'false) => false
(if '() 'true 'false) => true
(if 1 'true 'false) => true
(if '(a b c) 'true 'false) => true 

(not #t) => #f
(not "false") => #f
(not #f) => #t 

(or) => #f
(or #f) => #f
(or #f #t) => #t
(or #f 'a #f) => a
```

`and`表达式与`or`表达式类似，但正好相反。如果`and`表达式的所有子表达式不为`#f`，则整个表达式的值就是最后一个子表达式的值，只要其中一个子表达式的值为`#f`则立即停止对后面的子表达式求值，立即返回`#f`。

通过使用`and`, 我们可以定义一个稍微有些不同的`reciprocal`函数。

```
(define reciprocal
  (lambda (n)
    (and (not (= n 0))
         (/ 1 n))))

(reciprocal 3) => 1/3
(reciprocal 0.5) => 2.0
(reciprocal 0) => #f
```

在这个版本里，如果n的值为零则返回`#f`，否则就返回`(/ 1 n)`的值。

`=, <, >, <=, >=`这些函数被称作谓词。谓词是这样一类函数，它回答与它的参数有关的一个问题，并且返回`#t`或者`#f`。大多数谓词的后面跟着一个问号。不过上面列出的数学谓词是例外。当然，并非所有的谓词都需要数字型参数。比如谓词`null?`，当它的参数是空列表`'()`的时候返回 `#t`，其它所有情况下返回`#f`。

```
(null? '()) => #t
(null? 'abc) => #f
(null? '(x y z)) => #f
(null? (cdddr '(x y z))) => #t
```

在 Scheme 里，`cdr`函数不接受空列表做为参数，而在 Common Lisp 里可以这样做, `(cdr '())`的结果仍然为`()`。下面的函数 `lisp-cdr`是对 Lisp 的一个模仿。

```
(define lisp-cdr
  (lambda (x)
    (if (null? x)
        '()
        (cdr x))))

(lisp-cdr '(a b c)) => (b c)
(lisp-cdr '(c)) => ()
(lisp-cdr '()) => ()
```

另一个有用的谓词是`eqv?`，它需要两个参数，如果它们相等返回`#t`, 否则返回`#f`。

```
(eqv? 'a 'a) => #t
(eqv? 'a 'b) => #f
(eqv? #f #f) => #t
(eqv? #t #t) => #t
(eqv? #f #t) => #f
(eqv? 3 3) => #t
(eqv? 3 2) => #f

(let ((x "Hi Mom!"))
  (eqv? x x)) => #t

(let ((x (cons 'a 'b)))
  (eqv? x x)) => #t

(eqv? (cons 'a 'b) (cons 'a 'b)) => #f
```

如你所见，对于符号，布尔值和数字，`eqv?`比较它们是否相等，而对于 pair 对象，`eqv?`比较它们是否是同一个对象。分别调用两次`cons`产生看起来一起的 pair，但它们仍然不是同一个对象，所以结果为 `#f`。

Scheme 提供了一组类型谓词来判断某个对象是否是指定的类型：`pair?, symbol?, number?, string?`等等。

类型谓词用于判断传递给某个函数的参数是否是正确的类型。

```
(define reciprocal
  (lambda (n)
    (if (and (number? n) (not (= n 0)))
        (/ 1 n)
        "oops!")))

(reciprocal 2/3) => 3/2
(reciprocal 'a) => "oops!"
```

顺便提一下，调用 `reciprocal`的函数同样不得不判断它的返回值是一个整数还是字符串，为了减轻调用者的任务，更好的办法通常是报告一个错误，使用`assertion-violation`

```
(define reciprocal
  (lambda (n)
    (if (and (number? n) (not (= n 0)))
        (/ 1 n)
        (assertion-violation 'reciprocal
          "improper argument"
          n)))) 

(reciprocal .25) => 4.0
(reciprocal 0) => exception in reciprocal: improper argument 0
(reciprocal 'a) => exception in reciprocal: improper argument a
```

`assertion-violation`的第一个参数是个符号，用于标识引发错误的源头，第二个参数是一个字符串，用于说明错误， the third and subsequent arguments are "irritants" to be included with the error message.

让我们着眼于另一个多分支条件表达式`cond`。

```
(define sign
  (lambda (n)
    (cond
      [(< n 0) -1]
      [(> n 0) +1]
      [else 0])))
```

在`sign`函数中，无需考虑各分支的先后顺序，对于参数n，在三个测试条件中只有唯一的一个为为真。下面的程序根据收入来计算税金。分支条件的顺序就很重要了：

```
(define income-tax
  (lambda (income)
    (cond
     ((<= income 10000) (* income .05))
     ((<= income 20000) (+ (* (- income 10000) .08) 500.00))
     ((<= income 30000) (+ (* (- income 20000) .13) 1300.00))
     (else (+ (* (- income 30000) .21) 2600.00)))))

(income-tax 5000) => 250.0
(income-tax 15000) => 900.0
(income-tax 25000) => 1950.0
(income-tax 50000) => 6800.0
```

###2.8.简单的递归

我们已经知道怎么写顺序及分支结构的程序，而且也知道怎么把代码定义为一个函数进行多次调用。如果我们想重复执行某些表达式要怎么做？我们可以用递归来实现。递归是一个简单的观念：在一个函数内部调用函数自己。初次接触的人可以会对递归产生迷惑，但是一旦掌握了递归，它将提供远超普通循环结构的强大表达能力。

一个递归函数就是在函数内部自己调用自己。下面的`goodbye`函数也许是最简单的递归函数了。

```
(define goodbye
  (lambda ()
    (goodbye)))

(goodbye) =>
```

这个函数没有返回值，而且不会结束。

很明显，要使递归函数有用，我们必须有某种办法来让它终止，大多数递归函数至少有两个基本元素：基本情况，以及递归步进。基本情况是递归结束的条件, giving the value of the procedure for some base argument. The recursion step gives the value in terms of the value of the procedure applied to a different argument. 为了使递归正常终止, 变化的参数必须以某种方式收敛到基本情况.

我们来思考怎么通过递归来求一个列表的长度。我们需要一个基本情况及递归步骤。在列表上递归的结束条件几乎都是列表为空。

```
(define length
  (lambda (ls)
    (if (null? ls)
        0
        (+ (length (cdr ls)) 1))))

(length '()) => 0
(length '(a)) => 1
(length '(a b)) => 2
```

`if`表达式询问列表是否为空列表，如果是，则值为0，这是基本情况；如果不是，则长度就是列表的`cdr`部分加上1. 这就是递归步进。

很多 Scheme 实现允许你跟踪一个函数的执行函数，看看它是怎么操作的。在 ChezScheme 里，一种跟踪函数的方式是输入`(trace name)`，其中，name 是定义在 top-level 中的函数。如果你跟踪上面所定义的`length`函数，并且传递给它一个参数`'(a b c d)`，你将会看到像下面的东西：

```
|(length (a b c d))
| (length (b c d))
| |(length (c d))
| | (length (d))
| | |(length ())
| | |0
| | 1
| |2
| 3
|4
```

缩进显示了递归的嵌套级别；在视觉上，坚线将函数应用与它们的返回值联系在一起。注意，每一次对`length`的调用，参数变得越来越小，最后变成空列表，空列表返回0,而外层的每一次调用都给内层的返回值加上1,最后得到结果为4。

我们来写一个`list-copy`函数，它的参数必须是一个列表，而且返回这个列表的拷贝。新列表里包含原列表中的所有元素。当需通过`set-car!`或者`set-cdr!`修改初始列表或者拷贝的时候，拷贝列表是有用的。我们后面会讲到。

```
(list-copy '()) => ()
(list-copy '(a b c)) => (a b c)

试试看在学习下面的内容之前你能不能把 list-copy 给定义出来

(define list-copy
  (lambda (ls)
    (if (null? ls)
        '()
        (cons (car ls)
              (list-copy (cdr ls))))))
```


`list-copy`的定义与`length`比较相似，它们结束的条件是相同的`(null? ls)`，在`list-copy`里，基本情况的值是`()`而不是0，因为我们在制造一个列表，而不是一个数字。递归调用的函数也是一样的，只不过把 `+1` 改成了 `cons`。`list-copy`每一次都把当前列表的`car`给`cons`到递归调用的返回值里去。

没有什么理由限制只能有一个递归出口（基本情况）。下面的 `memv` 函数检测列表 ls 中是否含有元素 x，如果含有，则返回以第一个 x 开头的子列表，如果没有，则返回 `#f`. `memv` 的返回值可作为列表被使用，也可以作为布尔值被用于条件表达式中。在 Scheme 里，所有不是 `#f` 的对象都被视为“真”，所括空列表。空列表与表示假的布尔值`#f`是不同的对象，这与 Common Lisp 是不同的。

```
(define memv
  (lambda (x ls)
    (cond
     ((null? ls) #f)
     ((eqv? (car ls) x) ls)
     (else (memv x (cdr ls))))))

(memv 'a '(a b b d)) => (a b b d)
(memv 'b '(a b b d)) => (b b d)
(memv 'c '(a b b d)) => #f
(memv 'd '(a b b d)) => (d)
(if (memv 'b '(a b b d))
    "yes"
    "no") => "yes"
```

这里有两个条件检测，所以使用了 `cond` 表达式。第一个 cond 子句检测 ls 是否为空列表，如果是，则返回 `#f`。第二个子句检测 ls 的 car 是否与 x 相等，如果相等则返回 ls 本身。

也有可能有一个以上的递归。和 `memv` 类似，函数 `remv` 从某个列表中删除所有指定的元素（remv 并非修改列表本身，而是生成了一个新的列表作为返回值）

```
(define remv
  (lambda (x ls)
    (cond
     ((null? ls) '())
     ((eqv? (car ls) x) (remv x (cdr ls)))
     (else (cons (car ls) (remv x (cdr ls)))))))

(remv 'a '(a b b d)) => (b b d)
(remv 'b '(a b b d)) => (a d)
(remv 'c '(a b b d)) => (a b b d)
(remv 'd '(a b b d)) => (a b b)
```

到现在为止，递归还只能在列表的 `cdr` 上进行。然面，有时候需要在列表的 `car` 及 `cdr` 上同时进行递归。下面的`tree-copy` 函数将 `pair` (列表本质上是一种特殊的 pair）视作树，而不是普通的列表。`car`为左子树，`cdr`为右子树. 函数访问树的每一个节点，并且生成一个相同的拷贝。

```
(define tree-copy
  (lambda (tr)
    (if (not (pair? tr))
        tr
        (cons (tree-copy (car tr))
              (tree-copy (cdr tr))))))

(tree-copy '((a . b) . c)) => ((a . b) . c)
```

这种情况叫做双重递归。

至此，那些对别的编程语言有经验的读者也许会怀疑，在 Scheme 中是否需要类似于 `while` 或者 `for` 之类专门的迭代（循环）结构。这是没有必要的，在 Scheme 里，迭代可以通过递归清晰而简洁地表达出来。递归更通用，并且消除了对变量赋值的依赖，使得代码更可靠。一些递归本质上就是迭代（尾递归，Scheme 会执行尾递归优化，消除调用栈的开销），3.2 节会对此展开更多的讨论。通常情况下没必要对此做区分，而应当将集中精力在写出简洁，正确的程序上来。

在离开递归的讨论之前，我们来思考一种以重复为目的的特殊形式——映射。下面的函数 `abs-all` 接受一个数字列表，并且返回一个列表包含原里列里所有元素的绝对值。

```
(define abs-all
  (lambda (ls)
    (if (null? ls)
        '()
        (cons (abs (car ls))
              (abs-all (cdr ls))))))

(abs-all '(1 -2 3 -4 5 -6)) => (1 2 3 4 5 6)
```

这个函数通过在传入的列表中的每一个元素上应用函数 `abs` 而得到另一个新的列表，我们称之为：`abs-all` 将 `abs` 函数映射到输入列表上，产生一个新列表。将一个函数映射到一个列表上是一件很普遍的事，所以 Scheme 提供了 `map` 函数，它将第一个参数（一个函数）映射到它的第二个参数（一个列表）上。我们可以这样定义 `abs-all` 函数。

```
(define abs-all
  (lambda (ls)
    (map abs ls)))
```

我们甚至不需要定义一个 `abs-all` 函数，因为直接使用 `map` 函数更为简短和清晰

```
(map abs '(1 -2 3 -4 5 -6)) => (1 2 3 4 5 6)
```

当然，我们也可以用 `lambda` 创建一个匿名函数作为 `map` 的参数

```
(map (lambda (x) (* x x))
     '(1 -3 -5 7)) => (1 9 25 49)
```

我们还可以将接受多个参数的函数映射到多个列表上

```
(map cons '(a b c) '(1 2 3)) => ((a . 1) (b . 2) (c . 3))
```

列表的长度必须相同，而且列表的数量要与映射函数的参数数量相同。

重新查看最上面定义的 `abs-all` 函数，下面是一个受限的，递归版本的 `map1` 函数，它可以将一个函数映射到一个单独的列表上。

```
(define map1
  (lambda (p ls)
    (if (null? ls)
        '()
        (cons (p (car ls))
              (map1 p (cdr ls))))))

(map1 abs '(1 -2 3 -4 5 -6)) => (1 2 3 4 5 6)
```

它与 `abs-all` 的不同仅仅是将对 `abs` 函数的调用改成了对新的参数 `p` 的调用。更通用的 `map` 函数的定义会在 5.4 节给出。

#### 练习 2.8.1

描述一下，如果你改变了`tree-copy`函数中，cons 的参数顺序，会发生什么。

#### 练习 2.8.2

请查阅 6.3 节的 `append` 函数，然后自己写一个版本的`append`函数，如果在你定义的 `append` 函数里交换了 `append` 的参数顺序，会发生什么？

#### 练习 2.8.3

定义一个函数`make-list`，它接受一个正整数 n 以及一个对象，返回一个新列表，长度为 n，每一个元素都是由第二个参数传入的对象。

    (make-list 7 '()) => (() () () () () () ())

[提示：基本的测试条件可以是`(= n 0)`，递归步进可以是`(- n 1)`]

#### 练习 2.8.4

过程 `list-ref`及`list-tail`分别返回一个列表的 “第n个元素” 以及 “倒数第n个cdr”

```
(list-ref '(1 2 3 4) 0) => 1
(list-tail '(1 2 3 4) 0) => (1 2 3 4)
(list-ref '(a short (nested) list) 2) => (nested)
(list-tail '(a short (nested) list) 2) => ((nested) list)
```

请定义出这两个函数。

#### 练习 2.8.5

在练习 2.7.2 中，在定义 `shorter` 的时候，你使用了 `length`函数，它返回两个列表中较短的一个，或者两个相同长度的列表中的第一个。现在重新定义 `shorter` 函数，不要使用 `length`

[提示：定义一个递归的辅助函数 `shorter?`，并且用它替换长度比较]

#### 练习 2.8.6

现在，你看到的所有递归函数都是直接递归，意思是说，每一个函数在自己的函数体内使用一个新的参数来调用自己。其实可以写出两个互相递归调用的函数来，叫做间接递归。请定义两个函数`odd?`及`even?`，让它们互相调用。

[提示：当参数为0的时候，这两个函数分别要怎么对待？]

#### 练习 2.8.7

使用 `map` 定义一个函数 `transpose`，它接受一个列表作为参数，列表中每个元素都是点对，返回如下的新列表

    (transpose '((a . 1) (b . 2) (c . 3))) => ((a b c) 1 2 3)

[提示：`((a b c) 1 2 3)` 等价于 `((a b c) . (1 2 3))`]

### 2.9.赋值

虽然大多数程序不需要用到赋值，但是有时候，对顶级变量或者由 let、lambda 绑定的变量进行赋值是很有用的。赋值并不像`let`或`lambda`一样创建新的绑定，而是修改已经存在的绑定。赋值通过`set!`来进行。

```
(define abcde '(a b c d e))
abcde => (a b c d e)
(set! abcde (cdr abcde))
abcde => (b c d e)
(let ([abcde '(a b c d e)])
  (set! abcde (reverse abcde))
  abcde) => (e d c b a)
```

很多语言需要使用赋值来初始化本地变量，声明变量与绑定变量是分开的。在 Scheme 里，所有的本地变量在绑定时立即被赋予一个值。Besides making the separate assignment to initialize local variables unnecessary, 它确保程序员不会忘记初始化它们，在大多数语言里，这是很觉的错误。

事实上，对于其它语言是必需的、方便的赋值，对 Scheme 而言却是不必要的，也是不方便的。因为，通常存在着更清晰的不使用赋值的方式来表达相同的算法。One common practice in some language is to sequence expression evaluation with a series of assignments, 比如，下面的函数计算一个二次方程的根

```
(define quadratic-formula
  (lambda (a b c)
    (let ([root1 0] [root2 0] [minusb 0] [radical 0] [divisor 0])
      (set! minusb (- 0 b))
      (set! radical (sqrt (- (* b b) (* 4 (* a c)))))
      (set! divisor (* 2 a))
      (set! root1 (/ (+ minusb radical) divisor))
      (set! root2 (/ (- minusb radical) divisor))
      (cons root1 root2))))
```

根的计算依据二次方公式

![](img/4.gif)

计算出 *0 = ax<sup>2</sup> + bx + c* 的解。在这个函数里，`let`用于单独创建对其它语言而言所必需的变量。开头的三个赋值表达式分别计算公式中的 *-b*,![](img/5.gif), 以及 *2a*。最后两个赋值表达式分别计算两个根。由两个根构成的点对就是`quadratic-formula`的值。例如，*2x<sup>2</sub> - 4x - 6* 的根分别是 *x = 3* 以及 *x = -1*.


