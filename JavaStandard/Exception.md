# 异常
1. Java 类库中定义的可以通过预检查方式规避的 RuntimeException 异常不应该通过catch 的方式来处理，比如：NullPointerException，IndexOutOfBoundsException 等等。
2. 异常不要用来做流程控制，条件控制。
3. catch 时请分清稳定代码和非稳定代码，稳定代码指的是无论如何不会出错的代码。对于非稳定代码的 catch 尽可能进行区分异常类型，再做对应的异常处理。
4. 捕获异常是为了处理它，不要捕获了却什么都不处理而抛弃之，如果不想处理它，请将该异常抛给它的调用者。最外层的业务使用者，必须处理异常，将其转化为用户可以理解的
内容
5. 有 try 块放到了事务代码中，catch 异常后，如果需要回滚事务，一定要注意手动回滚事务。
6. finally 块必须对资源对象、流对象进行关闭，有异常也要做 try-catch。
7. 不要在 finally 块中使用 return。
8. 捕获异常与抛异常，必须是完全匹配，或者捕获异常是抛异常的父类。
9. 方法的返回值可以为 null，不强制返回空集合，或者空对象等，必须添加注释充分说明什么情况下会返回 null 值。
10. 防止 NPE，是程序员的基本修养，注意 NPE 产生的场景
11. 定义时区分 unchecked / checked 异常，避免直接抛出 new RuntimeException()，更不允许抛出 Exception 或者 Throwable，应使用有业务含义的自定义异常。推荐业界已定义
过的自定义异常，如：DAOException / ServiceException 等。
12. 对于公司外的 http/api 开放接口必须使用“错误码”；而应用内部推荐异常抛出；跨应用间 RPC 调用优先考虑使用 Result 方式，封装 isSuccess()方法、“错误码”、“错误简
短信息”。
13. 避免出现重复的代码（Don’t Repeat Yourself），即 DRY 原则。

