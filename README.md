# daily-life

**这是Leo日常学习、工作记录**

# 2020-12-27 

- 设计模式的核心是方便代码的维护和项目重构？

![微信截图_20201227131555](img/微信截图_20201227131555.png)

- 

# 2020-12-26 

- 前端是否需要写自动测试的代码

  ```javascript
  if(你写的是一个utils类 || 你写的是一个公共component || 你写的是一个开源项目)
    return 你需要写单元测试（UT）代码
  ```

![WX20201226-231903@2x](img/WX20201226-231903@2x.png)

![WX20201226-232056@2x](img/WX20201226-232056@2x.png)

# 2020-12-25

- 代码评审（Code Review)的作用
  - 降低低级bug产出，提高代码产出质量
  - 学习别人更好的代码风格和解决方案
  - 代码评审可以更好地评估工时，因为业务有时候是相似的，通过回顾以往代码，可以了解该模块的业务背景及开发手段，从而优化更好的方案，对于评估做类似模块的工时也会更为准确
  - 代码评审让团队成员不再只是涉及自；己开发的模块，而是对别人模块也有所了解，这样其他成员可以在后续工期接手前人的代码
  - 代码评审有助于指导培养新工程师
- 需求评审的作用
  - 明白需求的背景和目标
  - 统一需求实现的过程、方案以及相关功能点
  - 让参会成员清楚各种任务以及完成时间
  - 确认研发和测试公式，产品经理按照需求上线时间判断是否需要拆分任务、增加资源

# 2020-12-16

- Typescript的优势到底在哪？
  - 类型推断，减少“不知是字符串还是数字所以要转成数字”类似的兜底情况
  - 一边写代码一边产出半成品文档（因为注释较多） —— 这是个“成也萧何败也萧何”的问题
  - 杜绝了手误导致的变量名错误（找不到该变量命就会报错提示）
  - **静态类型有利于构建大型应用**，静态类型检查可以做到early fail，即你编写的代码即使没有被执行到，一旦你编写代码时发生类型不匹配，语言在编译阶段（解释执行也一样，可以在运行前）即可发现。针对大型应用，测试调试分支覆盖困难，很多代码并不一定能够在所有条件下执行到。而假如你的代码简单到任何改动都可以从UI体现出来，这确实跟大型应用搭不上关系，那么静态类型检查确实没什么作用。
