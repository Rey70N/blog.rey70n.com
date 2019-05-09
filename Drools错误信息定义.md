## Drools校验DRL文件内容的错误信息定义

标准化错误信息格式如下图所示：

![éè¯¯æ¶æ¯æ ¼å¼](assets/error_message.png)

- 第一块（1st Block）：标识错误代码；
- 第二块（2nd Block）：行数和列数信息，定义错误发生区域；
- 第三块（3rd Block）：问题的文字描述；
- 第四块（4th Block）：发生问题的上下文；通常为发生错误的规则、函数、模版或者查询，此块不是强制性的；
- 第五块（5th Block）：发生错误的模式，此块不是强制性的；



举个例子，DRL文件内容如下定义：

> 1:rule one
> 2:   when
> 3:     exists Foo()
> 4:     **exits** Bar()
> 5:   then
> 6: end

则会报错：

> [ERR 101] Line 4:4 no viable alternative at input 'exits' in rule one



