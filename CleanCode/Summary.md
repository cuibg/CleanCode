# 味道与启发

## 注释

1. 不恰当的信息

	> 作者、修改时间、SRP这些玩意都不应该在注释中出现，注释只应该描述有关代码和设计的技术性信息

2. 废弃的注释
3. 冗余的注释

	> **[warning] 不要画蛇添足，要字斟句酌**

4. 糟糕的注释  

## 环境

1. 构建系统应该是一步到位
2. 测试也应该是一个指令可以运行多个测试

## 函数

1. 过多的参数要避免
2. 输出参数

	> 输出参数违法直觉，读者希望参数用于输入而非输出

3. 标识参数

	> 令人恶心的boolean值，从而告诉了我们函数做了不止一件事

4. 死函数

	> 应该直接丢弃掉

## 一般性问题

1. 一个源文件中多种语言是不可取的
2. 明显的行为要实现

	> **[success] 遵循最小惊异原则，函数或者类应该实现其他程序员期待的事情**

3. 边界行为要重视
4. 不可重复

	> 同一条件的switch或者if可以用多态进行代替

5. 抽象等级代码不要分隔错误

	> 只与细节实现相关的常量、变量、或者工具函数不应该在基类中出现

6. 基类依赖于派生类是不科学的做法
7. 接口不能信息过多

	> 类中的方法越少越好，函数知道的变量越少越好

8. 死代码不需要（永不执行的代码）
9. 垂直分割
10. 前后一定要一致

	> 在一个方法中名为response的变量来持有HttpServletResponse，其他方法应该也是这个名字

11. 不可以混淆视听
12. 避免人为耦合

	> 两个没有目的之间的模块的耦合，根源是将变量或者常量放到不恰当地位置，偷懒行为真可耻

13. 消除特性依恋

	> 类的方法只应该对其所属类中的变量和函数感兴趣，不应该垂青其他类中的变量和函数
14. 选择算子参数

	> 使用多个函数犹于向单个函数传递某些代码来选择函数行为

15. 意图要明确
16. 位置要明确与合理
17. 静态方法一定要恰当

	> Math.max(a,b)这种静态方法就很合理，我们不需要拿到Max的多态特征，通常选择非静态的方法，如有有疑问，就用非静态方法

18. 使用解释性的变量
19. 函数名称明确表达行为和动作
20. 把逻辑以来改成物理依赖

	> A依赖于B，B所需要的一些东西不应该在A中拿

21. 多态代替if和switch

	* 对于给定的类型，我们使用单个Switch原则
	* 如果有多个，就必须创建多态对象

22. 遵循团队标准
23. 命名常量代替魔术值
24. 一定要准确，如果方法返回null，自己要判断
25. 结构甚于约定
26. if和switch的条件适合于封装
27. 避免否定条件，直接用肯定的
28. 函数只做一件事
29. 我们应该暴漏时序耦合
30. 别随意
31. 封装边界条件
32. 函数应该只在一个抽象层级上
33. 避免传递浏览，得墨忒耳律

## java

1. 通过通配符来避免导入过长清单文件
2. 不要继承常量（很恶心）
3. 常量和枚举（枚举有很多好处）
4. 采用描述性的名称
5. 名称应该与抽象等级相符合
6. 尽可能采用标准命名法
7. 无歧义的名称，比如说得到屌丝会员的内容
8. 为较大范围选择更长的名称
9. 避免编码命名，m_和f_前缀无意义
10. 名称说明函数的副作用

## 测试

1. 测试不足
2. 使用覆盖率工具（clover）
3. 别掠过小测试
4. 被忽略的测试就是对不确定事物的疑问（我们可以采用@ignore或者是注视掉）
5. 测试边界条件
6. 全面测试相近的缺陷

	> 缺陷趋向于扎堆，某个函数发现一个缺陷时，最好全面测试这个函数

7. 测试应该快速
8. 测试覆盖率的模式有启发性

	> 查看被或未被通过的测试执行的代码，往往能发现失败的测试为何失败

9. 测试失败的模式有启发性

	> 可以找到问题的所在