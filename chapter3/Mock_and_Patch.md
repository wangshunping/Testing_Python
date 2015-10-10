# Mock和Patch小技巧

  当你开始写测试的时候，很可能会遇到这样的情况，你的代码和一些测试完备的系统进行了交互，比如说调用了数据库服务或者web服务。在测试用例中，有很多原因，并不想真的去调用他们。比如，数据库和web服务挂了，但是你不想因此报出一个失败的测试在你测试系统中，这可是一个假阳性错误哇！～再比如，这些调用很慢或者会话费公司的钱，当你每次运行一次测试，会很花时间或者金钱。这就是引入Mock和Patch的意义，他让你能够将这些真实的调用变成你杜纂的返回值，这让你可以测试在这些情况下，代码行为是否符合预期。
  
## 安装Mock库
首先要安装Mock库，这样你才能在你的类中创造mock实例来模拟返回值。还是用pip安装。

```
$ pip install mock
...
Successfully intalled mock
Cleaning up...
```

更多信息，当写这本书的时候，mock库是2013.11.5号的1.0.1版本，但是他相对稳定的版本，应该是1.0版本。（# TODO）

## Mocking一个类和函数返回

建造一个Mock相当简单，你只需要引入Mock类然后创造它的一个实例。然后你就可以为mock添加函数让它返回你想要返回的值。新建一个名为 mock_example_test.py的文件，代码如下。
```
import unittest
from mock import Mock

class TestMocking(unittest.TestCase):
    def test_mock_method_returns(self):
        my_mock = Mock()
        my_mock.my_method.return_value = "Hello"
        self.assertEqual("hello", my_mock.my_method())
```

在这个例子中，你可以创造一个名为my_mock的实例，并且为它添建my_method方法，当它被调用的时候，它应该返回字符串"hello"。

你现在可能想知道，在测试你的应用时，这他喵的有什么用？试想你有一个在数据库里查找账号的程序。如果这个账号类初始化用了data_interface 类来调用一个数据库查找账号信息，(# TODO)

用代码说明这个粒子，你的Account类应该是这个样子：
```
class Account(object):
    def __init__(self, data_interface):
        self.di = data_interface
        
    def get_account(self):
        return self.di.get(id_num)
```
这个类只提供了一个方法，用以返回你输入的id_num在数据库里对应的数据。现在写一个类，来检查你mock的data_interface，当ID为1，是否正确返回。新建一个名为account_test.py的文件，代码如下：
```
import unittest
from mock import Mock
from app.account import Account

class TestAccount(object):
    def test_account_returns_data_for_id_1(self):
        account_data = {"id":"1", "name":"test"}
        mock_data_interface = Mock()
        mock_data_interface.get.return_value = account_data
        account = Account(mock_data_interface)
        self.assertDictEqual(account_data, account.get_account(1))
```

if __name__ == '__main__':
    unitttest.main()
    
















