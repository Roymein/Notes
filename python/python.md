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

