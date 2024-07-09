## 什么是Bean？
简单来说就是被IoC容器管理的对象

IoC容器：实质上是一种设计模式，实现了管理Bean对象

## 1. 传统XML配置方式
标准beans xml配置格式
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
                        http://www.springframework.org/schema/beans/spring-beans.xsd">


  
</beans>
```

## /2 基于注解的配置方式
- @Component注解：通用的注解，可标记任意类为Spring Bean
- @Service注解：用于标记业务层Bean
- @Repository注解：用于标记数据访问层Bean
- @Controller注解：用于标记表现层Bean

##### @Component 和 @Bean 的区别是什么？
- @Component注解用于类，而@Bean注解用于方法

## 3. 基于类的Java Config配置方式
- @Configuration注解：用于标记配置类，Spring容器在启动时会扫描带有@Configuration注解的类，并解析里面的@Bean注解
- @Bean注解：用于标记Bean方法
- @Import注解：用于导入其他配置类


## Bean的作用域有哪些？
- singleton：单例模式，Spring容器中只会存在一个Bean的实例，默认模式
- prototype：每次获取都会创建一个新的Bean
- request：(仅Web应用可用)每次HTTP请求都会产生一个Bean，仅在HTTP请求内有效
- session：(仅Web应用可用)每次HTTP会话都会产生一个Bean，仅在当前会话内有效
- application/global-session （仅 Web 应用可用）：每个 Web 应用在启动时创建一个 Bean（应用 Bean），该 bean 仅在当前应用启动时间内有效。
- websocket （仅 Web 应用可用）：每一次 WebSocket 会话产生一个新的 bean。

## Bean是线程安全的吗？
取决于作用域和状态

在prototype作用域下，每次获取Bean都会产生一个新的实例，因此不存在线程安全问题，而在singleton作用域下，只存在一个Bean实例，是共享的，因此会存在线程安全问题。

- 如在Bean状态可变的情况下，也就是说拥有可变的成员变量，如数组、集合、自定义类的情况

解决方法：
- 尽量不要在Bean中定义可变变量
- 在类中定义一个ThreadLocal成员变量，把可变成员变量保存在ThreadLocal中

## Bean的生命周期

1. Bean容器找到配置文件中Spring Bean的定义
2. Bean容器利用Java Reflection API创建Bean实例
3. 涉及属性值，利用set()来设置属性值
   
4. 如果实现了其他 *.Aware接口，就调用相应的方法。
   - Bean实现了BeanNameAware接口，调用setBeanName()方法，传入Bean的名字
   - Bean实现了BeanClassLoaderAware接口，调用setBeanClassLoader()方法，传入Bean的类加载器
   - Bean实现了BeanFactoryAware接口，调用setBeanFactory()方法，传入BeanFactory实例

5. Spring容器相关
   - 如果有和加载这个 Bean 的 Spring 容器相关的 BeanPostProcessor 对象，执行postProcessBeforeInitialization() 方法
   - 如果 Bean 实现了InitializingBean接口，执行afterPropertiesSet()方法。
   - 如果 Bean 在配置文件中的定义包含 init-method 属性，执行指定的方法。
   - 如果有和加载这个 Bean 的 Spring 容器相关的 BeanPostProcessor 对象，执行postProcessAfterInitialization() 方法

6. Bean销毁
   - 当要销毁 Bean 的时候，如果 Bean 实现了 DisposableBean 接口，执行 destroy() 方法。
   - 当要销毁 Bean 的时候，如果 Bean 在配置文件中的定义包含 destroy-method 属性，执行指定的方法。



 