# 开始写一个测试文档

doctest模块被包含在python标准库中，你不需要安装就可以开始写doctest。dcotest测试函数的方法和你至今所接触的单元测试略有不同。后者要用明确的方法检查你函数的返回值或者异常，而前者基本上只需要提供该函数在python shell中使用的例子和期望的输出。适当的使用doctest，你可以像unit test一样，像日常测试一样执行他们。就像unit test一样，当你的在python shell中的输出和在doctest描述的一样，他会通过，反之，则会失败。

## Python Shell
