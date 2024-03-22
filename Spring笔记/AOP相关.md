## 什么是AOP？
了解什么是AOP之前我们先要了解一下[代理模式](../代理模式手写实现/代理模式/代理模式讲解.md)

通过上面的了解我们可以得知：静态代理和动态代理两种方式
而Spring AOP就是基于动态代理的。
- 如果代理的对象实现了接口，Spring AOP 就会使用JDK Proxy去创建代理对象
- 如果没有实现接口，Spring AOP就会通过Cglib来生成一个被代理对象的子类来作为代理

## AOP切面编程的术语：
- 通知：增强的逻辑 / 代码，也即拦截到目标对象的连接点之后要做的事情
- 目标：被通知的对象
- 代理：向目标对象应用通知之后创建的代理对象
- 连接点：目标对象所属的类中，所有方法都属于连接点
- 切入点：被切面拦截或增强的连接点
- 切面：切入点 + 通知的组合
- 织入：将通知应用到目标对象，进而生成代理对象的过程

## Spring AOP和AspectJ AOP的区别？
- Spring AOP属于运行时增强,切面比较少时使用
- AspectJ AOP属于编译时增强，切面比较多时使用

## AspectJ定义的通知类型有哪些？
- Before（前置通知）：目标对象的方法调用之前触发
- After （后置通知）：目标对象的方法调用之后触发
- AfterReturning（返回通知）：目标对象的方法调用完成，在返回结果值之后触发
- AfterThrowing（异常通知）：目标对象的方法运行中抛出 / 触发异常后触发。
- AfterReturning 和 AfterThrowing 两者互斥。如果方法调用成功无异常，则会有返回值；如果方法抛出了异常，则不会有返回值。
- Around （环绕通知）：编程式控制目标对象的方法调用。环绕通知是所有通知类型中可操作范围最大的一种，因为它可以直接拿到目标对象，以及要执行的方法，所以环绕通知可以任意的在目标对象的方法调用前后搞事，甚至不调用目标对象的方法（任意操作）

## 多个切面的执行顺序如何控制？
1. 使用@Order注解
```java
// 值越小优先级越高
@Order(3)
@Component
@Aspect
public class LoggingAspect implements Ordered {}
```
2. 实现Ordered接口重写getOrder方法
```java
@Component
@Aspect
public class LoggingAspect implements Ordered {

    // ....

    @Override
    public int getOrder() {
        // 返回值越小优先级越高
        return 1;
    }
}
```




