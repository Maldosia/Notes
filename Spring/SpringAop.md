### SpringAop

AOP术语

- 连接点(Joinpoint)：程序执行的某个特定位置，表示具体拦截的方法。
- 切点(Pointcut)：程序的连接点有多个，通过切点来定位。
- 增强(Advice)：织入到目标类连接点上的一段程序代码。
- 目标对象(Target)：需要被增强的业务对象。
- 织入(Weaving)：生成代理对象并将切面内容融入到业务流程的过程。
- 代理类(Proxy)：一个类被AOP织入增强后，就产生了一个代理类。
- 切面(Aspect)：由切点和增强组成。例如数据库事务贯穿整个代码，可以称为切面。

#### 增强类型

```text
@Before:目标方法执行前执行
@After:目标方法执行后，不管方法抛出异常还是正常返回，都会执行
@AfterReturning:目标方法正常返回执行
@AfterThrowing:目标方法抛出异常后执行
@Around:目标方法执行前后都会执行
```

#### 使用场景

1. 权限
2. 缓存
3. 事务
4. 错误处理
5. 内容传递
6. logging，tracing，profiling，monitoring
7. 持久化
8. 同步





