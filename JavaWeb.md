# Springboot

## 📖 目录

1. [Springboot 概述](#1-springboot-概述)
2. [Spring 核心概念（IOC / DI）](#2-spring-核心概念ioc--di)
3. [AOP（面向切面编程）](#3-aop面向切面编程)
4. [分层架构](#4-分层架构)
5. [RESTful 风格](#5-restful-风格)
6. [Springboot 常用注解](#6-springboot-常用注解)

---

## 1. Springboot 概述

Springboot 是基于 Spring 框架的**快速开发脚手架**，核心解决 Spring 繁琐配置、依赖版本冲突问题。

### 1.1 两大核心特性

**① 自动配置（AutoConfiguration）**

根据引入的依赖自动装配 Bean，无需大量 XML / 注解配置。

**② 起步依赖（Starter）**

封装一套场景所需依赖，统一版本，一键引入。起步依赖是 SpringBoot 封装好的**场景化依赖包组合**，解决原生 Spring 两大痛点：

1. 手动导入一堆相关 jar，容易漏包、多包；
2. 各个 jar 版本不匹配，频繁出现版本冲突。

> 一句话：**做某一类开发，只需要引入一个 starter，所有配套依赖自动全部导入，版本统一由父工程管理，不用自己写版本号。**

#### Springboot 优点

- 简化配置
- 快速开发

---

## 2. Spring 核心概念（IOC / DI）

### 2.1 IOC（控制反转）

**控制反转**：Inversion Of Control，简称 **IOC**。对象的创建控制权由程序自身转移到外部（容器），这种思想称为控制反转。

- 对象的创建权由程序员主动创建转移到容器（由容器创建、管理对象）。这个容器称为：IOC 容器或 Spring 容器。

### 2.2 DI（依赖注入）

**依赖注入**：Dependency Injection，简称 **DI**。容器为应用程序提供运行时，所依赖的资源，称之为依赖注入。

- 程序运行时需要某个资源，此时容器就为其提供这个资源。
- 例：EmpController 程序运行时需要 EmpService 对象，Spring 容器就为其提供并注入 EmpService 对象。

---

## 3. Spring的第二大核心：AOP（面向切面编程）

### 3.1 什么是 AOP

AOP（Aspect Oriented Programming）—— 面向切面编程，就是面向特定方法编程。

### 3.2 AOP 的优势

1. **减少重复代码**：不需要在业务方法中定义大量的重复性的代码，只需要将重复性的代码抽取到 AOP 程序中即可。
2. **代码无侵入**：在基于 AOP 实现这些业务功能时，对原有的业务代码是没有任何侵入的，不需要修改任何的业务代码。
3. **提高开发效率**
4. **维护方便**

### 3.3 AOP 核心概念

| 概念 | 说明 |
|------|------|
| **JoinPoint（连接点）** | 可以被 AOP 控制的方法（暗含方法执行时的相关信息） |
| **Advice（通知）** | 抽取出的共性功能（最终体现为一个方法） |
| **PointCut（切入点）** | 匹配连接点的条件，通知仅会在切入点方法执行时被应用 |
| **Aspect（切面）** | 通知 + 切入点的组合，描述 AOP 程序需要针对哪个原始方法、在什么时候执行什么操作 |
| **Target（目标对象）** | 通知所应用的对象 |

> **连接点**：大部分业务方法都是可以被 AOP 控制的方法。在 SpringAOP 提供的 JoinPoint 当中，封装了连接点方法在执行时的相关信息。
>
> **通知**：在 AOP 中只需要将重复的代码逻辑抽取出来单独定义，这部分抽取出来的重复逻辑就是共性功能。
>
> **切入点**：在 AOP 开发中，通常会通过一个**切入点表达式**来描述切入点。
>
> **切面**：切面所在的类称为切面类（被 `@Aspect` 注解标识的类）。

### 3.4 通知类型

![[aop_type.png]]

### 3.5 切入点表达式

![[joinpoint.png|495]]

#### 3.5.1 execution

```java
execution(访问修饰符?  返回值  包名.类名.?方法名(方法参数) throws 异常?)
```

其中带 `?` 的表示可以省略的部分：

- 访问修饰符：可省略（比如：public、protected）
- 包名.类名：可省略
- throws 异常：可省略（注意是方法上声明抛出的异常，不是实际抛出的异常）

**切入点表达式语法规则：**

1. 方法的访问修饰符可以省略
2. 返回值可以使用 `*` 号代替（任意返回值类型）
3. 包名可以使用 `*` 号代替，代表任意包（一层包使用一个 `*`）
4. 使用 `..` 配置包名，标识此包以及此包下的所有子包
5. 类名可以使用 `*` 号代替，标识任意类
6. 方法名可以使用 `*` 号代替，表示任意方法
7. 可以使用 `*` 配置参数，一个任意类型的参数
8. 可以使用 `..` 配置参数，任意个任意类型的参数

示例：

```java
// 匹配 DeptServiceImpl 类中以 find 开头的方法
execution(* com.*.service.impl.DeptServiceImpl.find*(..))
```

#### 3.5.2 @annotation

步骤：

1. 编写自定义注解
2. 在业务类要做为连接点的方法上添加自定义注解

**示例：**

```java
// 编写自定义注解
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface LogOperation {}
```

```java
// 业务类
@Slf4j
@Service
public class DeptServiceImpl implements DeptService {
    @Autowired
    private DeptMapper deptMapper;

    @Override
    @LogOperation // 自定义注解（表示：当前方法属于目标方法）
    public List<Dept> list() {
        // do something
    }
    
    @Override
    @LogOperation
    public void delete(Integer id) {}
    
    @Override
    public void save(Dept dept) {}
}
```

```java
// 切面类
@Slf4j
@Component
@Aspect
public class MyAspect6 {
    // 前置通知
    @Before("@annotation(com.itheima.anno.LogOperation)")
    public void before(){
        log.info("MyAspect -> before ...");
    }
    
    // 后置通知
    @After("@annotation(com.itheima.anno.LogOperation)")
    public void after(){
        log.info("MyAspect -> after ...");
    }
    
    @Around("@annotation(com.itheima.anno.LogOperation)") 
    public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable { 
        log.info("around before ..."); 
        // 调用目标对象的原始方法执行 
        Object result = proceedingJoinPoint.proceed();
        // 原始方法如果执行时有异常，环绕通知中的后置代码不会在执行了 
        log.info("around after ..."); 
        return result; 
    }
}
```

---

## 4. 分层架构

| 分层 | 说明 |
|------|------|
| **Controller** | 控制层。接收前端发送的请求，对请求进行处理，并响应数据。 |
| **Service** | 业务逻辑层。处理具体的业务逻辑。 |
| **Dao** | 数据访问层（Data Access Object），也称为持久层。负责数据访问操作，包括数据的增、删、改、查。 |

---

## 5. RESTful 风格

通过 **URL 定位要操作的资源**，通过 **HTTP 动词（请求方式）** 来描述具体的操作。

在 REST 风格的 URL 中，通过四种请求方式来操作数据的增删改查：

| HTTP 动词 | 操作 |
|-----------|------|
| `GET` | 查询 |
| `POST` | 新增 |
| `PUT` | 修改 |
| `DELETE` | 删除 |

> 基于 REST 风格定义 URL，URL 将会**更加简洁、更加规范**。

---

## 6. Springboot 常用注解

### 6.1 核心注解（启动类）

| 注解 | 说明 |
|------|------|
| `@SpringBootApplication` | **启动类核心注解**，组合了 `@Configuration` + `@EnableAutoConfiguration` + `@ComponentScan`，通常标注在主启动类上 |

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

### 6.2 分层注解（Bean 注册）

| 注解 | 分层 | 说明 |
|------|------|------|
| `@Controller` | Controller | 标注该类为 Spring MVC 的 Controller |
| `@RestController` | Controller | `@Controller` + `@ResponseBody` 组合，方法默认返回 JSON，无需额外注解 |
| `@Service` | Service | 标注业务逻辑层组件 |
| `@Repository` | Dao | 标注数据访问层组件（一般用 MyBatis 的 `@Mapper` 代替） |
| `@Component` | 通用 | 标注为 Spring 管理的普通组件，当类不属于以上三层时使用 |
| `@Mapper` | Dao | MyBatis 的 Mapper 接口注解，标识数据访问接口 |

### 6.3 请求映射注解（接收请求）

| 注解 | 说明 |
|------|------|
| `@RequestMapping` | 通用请求映射，可指定 URL、请求方法、参数等 |
| `@GetMapping` | 简化 `@RequestMapping(method = RequestMethod.GET)` |
| `@PostMapping` | 简化 `@RequestMapping(method = RequestMethod.POST)` |
| `@PutMapping` | 简化 `@RequestMapping(method = RequestMethod.PUT)` |
| `@DeleteMapping` | 简化 `@RequestMapping(method = RequestMethod.DELETE)` |

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    @GetMapping("/{id}")
    public User get(@PathVariable Integer id) { ... }
    
    @PostMapping
    public User add(@RequestBody User user) { ... }
    
    @PutMapping("/{id}")
    public User update(@PathVariable Integer id, @RequestBody User user) { ... }
    
    @DeleteMapping("/{id}")
    public void delete(@PathVariable Integer id) { ... }
}
```

### 6.4 参数绑定注解

| 注解 | 说明 |
|------|------|
| `@PathVariable` | 获取 URL 路径中的参数（RESTful 风格） |
| `@RequestParam` | 获取查询参数（?key=value），可指定是否必需、默认值 |
| `@RequestBody` | 获取请求体中的 JSON 数据，自动反序列化为 Java 对象 |
| `@RequestHeader` | 获取请求头中的参数 |
| `@CookieValue` | 获取 Cookie 值 |
| `@ModelAttribute` | 绑定表单数据到 Model，或用于方法参数/方法级别 |

```java
@GetMapping("/users")
public Result list(@RequestParam(defaultValue = "1") Integer page,
                   @RequestParam(defaultValue = "10") Integer pageSize) { ... }
```

### 6.5 依赖注入注解

| 注解 | 说明 |
|------|------|
| `@Autowired` | Spring 提供的依赖注入，按**类型**自动装配 |
| `@Resource` | JSR-250 规范，按**名称**自动装配（也可按类型） |
| `@Qualifier` | 配合 `@Autowired` 使用，指定具体 Bean 名称注入 |
| `@Primary` | 当存在多个同类型 Bean 时，优先注入标注了 `@Primary` 的 Bean |

> **推荐**：`@Autowired` 用于单实现，`@Resource` 或 `@Autowired` + `@Qualifier` 用于多实现。

### 6.6 配置注解

| 注解 | 说明 |
|------|------|
| `@Configuration` | 标注该类为配置类，相当于 XML 配置文件 |
| `@Bean` | 标注在方法上，将返回值注册为 Spring Bean |
| `@Value` | 读取 `application.properties`/`yml` 中的单个配置值 |
| `@ConfigurationProperties` | 批量绑定配置文件中的属性到 POJO 类 |
| `@PropertySource` | 指定加载的自定义配置文件路径 |
| `@Import` | 导入额外的配置类或普通类到 IOC 容器 |

```java
@Configuration
@ConfigurationProperties(prefix = "aliyun.oss")
@Data
public class OssConfig {
    private String endpoint;
    private String accessKeyId;
    private String accessKeySecret;
    private String bucketName;
}
```

### 6.7 AOP 通知注解

| 注解 | 说明 |
|------|------|
| `@Before` | 前置通知，在目标方法执行前执行 |
| `@After` | 后置通知，在目标方法执行后执行（无论是否异常） |
| `@AfterReturning` | 返回通知，目标方法正常返回后执行 |
| `@AfterThrowing` | 异常通知，目标方法抛出异常后执行 |
| `@Around` | 环绕通知，可控制目标方法执行的前后逻辑及是否执行 |
| `@Pointcut` | 定义可复用的切入点表达式 |
| `@Aspect` | 标注该类为切面类 |

### 6.8 事务注解

| 注解 | 说明 |
|------|------|
| `@Transactional` | 声明式事务管理，标注在方法或类上，方法执行时自动开启/提交/回滚事务 |
| `@EnableTransactionManagement` | 启用事务管理（Spring Boot 中自动配置，通常无需显式添加） |

```java
@Service
public class UserService {
    @Transactional(rollbackFor = Exception.class)
    public void transfer() {
        // 事务内的多个数据库操作
    }
}
```

### 6.9 异常处理注解

| 注解 | 说明 |
|------|------|
| `@ExceptionHandler` | 在 Controller 中处理特定异常的方法 |
| `@ControllerAdvice` | 定义全局异常处理器，统一处理所有 Controller 的异常 |
| `@RestControllerAdvice` | `@ControllerAdvice` + `@ResponseBody` 组合，直接返回 JSON 响应 |

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(Exception.class)
    public Result handleException(Exception e) {
        return Result.error(e.getMessage());
    }
}
```

### 6.10 其他常用注解

| 注解 | 说明 |
|------|------|
| `@CrossOrigin` | 允许跨域请求，可标注在类或方法上 |
| `@Valid` / `@Validated` | 启用参数校验（配合 `@NotBlank`、`@NotNull` 等使用） |
| `@ResponseBody` | 将方法返回值直接写入 HTTP 响应体（JSON 格式） |
| `@ResponseStatus` | 指定 HTTP 响应状态码 |
| `@Conditional` | 条件装配，满足特定条件时才创建 Bean（如 `@ConditionalOnProperty`、`@ConditionalOnClass` 等） |
| `@Lazy` | 延迟初始化 Bean，第一次使用时才创建 |
| `@Scope` | 设置 Bean 的作用域：`singleton`（单例）、`prototype`（原型）等 |
| `@Async` | 标注方法异步执行（需配合 `@EnableAsync` 使用） |
| `@Scheduled` | 定时任务注解（需配合 `@EnableScheduling` 使用） |
| `@EnableScheduling` | 启用定时任务支持 |
| `@EnableAsync` | 启用异步方法支持 |
| `@EnableCaching` | 启用缓存支持 |
