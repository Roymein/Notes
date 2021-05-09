# Python

## 第八章 异常

### 8.1 异常定义

python使用异常对象来表示异常状态，在遇到错误时引发异常。异常对象未被处理时，程序被终止并显示一条错误消息。



### 8.2 引发异常

raise 语句：引发异常。将一个Exception的子类或实例作为参数。



常用内置异常

| 类名              | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| Exception         | 所有异常类的父类                                             |
| AttributeError    | 引用属性或给其赋值失败时                                     |
| OSError           | 操作系统不能执行指定的任务时引发，有多个子类                 |
| IndexError        | 使用序列中不存在的索引时引发                                 |
| KeyError          | 使用映射中不存在的值时引发                                   |
| NameError         | 找不到名称时引发                                             |
| SyntaxError       | 代码不正确时引发                                             |
| TypeError         | 将内置操作或函数用于类型不正确的对象时引发                   |
| ValueError        | 将内置操作或函数用于这样的对象时引发：其类型正确但包含的值不合适 |
| ZeroDivisionError | 在除法或求模运算的第二个参数为0时引发                        |



### 8.3 自定义异常类

像创建其他类一样，但务必直接或间接地继承Exception。其代码类似于：

```python
class CustomException（Exception）： pass
```

当然，也可以在自定义异常中添加方法



### 8.4 捕获异常

使用 try/except 语句。捕获异常之后，若需要重新引发它，可调用 raise 且不提供任何参数，使其异常向上传播，如

```python
class MuffledCalculator:
    __muffled = False

    def cal(self, expr):
        try:
            return eval(expr)
        except ZeroDivisionError:
            if self.__muffled:
                print("Division by zero is illegal")
            else:
                raise
```

也可以显式地提供捕获到地异常。要在 except 子句中访问**异常对象本身**，可使用两个而不是一个参数。需要让程序继续运行并记录错误时。

```python
# 显式地捕获异常
# 打印发生地异常并继续运行
try
	....
except (TypeError, ValueError) as e:
    print(e)
```



出现异常时，可能想引发其他异常，导致进入 except 子句地异常被作为异常上下文存储起来，并最终出现在最终地错误中，可**使用 raise …… from ……语句来提供自己地异常上下文**，也可以使用 None 来禁用上下文。

```python
#None 禁用上下文
try
	1/0
except ZeroDivisionError：
	raise ValueError from None
```



如果要在一个 except 语句中捕获多种异常，可在一个元组中指定这些异常，如：

```python
try
	……
except （ZeroDivisionError，ValueError）：
	……
```



在程序处理了多种异常之后，可能仍旧有漏网之鱼，如果要使用一段代码**捕获所有的异常**，只需要**在 except 语句中不指定任何异常类**即可。但这样会很危险，这样将会捕获用户使用ctrl+c终止执行地企图、调用 sys.exit 来终止执行地企图等。最好地选择是使用 except Exception as e 并对异常对象进行检查。



在有些情况下，没有出现异常时执行一个代码块很有用，为此，可像条件语句和循环一样，给 try/except 加一个else语句。

```python
def cal(expr):
    while True:
        try:
            a = int(input("a: "))
            b = int(input("b: "))
            return a / b
        except Exception as e:
            print("reason: {} , please try again.".format(str(e)))
        else:
            break
```



### 8.5 最后

finally子句，可用于在发生异常时执行清理工作。这个子句于 try 子句配套的。



### 8.6 警告

如果只是想发出警告，可使用 warnings 模块中的 warn 函数。

```python
from warnings import warn

warn("……")
```

使用 warnings 模块中的 filterwarnings 来抑制发出的警告，并指定要采取的措施。如 “error”或“ignore”。

```python
from warnings import warn

filterwarings（"ignore"）
```





## 第九章 魔法方法、特性和迭代器

### 9.1 魔法方法（特殊方法）



### 9.2 特性

类比 java 中的 set / get 。python 中用于存取方法，来获取封装状态变量。

```python
class C(object):
    def getx(self): return self._x
    def setx(self, value): self._x = value
    def delx(self): del self._x
    x = property(getx, setx, delx, "I'm the 'x' property.")
```



#### 9.2.1 静态方法和类方法

静态方法：将其包装在 staticmethod 类的对象中，静态方法中没有参数 self，可直接通过类来调用。

```python
    def cal():
        print()

    cal = staticmethod(cal)
```

类方法：将其包装在 classmethod 类的对象中，类方法的定义中包含cls参数，对于类方法，可通过对象之间调用，但参数cls将自动关联到类。

```python
    def work(cls):
        print("class method")

    work = classmethod(work)
```

在 python2.4中引入 **装饰器** 的新语法，可用于这样包装方法。装饰器用于包装任何可调用的对象，并且可用于方法和函数。可指定一个或多个装饰器。

使用装饰器描述特性

```python
class C(object):
    @property
    def x(self):
        "I am the 'x' property."
        return self._x
    
    @x.setter
    def x(self, value):
        self._x = value
    
    @x.deleter
    def x(self):
        del self._x
```



### 9.3 迭代器

方法 __iter__ 返回一个迭代器，它是包含 __next__  对象，next 迭代器应返回下一个值，如果迭代器没有可供返回的值，应引发 StopIteration 异常。 next（）函数与 it.__next__() 等效。

```python
# 斐波那契数列
class Fibs:

    def __init__(self):
        self.a = 0
        self.b = 1

    def __next__(self):
        self.a, self.b = self.b, self.a + self.b
        return self.a

    def __iter__(self):
        return self
    
if __name__ == '__main__':
    f = Fibs()
    for i in Fibs():
        print(i)
        if i > 1000:
            break

    it = iter(Fibs())
    print(next(it))
```



### 9.4 生成器

包含 yield 语句的函数称为 **生成器**。生成器是**一种使用普通函数语法定义的迭代器**。生成器包含 yield 关键字的函数，但被调用时不会执行函数体内的代码，而是返回一个迭代器。每次请求值时，都将执行生成器的代码，直到遇到yield 或return。yield 意味着一个生成一个值，return 意味着生成器应停止执行。

```python
# 生成器实现斐波那契数列
def fibs(n):
    a = 0
    b = 1
    while n > 0:
        a, b = b, a + b
        n -= 1
        yield b
```





```python
# 遍历文件
def lines(file):
    for line in file:
        yield line
    yield "\n"


def blocks(file):
    block = []
    for line in lines(file):
        if line.strip():
            block.append(line)
        elif block:
            yield "".join(block).strip()
            block = []
```



#### 递归式生成器

```python
# 生成器生成数组中的元素 nested = [[1, 2], [3, 4], 5] 
def flatten(nested):
    try:
        for sublist in nested:
            for element in flatten(sublist):
                yield element
    except TypeError:
        yield nested
```

调用flatten时，有两种可能基线条件和递归条件。在基线条件下，要求这个函数展开单个元素，然而如果要展开一个列表，就需要遍历所有的子列表并对他们调用flatten，然后使用另一个for循环生成展开所有的子列表中的所有元素。



