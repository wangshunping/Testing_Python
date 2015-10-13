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
为了让你确定你的程序已经被执行，我们可以加上-v标志，来显示正在被运行的测试的信息。
```
$ python app/calculate.py -v
Trying:
    c = Calculate()
Expecting nothing
ok
Trying:
    c.add(1, 1)
Expecting:
2 ok
Trying:
    c.add(25, 125)
Expecting:
    150
ok
2 items had no tests:
__main__
    __main__.Calculate
1 items passed all tests:
   3 tests in __main__.Calculate.add
3 tests in 3 items.
3 passed and 0 failed.
Test passed.
```

doctest的输出相当不错，清晰的显示了你的doctest执行的每一行——期望输出什么，是否通过了。doctest失败的时候，你也能得到失败的细节原因。将add(1,1)的结果改成3，去掉-v标志，你就能看到失败的doctest通知。
```
$ python app/calculate.py
*******************************************************************
File "app/calculate.py", line 10, in __main__.Calculate.add
Failed example:
    c.add(1, 1)
Expected:
3 Got:
    2
****************************************************************
1 items had failures:
   1 of   3 in __main__.Calculate.add
***Test Failed*** 1 failures.

```

你可以清晰的看到是哪一行导致了这个错误，它期望输出什么，而实际上输出了什么。因此你就可以更新你的doctest文档让他正确，或者改变你的代码，让函数返回和文档一样的值。这次再试试加上-v标准显示更多的细节。

### 处理错误示例

你当然也可以将错误的示例演示给读者，展示出造成你的函数抛出一个异常的情况，测试它的确会这样发生。(#: todo)

这可能是最好的指导了～当你在你的加函数中用了错误的输入。而加函数只支持两个整数，如果你传递一个float类型的数给该函数，一个 TypeError会被暴露出来。把下面的doctest加到doc string里面，去看看在该情境中异常是否被抛出。

```
def add(self, x, y):
    """Takes two integers and adds them together to produce
    the result.
    >>> c = Calculate()
    >>> c.add(1.0, 1.0)
    Traceback (most recent call last):
    ...     
    TypeError: Invalid type: <type 'float'> and <type \ 'float'>
    """
    if type(x) == int and type(y) == int:
        return x + y
    else:
        raise TypeError("Invalid type: {} and {}".format(type(x), type(y)))
```

当你期望某个异常被抛出，你必须以“Traceback(most recent call last):”或者 “Trackback (innermost last):”开头，这取决于在你的代码里这个异常是怎样被抛出的。接着你可以忽略traceback的主体部分，用省略号（...）代替他们，他们通常都是依赖与你正在使用的机器。而最后一行才是真正的你希望抛出的异常，包括了错误信息。当你现在再运行这个程序，它也会没有输出，测试通过。
```
$ python app/calculate.py
$
```

运行doctest，加上-v标志，确保你的异常测试被正确执行。
```
$ python app/calculate.py -v
Trying:
    c = Calculate()
Expecting nothing
ok
Trying:
    c.add(1.0, 1.0)
Expecting:
    Traceback (most recent call last):
     ...
    TypeError: Invalid type: <type 'float'> and <type 'float'>
ok
3 items had no tests:
    __main__
    __main__.Calculate
    __main__.Calculate.__init__
1 items passed all tests:
   4 tests in __main__.Calculate.add
4 tests in 4 items.
4 passed and 0 failed.
Test passed.
```

### 高级的doctest使用

如果你写doctest的技巧不够娴熟，他们很快会变的很大并且偏离你实际写的代码。doctest应该始终把阅读代码的人放在第一位并关注他们的感受。要知道，简洁的、描述性的函数的示例可以提供函数的上下文给阅读代码的开发者。你可以在你的doctest中应用一些小技巧来减少你需要写在doctest里的代码的数量。

在那个计算器例子中，你的函数是类的一部分。因为这个原因，当你写doctest的时候，你必须实例化一个Calcute的类，然后才能按照你希望的调用类方法。一个类可能有很多方法，在每个方法里面新建一个实例非常乏味，并且你还要敲很多空格。 其实dcotest支持上下文的传入，它可以在文件中的所有doctest里面调用。所以与其每次新建一个实例 c = Calculate()，你可以在doctest里面做一次。

```
if __name__ == "__main__":
    import doctest
    doctest.testmod(extraglobs={'c': Calculate()})
```

有了这一个改进，你就不需要在你的doctest里面创造类实例了，整洁的代码就像下面这样。
```
def add(self, x, y):
        """Takes two integers and adds them together to produce the result.
        >>> c.add(1,1)
        2
        >>> c.add(25,125)
        150
        >>> c.add(1.0, 1.0)
        Traceback (most recent call last):
         ...
        TypeError: Invalid type: <type 'float'> and <type 'float'>
        """
        if type(x) == int and type(y) == int:
            return x + y
        else:
            raise TypeError("Invalid type: {} and {}".format(type(x), type(y)))
```































