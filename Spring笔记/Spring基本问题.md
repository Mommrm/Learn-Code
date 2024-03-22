## @Resource和@Autowired的区别是什么？

@Autowired是属于Spring内置的注解，默认的注入方式是byType，但是如果有多个实现类的话，那就无法正确注入对象了，这时候会根据byName的方式进行匹配。

例子UserService接口有两个实现类，UserServiceImpl1和UserServiceImpl2：
```java
//byType和byName都无法匹配成功
@Autowired
private UserService userService;
//正确注入UserServiceImpl1对象对应的Bean
@Autowired
private UserService userService1;
//使用@Qualifier注解指定名称
@Autowired
@Qualifier("userService1")
private UserService userService;
```


@Resource属于JDK提供的注解，默认注入方式为byName，无法通过byName注入时会改变为byType

```java
// 报错，byName 和 byType 都无法匹配到 bean
@Resource
private UserService userService;
// 正确注入 SmsServiceImpl1 对象对应的 bean
@Resource
private UserService userService1;
// 正确注入 SmsServiceImpl1 对象对应的 bean（比较推荐这种方式）
@Resource(name = "userService1")
private UserService userService;
```

- @Autowired 是 Spring 提供的注解，@Resource 是 JDK 提供的注解。
- Autowired 默认的注入方式为byType（根据类型进行匹配），@Resource默认注入方式为 byName（根据名称进行匹配）。
- 当一个接口存在多个实现类的情况下，@Autowired 和@Resource都需要通过名称才能正确匹配到对应的 Bean。Autowired 可以通过 @Qualifier 注解来显式指定名称，@Resource可以通过 name 属性来显式指定名称。
- @Autowired 支持在构造函数、方法、字段和参数上使用。@Resource 主要用于字段和方法上的注入，不支持在构造函数或参数上使用。

