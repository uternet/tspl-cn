## 第3章 更进一步

通过前面一章，你可以用一套很小的元语法结构和过程编写出 Scheme 程序。这一章介绍了一些附加特性，可以让你写出更复杂，更有效率的程序来。

###3.1 语法扩展（宏）

在2.5节，我们看到`let`只不过是一个`lambda`表达式及过程调用的语法扩展。而 lambda 及过程调用这两者都属于核心语法。也就是说，let 语法，可以在 `lambda` 的基础上用宏来实现。现在，你可能想知道在 Scheme 的这么多关键字中，哪一个是核心语法形式，而哪一个是宏，以及，如何定义新的宏。在这一节里提供了一些答案。

事实上，我们没必要明确区分核心语法与宏的区别，因为，一旦定义了一个宏，它就和核心语法形式是相同的地位。分清它们之间的区别只是让你理解语言本身更容易。（意思是，透过现象看本质，你就会明白 Scheme 的核心语法很小，而所有其它的语法都是在这个很小的核心上扩展出来的）

It is necessary for a Scheme implementation to distinguish between core forms and syntactic extensions. A Scheme implementation expands syntactic extensions into core forms as the first step of compilation or interpretation, allowing the rest of the compiler or interpreter to focus only on the core forms. The set of core forms remaining after expansion to be handled directly by the compiler or interpreter is implementation-dependent, however, and may be different from the set of forms described as core here.

[注]好吧，翻译是个苦差事，严格按照英文意思来译，最后的结果是连母语使用者都看不懂。纠结了很长一段时间后我干脆不译了，把原文放在这里。

这一大段其实是说，对程序员来讲，没必要分清楚谁是核心语法，谁是宏。系统提供了这么一个语法，直接写就对了。然而对一个 Scheme 解释器或编译器来说，必须分清楚核心语法和宏。编译或解释的第一步就是宏展开，展开之后就都变成核心语法了。Scheme 只要对这个很小的核心语法进行编译或解释就行了。

The exact set of syntactic forms making up the core of the language is thus subject to debate, although it must be possible to derive all other forms from any set of forms declared to be core forms. The set described here is among the simplest for which this constraint is satisfied.


核心语法形式包括顶级定义(define)，常量，变量，过程调用，quote 表达式，lambda 表达式，if 表达式以及 set!表达式。下面的语法表描述了 Scheme 的核心语法术语的定义和表达式。在下面的语法表里，竖线(|)隔开独立的选项（这里理解成逻辑或比较好），一个形式后面跟着星号（*），表示该形式可以出现0个或多个（这里理解成通配符比较好），<variable> 可以是任何 Scheme 标识符。<datum> 可以是任何 Scheme 对象，比如数字，列表，符号或者向量。<boolean>是 #f 或 #t 其中之一，<number>可以是任何数字,<character>可以是任何字符，<string>可以是任意的字符串。我们已经见过数字，字符串，列表，符号，布尔值以及字符串的例子了。See Chapter 6 or the formal syntax description starting on page 455 for more on the object-level syntax of these and other objects.

```
<程序>			--> <form>*
<form>			--> <定义> | <表达式>
<定义>			--> <变量定义> | (begin <定义>*)
<变量定义> 		--> (define <变量> <表达式>)
<表达式>			--> <常量>
			 |  <变量>
			 |  (quote <datum>)
			 |  (lambda <形参> <表达式> <表达式>*)
			 |  (if <表达式> <表达式> <表达式>)
			 |  (set! <变量> <表达式>)
			 |  <过程调用>
<常量>			--> <布尔值> | <数字> | <字符> | <字符串>
<形参>			--> <变量>
			 |  (<变量>*)
			 |  (<变量> <变量>* . <变量>)
<过程调用>		--> (<表达式> <表达式>*)
```

上面的语法表是模棱两可的，过程调用的语法与lambda, if, 以及 set! 表达式互相冲突。为了有资格作为一个过程被调用，第一个表达式不得是这些关键字之一，除非该关键字已经被重新定义或者被绑定为本地变量。

[注]与 lambda 似乎并不冲突，`((lambda ...) 实参)` 这样的调用是合法的，而`((quote xxx) ...)`则是真的冲突了。常量(字面量)也不能作为过程被调用，if 表达式倒是可以通过不同的分支判断返回不同的过程加以调用。

2.6 节介绍的 defun（Lisp's defun） 语法并没有包含在核心语法中，Lisp 的 defun 语法可以视为 `define` 最简单和直接了当的翻译。类似地，在核心语法定义中，if 表达式不允许省略"else"子句。2.9 节中的例子表明，一个单分支的 if 表达式可以很容易地用任意的常量（比如 #f) 来代替缺失的 "else" 子句，从而翻译成核语法所能接受的表达式。

[注]这一段是对 Scheme 语法的吐槽，比如 define 与 Lisp 的 defun 相比不够清晰简洁，对 if 的处理不够灵活。

A begin that contains only definitions is considered to be a definition in the grammar; this is permitted in order to allow syntactic extensions to expand into more than one definition. begin expressions, i.e., begin forms containing expressions, are not considered core forms. A begin expression of the form

    (begin e1 e2 ...)

等价于过程调用

    ((lambda () e1 e2 ...))

因此，没必要把 begin 包含在核心语法里面。

[注]我说，Kent 你不是 Scheme 标准委员会成员吗？制定标准的时候提出修改的意见就行了，在这里叶槽个啥。

现在，我们已经确定了一个核心语法形式的集合，我们将转到语法扩展（宏）的讨论上。语法扩展之所以这么叫，是因为它们对 Scheme 的核心语法进行了扩展。Scheme 代码里的所有宏，最后必须被展开成核心语法形式。一个宏，

let 可以这样被定义

```
(define-syntax let
  (syntax-rules ()
    ((_ ((x v) ...) e1 e2 ...)
     ((lambda (x ...) e1 e2 ...) v ...))))
```


CPS

正如我们在上一节中所讨论的，延续等待每个表达式的值。特别是，延续与每个过程调用相关联。当一个过程通过非尾调用调用了另一个过程，被调用的过程接收一个隐含的延续，它负责完成主调过程最后剩下的body，并且将结果返回给主调过程的延续。如果调用是一个尾调用，被调过程只需接收主调过程的延续。
