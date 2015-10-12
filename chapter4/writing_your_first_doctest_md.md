# 开始写一个测试文档

doctest模块被包含在python标准库中，你不需要安装就可以开始写doctest。dcotest测试函数的方法和你至今所接触的单元测试略有不同。后者要用明确的方法检查你函数的返回值或者异常，而前者基本上只需要提供该函数在python shell中使用的例子和期望的输出。适当的使用doctest，你可以像unit test一样，像日常测试一样执行他们。就像unit test一样，当你的在python shell中的输出和在doctest描述的一样，他会通过，反之，则会失败。

## Python Shell

python shell 是一个交互式的提示，你可以输入、执行python 代码并看到结果。打开一个命令行（*inux的终端），输入python ,敲回车，你可以看到以下内容:
```
$ python
Python 2.7.6 (default, Jan  7 2014, 12:15:04)
[GCC 4.2.1 Compatible Apple LLVM 5.0 (clang-500.2.79)] on Darwin
           Type "help", "copyright", "credits" or "license" for
  more
information.
>>>
When you see the >>> prompt, you can start typing Python code, like this:
>>> 1 + 1 2
>>>
```
原生python shell 允许你执行一个函数并直接看到结果。写一个把两个数字加到一起的函数。调用它并看到结果：
```
>>> def add(x, y):
...     return x + y
...
>>> add(1,1)
2
>>>

```
现在你了解了基本的python shell的工作方式，你可以按照相同的逻辑为你的函数写一个简单的doctest。

## 为函数添加doctest

使用第二章计算机的例子，你可以简单的加上doc string来解释你的函数的作用。说明：doc string是放在函数定义下面，以三个"""来标记的字符串，用来解释函数的作用。

```
def my_method_with_docstring(arg):
    """This is my doc string.  Here you would describe what the
    method is doing."""
    Doctest extends these strings to include examples of the method in use. Apply a doc string to your add method in the Calculate class from Chapter 2.
    def add(self, x, y):
        """Takes two integers and adds them together to produce
        the result."""
        if type(x) == int and type(y) == int:
            return x + y
    else:
        raise TypeError("Invalid type: {} and {}"
                .format(type(x), type(y)))
```

这个函数现在清楚的像读者描述了他的作用。你现在可以为读者加一些例子来展示你期望的函数的行为，并将他们按照doctests来运行。加一些当你输入是整数的情况的例子。
```
def add(self, x, y):
        """Takes two integers and adds them together to produce
        the result.
        >>> c = Calculate()
        >>> c.add(1, 1)
        2
        >>> c.add(25, 125)
        150
        """
        if type(x) == int and type(y) == int:
            return x + y
        else:
            raise TypeError("Invalid type: {} and {}".format(type(x), type(y)))
```

注意到，你实例化了一个Calaulate类，他可以调用你的函数，然后按照你的期望输出。这是你应该在python shell里面运行的内容。所以如果你不确定放到doctest里面是否正确，请现在python shell里面试一下。

## 运行你的doctest

现在，你没有办法测试你的doctest。为了让他能运行，当你执行包含该类的python文件的时候，你先要加一块代码。加下面的内容到你文件的最下面：
```
if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

现在当你执行的时候，应该是没有输出的。这有一些有悖常理，但是这表示该函数按照你的预期通过啦。～

```
$ python app/calculate.py
$

```













