---
categories: 随笔
---
本篇文档是对 《编写高质量代码改善Python程序的91个建议》一书的记录

Pythonic代码 

```
优美胜于丑陋（Python 以编写优美的代码为目标）
明了胜于晦涩（优美的代码应当是明了的，命名规范，风格相似）
简洁胜于复杂（优美的代码应当是简洁的，不要有复杂的内部实现）
复杂胜于凌乱（如果复杂不可避免，那代码间也不能有难懂的关系，要保持接口简洁）
扁平胜于嵌套（优美的代码应当是扁平的，不能有太多的嵌套）
间隔胜于紧凑（优美的代码有适当的间隔，不要奢望一行代码解决问题）
可读性很重要（优美的代码是可读的）
--Python之禅
```

1.交换变量

```
a,b = b,a   #充分利用python特色很简洁
```

2.迭代列表

```
for i in alist:
	do_something(i)
```

3.倒序列表或者字符串

```
a = [1,2,3,4]
b = 'abcdefg'
print(a[::-1])
print(b[::-1]) #不可取 因为不太具有可读性 不太容易理解

可以变为使用 reversed函数
print(list(reversed(a)))
print(list(reversed(b)))
```

4.输出

```
print 'Hello %s!' % ('Tom',)

但是当变量数量多时 可读性变差，可以使用下面的方式
print  'Hello %(name)s!' % {'name':'Tom'}

举例：
value = {'greet':'Hello word','language':'python'}
print '%(greet)s from %(language)s' % value

但是%占位符源于对python的先验知识，对于新手有点不明白，所以下面的代码更合适
print '{greet} from {language}'.format(greet='Hello word',language='python')
[上面利用的是 python的  str.format() 这个方法]
```

5.关于包和模块

```
包和模块的命名采用小写，单数形式，而且短小
包通常仅作为命名空间，如只包含空的__init__.py文件
```

6.编写Pythonic代码

```
1.避免劣化代码
	(1).避免只用大小写来区分不同的对象，如 变量a 是数值类型  变量A为字符串类型
	(2).避免使用容易引起混淆的名称，变量名要和所解决的问题一致
	(3).不要害怕过长的变量名，不要为了少些几个字符过分缩写，例如 person_info 的可读性比 pi 高很多
2.深入认识python
	(1).全面掌握Python提供给我们的所有特性，包括语言特性和库特性。其中最好的学习方式就是通读官方手册中的Language Reference 和 Library Reference。掌握了语言特性和库特性，就会利用很多python的惯用法。
	(2).随着python的迭代更新，不断学习新版本中更新的特性
	(3).深入学习业界公认的比较Pythonic的代码 如 flask,gevent,requests等
3.利用工具  (pip install pep8) 检测不符合代码格式的代码
4.注释的艺术
	(1)不写无用的注释
	(2)注释那些别人难以理解的或者不够一目了然的代码
	(3)注释和代码隔开一定距离 例：x=x+1       #increace x by 1
	(4)给外部可访问的函数和方法添加文档注释(函数注释要形成一定的规范，可参考python第三方库中的注释)
	(5)推荐在文件头添加 copyright,模块声明 等信息
	(6)注释是对代码的功能，原因以及想法进行注释 不要做一些无用的注释
	(7)代码与注释同步更新
5.常量的管理
	(1)常量名所有字母大写 用下划线连接
	(2)可以通过自定义的类实现常量功能。需要符合 命名全部为大写和值一旦绑定不可修改
		
		const.py
		class _const:
			class ConstError(TypeError):pass
			class ConstCaseError(ConstError):pass
			
			def __setattr__(self,name,value):
				if name in self.__dict__.keys():
					raise self.ConstError,"can't change const.%s" % name
				if not name.isupper():
					raise self.ConstCaseErrpr,'const name "%s" is not all uppercase' % name
				self.__dict__[name] = value
		
	import sys
	sys.modules[__name__] = _const()
	
	test.py
	import const
	const.NAME = 'zhang'
	
	##
	这里第一次接触这样定义常量，需要理解 __name__ ，sys.modules，import 和 from...import...区别
	(1)__name__是python模块中的一个变量  用来表示模块名。如果在本模块中打印则__name__为__main__，如果这个模块在其他模块中被调用 则name为模块名称
	(2)sys.modules 是一个全局的字典 里面存放了和模块名对应的模块， python运行时会放在内存中
	(3) import 模块 ???
	##
```

惯用法

1.assert 断言的使用

```
不要滥用断言
断言实际用来捕获用户定义的约束的，当条件是业务逻辑继续下去的先决条件时可以使用断言。如 前面代码必须要保证变量x,和y一致，而下面业务进行下去的条件是x和y一致 可以使用断言 assert x==y,'not equal'
当函数调用后，需要确认返回值是否合理时可以使用断言
```

2.数据交换值时不建议使用中间变量

```
temp = x
x = y
y = temp

与代码
x,y = y,x 

比较下面的方法更加pyhtonic 并且也更加高效
```

3.充分利用 Lazy evaluation （延迟计算，惰性计算）的优势，即在真正需要计算的时候才计算的表达式

```
(1).避免不必要的计算，比如 if x and y 时 要将x和y中为False的几率大的放在前面 和 if x or y 将为True几率大的放在前面。 
(2).节省空间，python最典型的延迟计算就是使用 生成器表达式，仅仅在每次需要的时候才通过yield产生数据，例如斐波那契数列，就不会出现在其他语言中while True时遇到的无限循环的例子
 	def fib():
 		a,b = 0,1
 		while True:
 			yield a
 			a,b = b,a+b
 	from itertools import islice
 	print(list(islice(fib(),5)))  #[0，1，1，2，3]
```

4.