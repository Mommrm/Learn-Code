## @SpringBootApplication
可以看成是由三个注解组合而成的：
- @Confiuration:运行该类下注册额外的Bean或导入配置其他类
- @EnableAutoConfiguration:开启SpringBoot自动装配
- @ComponentScan:开启扫描@Component、@Controller、@Service、@Repository等注解的Bean

## Bean相关

[Bean注解](../Spring笔记/IOC&&Bean相关.md)

## 处理HTTP请求相关
5中常见的请求类型：
- GET：获取资源
- POST:创建资源
- PUT:更新资源
- DELETE:删除资源
- PATCH:更新服务器上的资源

### 请求注解
对应的注解
- GET: @GetMapping()
- POST: @PostMaping()
- PUT: @PutMapping()
- DELETE: @DeleteMapping()
- PATCH: @PatchMapping()
通用注解：
@RequestMapping(value = "path", method = RequestMethod = RequestMethod.XXX)

### 前后端传值
##### 可以有多个@RequestParam注解和@PathVariable注解
- @RequestParam: 获取查询参数
- @PathVariable: 获取路径参数

```url
/klasses/123456/teachers?type=web
```
```java
@GetMapping("/klasses/{klassId}/teachers")
public List<Teacher> getKlassRelatedTeachers(
         @PathVariable("klassId") Long klassId,
         @RequestParam(value = "type", required = false) String type ) {
...
}
```
##### 只能有一个@RequestBody注解
- @RequestBody: 获取Request请求中的body部分(接收JSON格式数据)

```java
@PostMapping("/sign-up")
public ResponseEntity signUp(@RequestBody @Valid UserRegisterRequest userRegisterRequest) {
  userService.save(userRegisterRequest);
  return ResponseEntity.ok().build();
}
```


## 读取配置信息

- @Value注解:读取配置文件中的属性值
- @ConfigurationProperties注解:读取配置文件中的属性值，并绑定到一个类上
- @PropertySource读取指定 properties 文件


## 校验信息
SpringBoot已经导入了Hibernate Validator，可以使用注解来校验参数。

- @NotEmpty 被注释的字符串不能为null或为空 " "满足
- @NotBlank 被注释的字符串不能为null或必须包含一个非空字符 " "不满足
- @Null 被注释的元素必须为null
- @NotNull 被注释的元素不能为null
- @AssertTrue 被注释的元素必须为true
- @AssertFalse 被注解的元素必须为False
- @Pattern(regex=,flag=)被注释的元素必须符合指定的正则表达式
```java
// 这里的flag是忽略大小写
@Pattern(regex=^abc$,flag= Pattern.Flag.CASE_INSENSITIVE)
```

- @Email 被注释的字符串必须是Email格式
- @Min(value) 被注释的元素必须大于等于指定的最小值(验证整数类型)
- @Max(value) 被注释的元素必须小于等于指定的最大值(验证整数类型)
- @DecimalMin(value) 和@Min(value)类似，但用于验证BigDecimal
- @DecimalMax(value) 和@Max(value)类似，但用于验证BigDecimal
- @Size(max=, min=) 被注释的元素的大小必须在指定的范围内
- @Past被注释的元素必须是一个过去的日期
- @Future 被注释的元素必须是一个将来的日期

##### 使用：
```java
//请求体
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Person {

    @NotNull(message = "classId 不能为空")
    private String classId;

    @Size(max = 33)
    @NotNull(message = "name 不能为空")
    private String name;

    @Pattern(regexp = "((^Man$|^Woman$|^UGM$))", message = "sex 值不在可选范围")
    @NotNull(message = "sex 不能为空")
    private String sex;

    @Email(message = "email 格式不正确")
    @NotNull(message = "email 不能为空")
    private String email;

}

```
###### 接收前端参数注解配合@RequestBody @Valid一起使用
```java
@RestController
@RequestMapping("/api")
public class PersonController {

    @PostMapping("/person")
    public ResponseEntity<Person> getPerson(@RequestBody @Valid Person person) {
        return ResponseEntity.ok().body(person);
    }
}

```

如果出现不匹配的情况，会抛出MethodArgumentNotValidException


## JPA相关

1. 创建表
@Entity： 声明一个类对应数据库中的实体
@Table(name = "tablename"): 设置表名

2. 创建主键
@Id: 声明主键
@GeneratedValue(strategy = GenerationType.IDENTITY): 主键生成策略，默认是自动生成，这里设置为手动生成

3. 设置字段类型

@column： 声明字段
```java
// 设置属性 userName 对应的数据库字段名为 user_name，长度为 32，非空
@Column(name = "user_name", nullable = false, length=32)
private String userName;
// 设置字段类型并且加默认值，这个还是挺常用的。
@Column(columnDefinition = "tinyint(1) default 1")
private Boolean enabled;
```

4. 指定不持久化特定字段
@Transient: 声明该字段不持久化

5. 声明大字段
@Lob: 声明该字段为大字段，比如图片、视频等

6. 创建枚举类型的字段
```java
// 枚举类
public enum Gender {
    MALE("男性"),
    FEMALE("女性");

    private String value;
    Gender(String str){
        value=str;
    }
}
// 注解
@Entity
@Table(name = "role")
public class Role {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String description;
    @Enumerated(EnumType.STRING)
    private Gender gender;
    省略getter/setter......
}

```

7. 审计功能
@EnableJpaAuditing: 开启审计功能


8. 删除/修改数据
@Modifying: 声明该方法修改数据

9. 关联关系

- @OneToOne: 一对一关系
- @OneToMany: 一对多关系
- @ManyToOne: 多对一关系
- @ManyToMany: 多对多关系


## 事务相关

@Transactional: 声明一个方法为事务方法（一般用于类或方法上）
- 用于类上：该类所有的public方法都会加上事务
- 用于方法上：会覆盖类上的事务的配置信息，如果类上加了@Transactional

```java
@Transactional(rollbackFor = Exception.class)
public void save() {
  ......
}
```
Exception分为运行时异常和非运行时异常
这里加上rollbackFor是因为如果不加上这个只会在RuntimeException时候才会回滚

## Json相关
1. 过滤Json数据
@JsonIgnoreProperties作用是在类上过滤掉特点字段，不返回或不解析
@JsonIgnore作用于字段上
```java
//类上
@JsonIgnoreProperties({"userRoles"})
public class User{}
//字段上
@JsonIgnore
private String password;
```
2. 格式化Json数据
@JsonFormat: 格式化数据
```java
@JsonFormat(shape=JsonFormat.Shape.STRING, pattern="yyyy-MM-dd'T'HH:mm:ss.SSS'Z'", timezone="GMT")
private Date date;

```

3. 扁平化对象
@JsonUnwrapped：扁平对象之后
```java
@Getter
@Setter
@ToString
public class Account {
    @JsonUnwrapped
    private Location location;
    @JsonUnwrapped
    private PersonInfo personInfo;
    ......
}
```
```json
{
  "provinceName": "湖北",
  "countyName": "武汉",
  "userName": "coder1234",
  "fullName": "shaungkou"
}

```

## 测试相关
@ActiveProfiles一般作用于测试类上， 用于声明生效的 Spring 配置文件。
@Test声明一个方法为测试方法
@WithMockUser Spring Security 提供的，用来模拟一个真实用户，并且可以赋予权限。

```java
@Test
@Transactional
@WithMockUser(username = "user-id-18163138155", authorities = "ROLE_TEACHER")
void should_import_student_success() throws Exception {
    ......
}
```








