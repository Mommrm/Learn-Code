## #{}和${}的区别

- #{}:是sql占位符：MyBatis会将#{}替换为?号，在sql执行前会通过反射从参数中获取参数值，并将其替换为?号。
- ${}:是表达式占位符：MyBatis会将${}替换为表达式的值，在sql执行前会通过OGNL表达式从参数中获取参数值，并将其替换为表达式的值。

最重要的区别是：
#{}能防止sql注入，而${}不能。

## xml映射文件中，除了select、insert、update、delete之外，还有其他标签，它们的作用是什么？

- <sql>: 定义sql语句 （与方法类似）
- <include>: 和<sql>配合使用可以减少重复代码 （与调用方法类似）
- <selectKey>: 获取主键自增的Id值
```xml
<selectKey resultType="java.lang.Long" keyProperty="id" order="AFTER" >
    SELECT LAST_INSERT_ID()
</selectKey>
```
resultType: 定义返回值类型
keyProperty: 定义返回值属性名
order: 定义生成Id值的时机，AFTER表示在insert语句执行后生成的Id值，BEFORE表示在insert语句执行前生成的Id值。

## MyBatis动态sql是什么？有哪些动态sql？

- <if>:单条件分支判断 搭配test属性来使用 类似于if()中的()
- <choose>: 多条件分支，要搭配<when>和<otherwise>标签使用
  - <when>: 条件判断，搭配test属性来使用
  - <otherwise>: 条件不成立时执行的语句
```xml
<select id="findUser">
    select * from User where 1=1 
    <choose>
        <when test=" age != null ">
            and age > #{age}
        </when>
        <when test=" name != null ">
            and name like concat(#{name},'%')
        </when>
        <otherwise>
            and sex = '男'
        </otherwise>
    </choose>
</select>
```