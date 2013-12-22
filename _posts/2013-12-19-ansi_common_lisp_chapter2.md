## ANSI Common Lisp Chapter2: Welcome to Lisp

### 2.1 Form
lisp 表达式只有两种

+ atoms 包括数字,字符串,符号
+ list 一个括号,其中包括用空格隔开的0个或者多个表达式.
	
	`(+ 2 3 4)`,`(/ (- 7 1) (- 4 2))`.均是有效的表达式.

### 2.2 Evaluation
#### lisp 表达式求值规则

lisp 表达式求值过程如下,我们拿`(/ (- 7 1) (- 4 2))`举例.

1. 计算`(- 7 1)`: 7计算得到7,1计算得到1,然后7和1传递给函数`-`,计算得到6
2. 计算`(- 4 2)`: 4计算得到4,2计算得到2,然后4和2传递给函数`-`,计算得到2.
3. 6和2传递给函数`/`,计算得到3.

**注意**:不是所有的操作符都是函数.所以特殊的操作符不一定按照上面的规则进行求值.

#### 操作符 quote
quote 操作符是一个特殊的操作符,不遵从上述求值规则,它的作用是,原封不动的返回它的参数.
如

```
>(quote (+ 3 5))
(+ 3 5)
```
在 common lisp 中我们可以用`'`来表示 quote.

```
>'(+ 2 1)
(+ 2 1)
```


lisp 提供 quote 操作符作为防止表达式求值的一种方式.

### 2.3 data
lisp 提供的数据类型有

1. integer 比如 256.求值等于自身.
2. string 用双引号引起来的字符序列. 如 "hello wold".求值等于自身.
3. symbols 即单词.忽略大小写,一般转化为大写.字符求值往往不得到自身,我们一般用quote引用.避免求值.
4. list 如果不用 quote 引用,那么按照函数调用处理.想要表示一个 list,用 quote 引用之.

#### 函数 list
可以通过调用 list 函数来构造一个 list.

```
>(list 'my (+ 2 1) '(+ 2 1) "Sons")
(MY 3 (+ 2 1) "Sons")
```

注意:list 的第一个元素是一个 symbol,忽略大小写,一般用大写展示,第二个元素是一个函数调用(2+1)的结果整数3,而第三个是一个 list 本身(用 quote 引用了),第四个元素是一个字符串,不忽略大小写.

**lisp 其实就是在用 list 来编程**

#### 空表
lisp 中有两种方式来表达空表.

1. ()
2. nil. 求值等于自身,所以不用 quote 引用之.

### 2.4 List 操作
这里介绍了四个函数,分别是 cons,car,cdr,third.
作用分别是cons:构造conses,car:获得第一个元素,cdr:获得剩余所有的元素(主意不一定是 list,和本身的构成方式有关),third:获得第三个元素.
似乎除了 third 可以表达出这个意思之外,cons,car,cdr 都不能表达其作用,其实这么命名是有历史原因的,至于为什么,请看[cons car cdr 为什么这么命名](http://)这篇文章.
好,开始介绍这四个函数

##### 函数 cons
cons 用于构造conses(注意不是表).接受两个参数.

如果第二个参数是一个 list 的话,那么就将第一个元素加到表头.

```
>(cons 'a '(b c d))
(A B C D)
```

如果第二个参数仅仅是一个原子的话,就构造包含两个原子的表.

```
>(cons 'a (cons 'b nil))
(A B)
```

其实 list 函数就是一个 cons 的进阶版本.

#### 函数 car cdr third
car 用于获得 list 的第一个元素,cdr 获得剩余所有的元素(主意不一定是 list,和本身的构成方式有关),third 获得第三个元素.

```
>(car '(a b c))
A
>(cdr '(a b)) //主意这是一个列表,和下面的表达式等价
(B)
>(cdr (list 'a 'b))
(B)
>(cdr (cons 'a 'b)) //主意这是一个 conses,不是一个列表
B
>(third '(a b c d))
C
```

### 2.5 Truth

符号 t 表示真,t 和 nil 一样求值得到自身.
实际上,除了 nil,() 在逻辑上均为真.

#### predicate

如果一个函数返回真假那么这个函数称之为 predicate 谓词.一般以函数名 p 结尾.

#### 函数 listp

``` 
>(listp '(a b c))
T
>(listp 27)
NIL
```

#### NIL

nil 即可以表示空表也可以表示假.

#### 函数 null

判断list 是否为空表

```
>(null nil)
T
```

#### 函数 not
如果参数为真返回假,参数为真返回假.

```
>(not nil)
T
>(not 'a)
NIL
```

#### 操作符 if

```
(if test-exp 
	then-exp 
	else-exp)
```

如果 test 为真,执行并返回then-expression.如果为假,执行并返回 else-expression.最后一个 else-expression 可选,如果没有返回 nil

```
>(if (listp '(a b c)) 
	(+ １ 2)
	(+ 5 6))
3
>if( "any thing except nil or () is considered as true")
	(+ 1 2)
	(+ 5 6))
11
>(if nil 1 )
NIL
```

#### 逻辑操作符 and or
采用短路判断,参数不限.

and:从左到右,一旦某个参数为假,立即返回 nil,否则返回最后一个表达式的值.

or:从左到右,一旦某个参数为真,立即返回其值,否则返回 nil.

```
>(and 1 (listp 27) t)
NIL
>(or (listp 27) 1 t)
1
```

注意,其实 and 和 or 都是宏.具体实现看第十章.

### 2.6 Functions
#### 自定义函数 defun 

```
(defun func-name list-of-params expression expression ...)
```

自定义函数包括至少三个参数,一个函数名,一个参数列表,若干表达式构成的函数体.

defun 返回函数名.调用函数时,**返回最后一个表达式的值.**

```
>(defun our-third (x) 
	(car (cdr (cdr x))))
OUR-THIRD
>(our-third (a b c d))
C
```
### 2.7 Recursion

调用自身的函数就是递归函数.

举例,鉴定 obj 是否为 list 的某个元素.

```
(defun our-member (obj lst)
	(if (null lst))
		nil
		(if (eql (car lst) obj)
			lst
			(our-member obj (cdr lst)))))
```

#### 函数 eq 和 eql

eq 表示是否是同一个东西.

eql 表示值是否相同.

### 2.8 reading Lisp
lisp 程序因为很多括号变得难读,所以我们采取合适的缩进展现一定的逻辑.

### 2.9 Input and Output
#### 标准输出函数 format.

format 一般由至少两个参数,第一个表示输出地点(一般 t 表示控制台),第二个参数是字符串模板(~A表示需要被后面的参数填充,~%表示换行),后面的参数是模板的参数.

```
>(format t "~A plus ~A equals ~A.~%" 2 3 (+ 2 3))
2 plus 3 equals 5
NIL (函数返回值)
```

#### 标准输入函数 read
read 本身是一个 lisp 解析器,返回一个解析出来的 lisp 对象,而不是单纯的字符串.

read 函数若没有参数表示控制台输入.

最好在 read 前输出提示,否则 read 会一直等待输入.

```
>(defun ask (string)
	(format t "~A" string)
	(read))
>(ask "how old are u? ")
how old are u? 29
29(由 read 解析出来的整数29)
```

### 2.10 Variables
#### 操作符 let
let 可以帮助引入局部变量.
举例如下

```
>(let ((x 1) (y 2))
	(+ x y)
	(- x y))
-1
```

let表达式包含两个部分.第一个部分是包含形如(var exp)列表的列表.第二部分是一系列顺序执行的表达式.返回最后一个表达式的值.

#### 函数 defparameter
let 创建局部变量,defparameter 创建全局变量,全局变量全局可见,除非局部变量和全局变量重名,为了防止以外,我们约定全局变量形如`*global-var*`

```
>(defparameter *glob* (+ 1 98)))
*GLOB*
>*glob*
99
```

#### 函数 defconstant
常量全局可见,不允许局部变量个全局变量和常量重名.

```
>(defconstant limit (+ *glob* 1))
LIMIT
>limit
100
```
#### 函数 boundp
用函数 boundp 检查参数是否全局

```
>(boundp '*glob*)
T
```

### 2.11 Assignment
#### 函数 setf
setf 可以对变量进行赋值

```
>(let ((n 10))
	(setf n 2))
2
```

**注意**:

1. 若 setf 的第一个参数符号之前未曾出现,那么这个符号就是全局的一个变量.(隐式.)
2. setf 的第一个参数也可以是一个表达式.
3. setf 接受偶数个参数.逐对进行赋值.

``` 
>(setf a 1 b 2 c (list 'a 'b 'c))
(A B C)
```

### 2.12 Functional Programming
>Functional programming means writing programs that work >by returning values, instead of by modifying things.It >is the dominant paradigm in Lisp. Most built-in Lisp >functions are meant to be called for the values they >return, not for side-effects.

函数式编程是 lisp 的主要编程范式.而函数时编程的主要思想是通过返回值编程而不是修改值编程.(没有副作用).
函数式编程的几篇文章:)

1. [函数式编程初探](http://)


#### 函数 remove
remove 接受两个参数,obj list1.返回不包含 obj 的 list2.(list1不受影响.)

```
>(setf list1 '(a p p l e))
(A P P L E)
>(remove 'p list1)
(A L E)
>list1
(A P P L E)
```

如果真想删除,可以采取赋值的方式

```
(setf x (remove 'a x))
```


函数式编程:副作用越少越好.
其中的一个好处是可以允许交互式测试.(因为没有副作用)那么代码的正确与否可以得到及时的检验,这对开发来说十分有利,及时的反馈增加信心,且降低了 debug 的成本.

### 2.13 Iteration
如果想要重复的做一件事,可以采用下面的方式.

```
>(def show-squares (start end)
	(do ((i start (+ i 1)))
		((> i end ) 'done)
		(format t "~A ~A ~%" i (* i i))))
SHOW-SQUARES
>(show-squrars 2 3)
2 4
3 9
DONE
```
#### marco do
do 用于迭代操作(循环操作).

do 和 let 一样可以创建局部变量,do 接受至少三个参数.

```
(do ((var init update) ...))
	(test-exp exp ...)
	exp ...)
```
	
第一个是包含形如(var initital update)列表的列表,创建局部变量,进行初始化和更新处理.

第二个是包含至少一个 exp 的列表,第一个列表用来判断停止条件,剩余为条件为真时要执行的一系列 exp.

剩余为条件为假时需要执行的一系列 exp.

最后返回最后执行的 exp 的值.

#### progn
我们来看一下上面 show-squares 的递归版本

```
(defun show-squares (i end)
	(if (> i end)
		'done
		(progn
		(format t "~A ~A ~%" i (* i i))
		(show-squares (+ i 1) end))))
```
progn 接受任一数量的表达式,依次执行.返回最后一个执行的表达式的值.

#### dolist
dolist 类似于 foreach.

```
(dolist (obj lst)
	exp
	exp ...)
```

dolist 的第一个参数(obj lst),表示每次迭代用 obj 代替 lst 的一个元素.
接下来的 exp 依次执行.

### 2.14 Functions as Objects
在 lisp 中,function 就像普通对象一样.地位和 symbol list 字符串一样.

#### 函数 function
接受一个函数名称,返回一个函数关联对象.可以用 `#'`来代替.

```
>(function +)
#<SYSTEM-FUNCTION +>
>#' +
#<SYSTEM-FUNCTION +>
```
#### 函数 apply

```
>(apply #'+ '(1 2 3))
6
>(apply #' 1 2 (3 4 5))
15
```

接受一个函数参数,可以紧跟一个列表,或者只要最后一个参数是个列表,就可以紧跟任一数量的参数.

#### 函数 funcall

```
>(funcall #' 1 2 3)
6
```
和 apply 做相同的事,只不过参数不用以列表的形式展现.

#### lambda expression
并不是所有的函数斗有必要有个名字.

```
>(lambda (arg ...) exp ...) // lambada表达式的形式.
>((lambda (x) (+ x 100)) 1) // 按照普通的函数调用
101
>(funcall #'(lambda (x) (+ x 100)) 1) //funcall调用
101
```

### 2.15 Types
**在 lisp 中,变量没有类型,而值有类型.**并且 lisp 的对象有一个树状的 type 类型.比如,27同时是 fixnum,integer,rational,real,number,atom,t.(注意从左到右,越泛化.)
#### 函数 typep

```
>(typep 27 'integer)
T
```

### 总结
1. Lisp is an interactive language. If you type an expression into the toplevel, Lisp will display its value.2. Lisp programs consist of expressions. An expression can be an **atom**, or a **list** of an operator followed by zero or more arguments. Prefix syntax means that operators can take any number of arguments.3. The evaluation rule for Common Lisp function calls: evaluate the arguments left to right, and pass them to the function denoted by the operator. The quote operator has its own evaluation rule, which is to return the argument unchanged.
4. Along with the usual **data type**s, Lisp has **symbols** and **lists**. Because Lisp programs are expressed as lists, it's easy to write programs that write programs.
5. The three basic list functions are **cons**, which builds a list; **car**, which returns the first element; and **cdr**, which returns everything after the first element.
6. In Common Lisp, **t** represents true and **nil** represents false. In a logical context, anything except nil counts as true. The basic conditional is **if**. The **and** and **or** operators resemble conditionals.
7. Lisp consists mainly of functions. You can define new ones with **defun**.
8. A function that calls itself is **recursive**. A recursive function should be considered as a process rather than a machine.9. Parentheses are not an issue, because programmers read and write Lisp by **indentation**.10. The basic I/O functions are **read**, which includes a complete Lisp parser, and **format**, which generates output based on templates.

11. You can create new local variables with **let** , and global variables with **defparameter**.(用 **defconstant** 创建常数)12. The assignment operator is **setf** . Its first argument can be an expression.13. **Functional programming**, which means **avoiding side-effects**, is the dominant paradigm in Lisp.14. The basic iteration operator is **do**.15. Functions are regular Lisp objects. They can be passed as arguments, and denoted by lambda expressions.16. In Lisp, **values have types, not variables**.


