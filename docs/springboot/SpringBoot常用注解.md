![微信图片_20200430132519](C:\Users\pc-hone\Desktop\林家小铺\微信图片_20200430132519.jpg)

### 1. `@SpringBootApplication`

我们可以把 `@SpringBootApplication`看作是 `@Configuration`、`@EnableAutoConfiguration`、`@ComponentScan` 注解的集合。

`@EnableAutoConfiguration`：启用 SpringBoot 的自动配置机制

`@ComponentScan`：扫描被`@Component` (`@Service`,`@Controller`)注解的 bean，注解默认会扫描该类所在的包下所有的类。

`@Configuration`：允许在 Spring 上下文中注册额外的 bean 或导入其他配置类

### 2. Spring Bean 相关

#### 2.1. `@Autowired`   

#### 2.2. `Component`,`@Repository`,`@Service`, `@Controller`

#### 2.3. `@RestController`

`RestController`注解是`@Controller和`@`ResponseBody`的合集    REST风格控制器

#### 2.4. `@Scope`

声明 Spring Bean 的作用域，使用方法:

```
@Bean
@Scope("singleton")
public Person personSingleton() {
    return new Person();
}
```

**四种常见的 Spring Bean 的作用域：**

- singleton : 唯一 bean 实例，Spring 中的 bean 默认都是单例的。
- prototype : 每次请求都会创建一个新的 bean 实例。
- request : 每一次 HTTP 请求都会产生一个新的 bean，该 bean 仅在当前 HTTP request 内有效。
- session : 每一次 HTTP 请求都会产生一个新的 bean，该 bean 仅在当前 HTTP session 内有效。

#### 2.5. `Configuration`

一般用来声明配置类，可以使用 `@Component`注解替代，不过使用`Configuration`注解声明配置类更加语义化。

```
@Configuration
public class AppConfig {
    @Bean
    public TransferService transferService() {
        return new TransferServiceImpl();
    }

}
```

### 3. 处理常见的 HTTP 请求类型

**5 种常见的请求类型:**

- **GET** ：请求从服务器获取特定资源。举个例子：`GET /users`（获取所有学生）
- **POST** ：在服务器上创建一个新的资源。举个例子：`POST /users`（创建学生）
- **PUT** ：更新服务器上的资源（客户端提供更新后的整个资源）。举个例子：`PUT /users/12`（更新编号为 12 的学生）
- **DELETE** ：从服务器删除特定的资源。举个例子：`DELETE /users/12`（删除编号为 12 的学生）

### 4. 前后端传值

`@PathVariable`用于获取路径参数，`@RequestParam`用于获取查询参数。 `@RequestBody`用于读取 Request 请求（可能是 POST,PUT,DELETE,GET 请求）的 body 部分

需要注意的是：**一个请求方法只可以有一个`@RequestBody`，但是可以有多个`@RequestParam`和`@PathVariable`**。如果你的方法必须要用两个 `@RequestBody`来接受数据的话，大概率是你的数据库设计或者系统设计出问题了！

### 5. 读取配置信息

######  5.1.`@Value("${property}")`



###### 5.2.通过`@ConfigurationProperties`读取配置信息并与 bean 绑定。

@Component
@ConfigurationProperties(prefix = "library")

###### `5.3.@PropertySource`读取指定 properties 文件(不常用)

```
@Component
@PropertySource("classpath:website.properties")

class WebSite {
    @Value("${url}")
    private String url;

  省略getter/setter
  ......
}
```

### 6. 参数校验

**JSR(Java Specification Requests）** 是一套 JavaBean 参数校验的标准，校验的时候我们实际用的是 **Hibernate Validator** 框架。

#### 6.1. 一些常用的字段验证的注解

- `@NotEmpty` 被注释的字符串的不能为 null 也不能为空
- `@NotBlank` 被注释的字符串非 null，并且必须包含一个非空白字符
- `@Null` 被注释的元素必须为 null
- `@NotNull` 被注释的元素必须不为 null
- `@AssertTrue` 被注释的元素必须为 true
- `@AssertFalse` 被注释的元素必须为 false
- `@Pattern(regex=,flag=)`被注释的元素必须符合指定的正则表达式
- `@Email` 被注释的元素必须是 Email 格式。
- `@Min(value)`被注释的元素必须是一个数字，其值必须大于等于指定的最小值
- `@Max(value)`被注释的元素必须是一个数字，其值必须小于等于指定的最大值
- `@DecimalMin(value)`被注释的元素必须是一个数字，其值必须大于等于指定的最小值
- `@DecimalMax(value)` 被注释的元素必须是一个数字，其值必须小于等于指定的最大值
- `@Size(max=, min=)`被注释的元素的大小必须在指定的范围内
- `@Digits (integer, fraction)`被注释的元素必须是一个数字，其值必须在可接受的范围内
- `@Past`被注释的元素必须是一个过去的日期
- `@Future` 被注释的元素必须是一个将来的日期
- `@Pattern(regexp = "((^Man$|^Woman$|^UGM$))", message = "sex 值不在可选范围")`

#### 6.2. 验证请求体(RequestBody)

```
@Data
@AllArgsConstructor
@NoArgsConstructor
**public** **class** **Person** {

  @NotNull(message = "classId 不能为空")
  **private** String classId;

  @Size(max = 33)
  @NotNull(message = "name 不能为空")
  **private** String name;

  @Pattern(regexp = "((^Man$|^Woman$|^UGM$))", message = "sex 值不在可选范围")
  @NotNull(message = "sex 不能为空")
  **private** String sex;

  @Email(message = "email 格式不正确")
  @NotNull(message = "email 不能为空")
  **private** String email;

}
```

我们在需要验证的参数上加上了`@Valid`注解，如果验证失败，它将抛出`MethodArgumentNotValidException`。

#### 6.3. 验证请求参数(Path Variables 和 Request Parameters)

```
@Valid @PathVariable("id") @Max(value = 5,message = "超过 id 的范围了") Integer id
```

### 7. 全局处理 Controller 层异常

1. `@ControllerAdvice` :注解定义全局异常处理类
2. `@ExceptionHandler` :注解声明异常处理方法

```
@ControllerAdvice
@ResponseBody
public class GlobalExceptionHandler {

    /**
     * 请求参数异常处理
     */
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<?> handleMethodArgumentNotValidException(MethodArgumentNotValidException ex, HttpServletRequest request) {
       ......
    }
}
```

### 8. JPA 相关

#### 8.1. 创建表

`@Entity`声明一个类对应一个数据库实体。

`@Table` 设置表明

```
@Entity
@Table(name = "role")
public class Role {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String description;
    省略getter/setter......
```

#### 8.2. 创建主键

`@Id` ：声明一个字段为主键。

使用`@Id`声明之后，我们还需要定义主键的生成策略。我们可以使用 `@GeneratedValue` 指定主键生成策略。

**1.通过 `@GeneratedValue`直接使用 JPA 内置提供的四种主键生成策略来指定主键生成策略。**

```
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```

JPA 使用枚举定义了 4 中常见的主键生成策略，如下：

```
public enum GenerationType {

    /**
     * 使用一个特定的数据库表格来保存主键
     * 持久化引擎通过关系数据库的一张特定的表格来生成主键,
     */
    TABLE,

    /**
     *在某些数据库中,不支持主键自增长,比如Oracle、PostgreSQL其提供了一种叫做"序列(sequence)"的机制生成主键
     */
    SEQUENCE,

    /**
     * 主键自增长
     */
    IDENTITY,

    /**
     *把主键生成策略交给持久化引擎(persistence engine),
     *持久化引擎会根据数据库在以上三种主键生成 策略中选择其中一种
     */
    AUTO
}

```

一般使用 MySQL 数据库的话，使用`GenerationType.IDENTITY`策略比较普遍一点（分布式系统的话需要另外考虑使用分布式 ID）。

**2.通过 `@GenericGenerator`声明一个主键策略，然后 `@GeneratedValue`使用这个策略**

```
@Id
@GeneratedValue(generator = "IdentityIdGenerator")
@GenericGenerator(name = "IdentityIdGenerator", strategy = "identity")
private Long id;
```

等价于：

```
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```

jpa 提供的主键生成策略有如下几种：

```
public class DefaultIdentifierGeneratorFactory
  implements MutableIdentifierGeneratorFactory, Serializable, ServiceRegistryAwareService {

 @SuppressWarnings("deprecation")
 public DefaultIdentifierGeneratorFactory() {
  register( "uuid2", UUIDGenerator.class );
  register( "guid", GUIDGenerator.class );   // can be done with UUIDGenerator + strategy
  register( "uuid", UUIDHexGenerator.class );   // "deprecated" for new use
  register( "uuid.hex", UUIDHexGenerator.class );  // uuid.hex is deprecated
  register( "assigned", Assigned.class );
  register( "identity", IdentityGenerator.class );
  register( "select", SelectGenerator.class );
  register( "sequence", SequenceStyleGenerator.class );
  register( "seqhilo", SequenceHiLoGenerator.class );
  register( "increment", IncrementGenerator.class );
  register( "foreign", ForeignGenerator.class );
  register( "sequence-identity", SequenceIdentityGenerator.class );
  register( "enhanced-sequence", SequenceStyleGenerator.class );
  register( "enhanced-table", TableGenerator.class );
 }

 public void register(String strategy, Class generatorClass) {
  LOG.debugf( "Registering IdentifierGenerator strategy [%s] -> [%s]", strategy, generatorClass.getName() );
  final Class previous = generatorStrategyToClassNameMap.put( strategy, generatorClass );
  if ( previous != null ) {
   LOG.debugf( "    - overriding [%s]", previous.getName() );
  }
 }

}
```

#### 8.3. 设置字段类型

`@Column` 声明字段。

**示例：**

设置属性 userName 对应的数据库字段名为 user_name，长度为 32，非空

```
@Column(name = "user_name", nullable = false, length=32)
private String userName;
```

设置字段类型并且加默认值，这个还是挺常用的。

```
Column(columnDefinition = "tinyint(1) default 1")
private Boolean enabled;
```

#### 8.4. 指定不持久化特定字段

`@Transient` ：声明不需要与数据库映射的字段，在保存的时候不需要保存进数据库 。

如果我们想让`secrect` 这个字段不被持久化，可以使用 `@Transient`关键字声明。

```
Entity(name="USER")
public class User {

    ......
    @Transient
    private String secrect; // not persistent because of @Transient

}
```

除了 `@Transient`关键字声明， 还可以采用下面几种方法：

```
static String secrect; // not persistent because of static
final String secrect = “Satish”; // not persistent because of final
transient String secrect; // not persistent because of transient
```

一般使用注解的方式比较多。

#### 8.5. 声明大字段

`@Lob`:声明某个字段为大字段。

```
@Lob
private String content;
```

更详细的声明：

```
@Lob
//指定 Lob 类型数据的获取策略， FetchType.EAGER 表示非延迟 加载，而 FetchType. LAZY 表示延迟加载 ；
@Basic(fetch = FetchType.EAGER)
//columnDefinition 属性指定数据表对应的 Lob 字段类型
@Column(name = "content", columnDefinition = "LONGTEXT NOT NULL")
private String content;
```

#### 8.6. 创建枚举类型的字段

可以使用枚举类型的字段，不过枚举字段要用`@Enumerated`注解修饰。

```
public enum Gender {
    MALE("男性"),
    FEMALE("女性");

    private String value;
    Gender(String str){
        value=str;
    }
}
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

数据库里面对应存储的是 MAIL/FEMAIL。

#### 8.7. 删除/修改数据

`@Modifying` 注解提示 JPA 该操作是修改操作,注意还要配合`@Transactional`注解使用。

```
@Repository
public interface UserRepository extends JpaRepository<User, Integer> {

    @Modifying
    @Transactional(rollbackFor = Exception.class)
    void deleteByUserName(String userName);
}
```

[SpringBoot常用注解大全](https://mp.weixin.qq.com/s/Lq_iBz9cV9g11OvAwFmo-A)