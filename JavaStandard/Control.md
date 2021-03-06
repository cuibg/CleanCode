# 控制语句

1. 在一个 switch 块内，每个 case 要么通过break/return 等来终止，要么注释说明程序将继续执行到哪一个 case 为止；在一个 switch 块内，都必须包含一个 default 语句并且放在最后，即使空代码。
2. 在 if/else/for/while/do 语句中必须使用大括号。即使只有一行代码，避免采用单行的编码方式：if (condition) statements;
3. 在高并发场景中，避免使用”等于”判断作为中断或退出的条件，可以使用小于或者大于 
4. 表达异常的分支时，少用 if-else 方式，这种方式可以改写成：

  ```
  if (condition) {
    ...
    return obj;
  } 
  ```
5. 除常用方法（如 getXxx/isXxx）等外，不要在条件判断执行其它复杂的语句，将复杂逻辑判断的结果赋值给一个有意义的布尔变量名，以提高可读性。
6. 循环体中的语句要考量性能，以下操作尽量移至循环体外处理，如定义对象、变量、获取数据库连接，进行不必要的 try-catch 操作（这个 try-catch 是否可以移至循环体外）。
7. 避免采用取反逻辑运算符
8. 接口入参保护，这种场景常见的是用作批量操作的接口。