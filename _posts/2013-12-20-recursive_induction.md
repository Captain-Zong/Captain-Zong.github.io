递归和数学归纳法

在设计算法时,递归总是可以"优雅的"解决问题.
但是之前学习数据结构课时总是在想递归调用时陷入一种思想泥潭,无法自拔.

今天再看 ANSI Common Lisp 时,书上的一些话让我豁然开朗,特此记录.

书上说在思考递归的时候根本没必要想明白层层调用是何时何地如何实现的(想想我们在学递归的时候是不是调用关系就是自己最头痛的).作者说只要抓住一点就好--是不是覆盖了所有的情况.

比如算一个列表的长度

```
(defun len (lst)
	(if (null lst)
		0
		(+ (len (cdr lst)) 1)))
```

只要保证

1. 列表长度为0正确.
2. 若长度为n 正确,那么只要保证长度 n+1 正确.

就可以啦~

其实看看这个步骤是不是特别像`数学归纳法`?

但是貌似递归是一种 top-down 的思想,而数学归纳法是一种 bottom-up 的思想.但是就其本质来说其实是一样的. 

所以在设计算法时,不要一开始就陷入对过程的调用中去,应该做的是看看整个算法是否覆盖了所有的情形.(只要数学上证明是对的就能保证结果是对的.)

而且 bottom-up 的思考方式对于我来说更加自然.

那么以后应该从数学归纳的角度来解决递归的问题.O(∩_∩)O~~

并且要注意的是不要忘了初始条件的验证.否则算法往往会进入死循环.