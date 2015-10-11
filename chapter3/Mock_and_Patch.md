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

更多信息，当写这本书的时候，mock库是2013.11.5号的1.0.1版本，但是他相对稳定的版本，应该是1.0版本。

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

你现在可能想知道，在测试你的应用时，这他喵的有什么用？试想你有一个在数据库里查找账号的程序。如果这个账号类初始化用了data_interface 类来调用一个数据库查找账号信息，现在你不用使用这个data_interface而是mock一个data_interface来提供函数返回，用以测试。因为data_interface这个类是一个整的其他职责的类，他应该在其他地方被单独测试。为此，mock的data_interface仅仅设置一个函数来返回你想要返回的内容。这可以让你按照代码需求设置情景，比如成功返回该账号信息，或者返回一些错误。

用代码说明这个例子，你的Account类应该是这个样子：
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


if __name__ == '__main__':
    unitttest.main()
```
通过这种方式给mock_data_interface赋值，你可以建立一个只测试Account类而不用测试任何由data_interface类提供的方法的环境。一旦你能够控制类表现的场景，你将可以测试一些可能发生情况，比如当调用data_interface层面的代码出现问题的异常处理（用Mock模拟异常返回）。能够很好的解决这些问题是很有用的，它也是测试驱动开发（TDD）主要优势之一。（第五章会讲到）

现在我们假设get_account返回一个客户端错误，叫做ConnectionError。这个错误的代码如下：
```
class ConnectionError(Exception):
    pass
```
get_account方法同时更新，能够捕获这个错误并返回错误信息，如下代码：
```
def get_account(self, id_num):
    try:
        result = self.di.get(id_name)
    except ConnectionError:
        result = "Connection error occurred. Try Again."
    return result
```
现在你可以用mocking来模拟异常，来测试代码调用data_interface时，是否正确处理异常并返回错误信息。添加如下测试文件：
```
def test_account_when_connect_exception_raised(self):
    mock_data_interface = Mock()
    mock_data_interface.get.side_effcet = ConnectionError()
    account = Account("Connection error occurred. Try Again.", account.get_account(1))
```

对代码完全控制意味着去掉对真实数据库的依赖，把你能够控制的内容替换进来，确保代码完全按照你的标准测试通过。当你之后希望测试你的代码的真实的性能指标，希望最小化或者根除外部因素，让代码独立，这种隔离测试，是很有用的。（更多信息见第八章）

## Mock干不了？用Patch！
有时候你的应用设计的方式mocking的方法不适用或者根本用不了。大部分情况是你调用一些函数，他们是从你自己或者他人的库import而来，但是你并不想在代码中测试他们。这个时候你没法注入mock，但是你可以用一个Mock的实例去“patch”代码，而不是调用真的实例。一旦你使用了patch，这个方法的返回值就被设置为与上述Mock一样的值。

### Requests库
一个典型的说明你使用了但是并不想测试的例子，大概就数requests库了。这个非常健壮的库最近在需要调用web服务——或者更具体一点，REST服务（比如GET,POST,PUT,DEKETE等等）的python开发者中非常受欢迎。requests库是对python标准库工具urllib2的一个包装。如果你想使用requests库，或者以下示例代码，需要通过pip安装requests。
```
$pip install requests
Successfully installed requests
Cleaning up...
```
同样的说明，本书的requests时2.1.0版本，如果你想使用该版本，请指定版本号安装：
```
$ pip install requests==2.1.0
```

### patch实战

就像上文的数据库例子一样，你并不想在测试中真正的调用web服务，因为当你运行测试的时候，可能那个服务不可用，或者根本没有网络而导致你根本没动代码，但是测试却时好时坏。

ptach一个库就是用来对付这个的，你可以使用@patch装饰器，来指定你想要ptach的Mock实例目标（target）。文档是这么描述target的:

>target 应该为‘package.module.ClassName’形式的字符串。target被引入，指定的对象被新的对象代替，所有target必须在你的调用patch的地方被包含进来。target被引入应该在被装饰时间，而不是执行时间。

为了说明ptach的使用。在之前的Account类里面使用reqeusts.get调用一个web服务来获取一些数据。
```
import requests
...
    def get_current_balance(self, id_num):
        return requests.get("http://some-account-url/"+id_num)
```

这个web请求在account后加上特定的id_num来获取制定的账号信息。在这里，我们仅仅简单的返回用GET方法从特定url上返回的数据。现在，如果你想测试当函数调用时，GET请求是否成功，数据是否返回。你需要使用patch在requests库上面。
```
import unittest
from mock import Mock, patch
from app.account import Account

class TestAccount(unittest.TestCase):
    @patch('app.account.requests')
    def test_get_current_balance_retunrs_data_correctly(self, mock_requests):
        mock_requests.get.return_value = '500'
        account = Account(Mock())
        self.assertEqual('500', account.get_current_balance('1'))
```

你也许注意到了，在上例中，我们仍然使用了Mock ,但是他的作用仅仅是让你创造一个Account类实例，并且拥有 data_interface对象。在这个测试中，你可以只提供一个Mock ,他在你初始化之后不会被使用。这展示了一个非常棒的Mock 的使用方法，当你不关心一个类的作用，活着这个类对你的测试没有影响，这种方法的使用我们叫做傻瓜Mock 。

值得注意的是例子中目标requests库方式。你引入进Account类文件的requests方法实例，不能仅仅是@patch('requests')，这是没用的，这点很重要。你必须制定target、指定被引入的真实的库。

一旦patched mock实例会被当作self之后的第一个参数传递进去。像之前指出的一样，你可以像之前那样操作Mock。你可以mock get调用你的代码以完成测试，去看看函数是否确实返回了你从requests里获得的值。

尽可能保持你的测试明确和独特是非常重要的。测试你的代码的一个方面只能让你的测试很low。有了如此简洁的测试，当代码出了问题，会很容易debug。因为他们已经每个函数都有一个明确的测试，这能大大的缩小范围。Mocking和Patching能帮你达到这个目标的同时，帮你减少冗余的测试代码。你可以使你的代码独立，并且在每个测试中只测试他的功能性的某一个方面。

## 高级Mocking指南
如果你对requests库有一点了解，你可以把这个例子向前再进一步，着手开始一些更好mocking来使用真实的requests库行为。作为一个使用过requests库的开发者，当你使用GET请求的时候，requests库会返回一个对象，该对象包含一些属性，比如status_code和text。改变你的Account类行为让他们使用这些属性，然后你就可以在你的测试中patch这些行为。

```
def get_current_balance(self, id_num):
    response = requests.get("http://some-account-url/"+id_num)
    return {'status': response.status_code,
            'data': response.text}
```

如果使用上述代码，你之前的测试会失败，因为他仅仅mock这个库返回一个字符串。你需要做的是，mock这个请求，让他返回另一个mock。你可以为第二个mock田间一些属性去模仿requests库的行为。

```
@patch('app.acount.requests')
def test_get_current_balance_returns_data_correctly(self, mock_requests):
    mock_response = Mock()
    mock_response.status_code = 200
    mock_response.text = 'Some Text Data'
    mock_requests.get.return_value = mock_response
    account =  Account(Mock())
    self.assertEqual({'status': 200, 'data': 'Some Text Data'},
                        account.get_current_balance('1'))
```
像上面这样，我们先创造了一个mock_response对象，为他加了你需要的属性，调用mock_request.get函数，设置return_value为mock_response。现在你可以通过mock和patch测试所有不同的http调用，比如得到一个404Not Fount，500Bad Server。




















