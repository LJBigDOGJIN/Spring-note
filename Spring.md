#### 什么是Spring框架？

spring框架是一款开源的轻量级java开发框架，由多个模块组成，易上手，开箱即用，支持大量插件配置。 Spring 支持 IoC（Inversion of Control:控制反转） 和 AOP(Aspect-Oriented Programming:面向切面编程)、可以很方便地对数据库进行访问、可以很方便地集成第三方组件（电子邮件，任务，调度，缓存等等）、对单元测试支持比较好、支持 RESTful Java 应用程序的开发。

**spring framwork 核心是ioc和aop**

#### Spring的主要模块

![image-20240910194935923](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240910194935923.png)

各个模块的依赖关系

![image-20240910195328585](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240910195328585.png)

**Core Container**

Spring的核心模块，主要提供IOC依赖注入功能的支持，Spring的其他模块基本都需要依赖此模块

- **spring-core**：Spring框架的核心工具类
- **spring-beans**：提供对bean的创建、配置和管理等功能的支持
- **spring-context**：提供对国际化、事件传播、资源加载等功能的支持
- **spring-expression**：提供对表达式语言（Spring Expression Language） SpEL 的支持，只依赖于 core 模块，不依赖于其他模块，可以单独使用。

**AOP**

- **spring-aspect**：提供对集成AspectJ的支持
- **spring-aop**：提供了面向切面的编程实现
- **spring-instrument**：提供了为JVM添加代理的功能。 具体来讲，它为 Tomcat 提供了一个织入代理，能够为 Tomcat 传递类文 件，就像这些文件是被类加载器加载的一样。

**Data Access/Integeration**

- **spring-jdbc**：提供了对数据操作的抽象，不同的数据库都有不同的API，而Java程序只用和JDBC API交互，这样就消除了不同数据库间的操作差异
- **spring-tx**：提供对事务的支持
- **spring-orm**：提供对ORM框架的支持，例如hibernate、mybatis等
- **spring-oxm**：提供一个抽象层支撑 OXM(Object-to-XML-Mapping)，例如：JAXB、Castor、XMLBeans、JiBX 和 XStream 等。
- **spring-jms** : 消息服务。自 Spring Framework 4.1 以后，它还提供了对 spring-messaging 模块的继承。

**Spring Web**

**spring-web**：对web功能的实现提供基础支持

**spring-webmvc**：提供对Spring MVC的实现

**spring-webSocket**：提供对websocket的支持，使服务端和客户端能够进行全双工通信

**spring-webflux**：提供对 WebFlux 的支持。WebFlux 是 Spring Framework 5.0 中引入的新的响应式框架。与 Spring MVC 不同，它不需要 Servlet API，是完全异步。

#### Spring IOC

ioc（inversion of control ）是一种设计思想，将本来由程序手动创建的对象，交给spring框架来创建管理。

为什么要叫控制反转

因为创建和管理bean的职责交给了框架和ioc容器

![image-20240911130854019](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240911130854019.png)

![image-20240913160059732](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240913160059732.png)

ioc容器实际上是一个Map集合，在使用时我们不用考虑bean的各种依赖关系，当我们配置好之后，在使用bean时直接注入即可使用，大大简化了开发，提高了开发效率，不需要考虑对象是如何被创建出来的

#### 什么是Bean

Bean就是被ioc所管理的对象，我们需要告诉 IoC 容器帮助我们管理哪些对象，这个是通过配置元数据来定义的。配置元数据可以是 XML 文件、注解或者 Java 配置类。

```xml
//注册bean
<!-- Constructor-arg with 'value' attribute -->
<bean id="..." class="...">
   <constructor-arg value="..."/>
</bean>
//还有一个前置加载bean的概念，当一个beanA依赖beanB的时候，我们可以让他先加载beanB，通过depends-on来设定
<bean id="beanA" class="..."  depends-on="beanB	" />
<bean id="beanB" class="..."/>	
```

#### **Bean依赖注入方式**

- setter方法注入
- 构造器注入
- 注解自动装配

![image-20240911142722581](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240911142722581.png)

**将一个类声明称Bean的注解有哪些**

- @Component：通用注解，可以将任意类标注为Spring组件，当不知道类属于哪一层时可以使用。
- @Repository：用于Dao层的类，用于对数据的操作。
- @Service：用业务层的类，通常写复杂的业务逻辑，需要用到Dao层。
- @Controller：一般用于控制层，Spring MVC ，调用Service层的业务逻辑。

**@Component和@Bean的区别**

- component用于类上，bean用在方法上。
- component通常是通过类路径扫描来自动侦测以及自动装配到 Spring 容器中（我们可以使用 `@ComponentScan` 注解定义要扫描的路径从中找出标识了需要装配的类自动装配到 Spring 的 bean 容器中）。`@Bean` 注解通常是我们在标有该注解的方法中定义产生这个 bean,`@Bean`告诉了 Spring 这是某个类的实例。
- @Bean的自定义性要更强一些，有些情况下也只能通过@Bean注解来定义

```java
// 案例一
@Configuration
public class AppConfig {
    @Bean
    public TransferService transferService() {
        return new TransferServiceImpl();
    }
​
}
//案例二，只能通过@Bean来定义
@Bean
public OneService getService(status) {
    case (status)  {
        when 1:
                return new serviceImpl1();
        when 2:
                return new serviceImpl2();
        when 3:
                return new serviceImpl3();
    }
}
```

#### Bean自动装配

#### 注入bean的注解有哪些

- @Autowried：根据bean的类型来注入，默认情况下要求被注入的对象一定存在，如果被注入的对象为null，可以使用required属性，也可以配合使用@Qualify注解来实现按名称注入

  ```java
  @Autowired(required=false)
  ```

- @Recourse：根据bean的名字，若无法通过名称来找到bean也可以ByType

- @Inject：

#### @Autowired和@Resource的区别

两者都能够作用在setter方法上或字段上，但autowired还能作用在构造函数上，resource不能。

但@Autowired 采用的是ByType来注入对象，被注入的对象默认都是必须存在的，若被注入的对象不存在，可以使用Autowired的required=false属性来限制。也可以配合@Qualify属性来实现按名称注入对象

```java
// smsService 就是我们上面所说的名称
@Autowired
private SmsService smsService;
```

@Resource注解是属于JDK的，按照名称来注入对象，但当在容器中找不到对应名称的对象时就会按照类型来注入对象。**他有两个属性  name 和 type，如果仅指定 `name` 属性则注入方式为`byName`，如果仅指定`type`属性则注入方式为`byType`，如果同时指定`name` 和`type`属性（不建议这么做）则注入方式为`byType`+`byName`。**

```java
//Resource注解定义
public @interface Resource {
    String name() default "";
    Class<?> type() default Object.class;
}
```

```java
//假设SmsService有两个实现类smsServiceImpl1h和smsServiceImpl2，下面看一下代码
// 报错，byName 和 byType 都无法匹配到 bean
@Resource
private SmsService smsService;
// 正确注入 SmsServiceImpl1 对象对应的 bean
@Resource
private SmsService smsServiceImpl1;
// 正确注入 SmsServiceImpl1 对象对应的 bean（比较推荐这种方式）
@Resource(name = "smsServiceImpl1")
private SmsService smsService;
```



> 当一个接口有多个实现类时，用@Autowired注解通过类型来注入对象就不是那么顺利了，因为autowired会找到多个类型相同的对象，这样就导致不知道该选择哪一个。此时我们要么用@Qualify 来实现按名称注入，要么用@resource注解直接按名称注入。

```java
//假设SmsService有两个实现类smsServiceImpl1h和smsServiceImpl2，下面看一下代码
​
// 报错，byName 和 byType 都无法匹配到 bean
@Autowired
private SmsService smsService;
// 正确注入 SmsServiceImpl1 对象对应的 bean
@Autowired
private SmsService smsServiceImpl1;
// 正确注入  SmsServiceImpl1 对象对应的 bean
// smsServiceImpl1 就是我们上面所说的名称
@Autowired
@Qualifier(value = "smsServiceImpl1")
private SmsService smsService;
```

#### Bean的注入方式有哪些

- setter注入

  ```java
  @Service
  public class UserService {
  ​
      private UserRepository userRepository;
  ​
      // 在 Spring 4.3 及以后的版本，特定情况下 @Autowired 可以省略不写
      @Autowired
      public void setUserRepository(UserRepository userRepository) {
          this.userRepository = userRepository;
      }
  ​
      //...
  }
  ```

- 构造器注入

  ```java
  @Service
  public class UserService {
  ​
      private final UserRepository userRepository;
  ​
      public UserService(UserRepository userRepository) {
          this.userRepository = userRepository;
      }
  ​
      //...
  }
  ```

- Field注入

  ```java
  @Service
  public class UserService {
      @Autowired
      private UserRepository userRepository;
      //...
  }
  ​
  ```

  #### Bean的作用域

  - singleton：IoC容器中中只有唯一的对象。Spring中默认就是单例的。
  - prototype：每次获取都会获得一个新的bean，即每次获取的bean都不一样
  - request（仅web应用可用）：每一次HTTP请求都会产生一个新的bean，且该bean仅在当前HTTP请求内有效
  - session（仅web应用可用）：每次来自新session的HTTP请求都会产生一个新的bean（又叫会话bean），该bean仅在当前HTTP session内有效。
  - application/global-sessio（仅web应用可用）：每个web应用在启动时创建一个Bean，该bean仅在当前应用启动时间内有效。
  - webSocket（仅web应用可用）：每一次webSocket会话都会产生一个新的Bean

怎么配置：

xml文件方式

```xml
<bean id="..." class="..." scope="singleton"></bean>
```

注解方式

```java
@Bean
@Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public Person personPrototype() {
    return new Person();
}
```

Bean是否线程安全

安不安全要具体分析，通常说的线程安全是多线程对同一份共享资源进行操作时容易出现问题。在Spring中默认使用singleton（单例模式），即容器中只有这一个对象，独一无二的，这样在进行操作时就可能会出现线程安全问题。

这里还要说一下什么是有状态bean和无状态bean。无状态bean就是在bean对象中没有定义可变成员变量的，有状态bean就是在bean中定义了可变成员变量

```java
//有状态bean
// 定义了一个购物车类，其中包含一个保存用户的购物车里商品的 List
@Component
public class ShoppingCart {
    private List<String> items = new ArrayList<>();
​
    public void addItem(String item) {
        items.add(item);
    }
​
    public List<String> getItems() {
        return items;
    }
}
//无状态bean
// 定义了一个用户服务，它仅包含业务逻辑而不保存任何状态。
@Component
public class UserService {
​
    public User findUserById(Long id) {
        //...
    }
    //...
}
```

Bean线程安全问题解决

- 尽量设计无状态bean

- 使用ThreadLocal保存无状态bean

  > ThreadLocal是每个线程都有的,用来存储信息的独立副本,不同线程间互不影响

- 使用同步锁 sychronized和retrentLock来保证线程安全

```java
//这是一个使用threadLocal 保存成员变量的例子
public class UserThreadLocal {
​
    private UserThreadLocal() {}
​
    private static final ThreadLocal<SysUser> LOCAL = ThreadLocal.withInitial(() -> null);
​
    public static void put(SysUser sysUser) {
        LOCAL.set(sysUser);
    }
​
    public static SysUser get() {
        return LOCAL.get();
    }
​
    public static void remove() {
        LOCAL.remove();
    }
}
```

Bean的生命周期

1. Bean实例化

   Bean容器会先找到配置文件中的bean,然后通过java的反射机制来创建bean的实例.

2. Bean赋值填充

   填充bean中的值,例如通过@value给字段赋值或使用set方法等.

3. Bean初始化

4. Bean销毁

   此处销毁并非是立即销毁,而是先把销毁bean的方法记录下来,等到需要销毁时再调用该方法释放bean的资源.

   ```java
   //AbstractAutowireCapableBeanFactory 的 doCreateBean() 方法中能看到依次执行了这 4 个阶段：
   protected Object doCreateBean(final String beanName, final RootBeanDefinition mbd, final @Nullable Object[] args)
       throws BeanCreationException {
   ​
       // 1. 创建 Bean 的实例
       BeanWrapper instanceWrapper = null;
       if (instanceWrapper == null) {
           instanceWrapper = createBeanInstance(beanName, mbd, args);
       }
   ​
       Object exposedObject = bean;
       try {
           // 2. Bean 属性赋值/填充
           populateBean(beanName, mbd, instanceWrapper);
           // 3. Bean 初始化
           exposedObject = initializeBean(beanName, exposedObject, mbd);
       }
   ​
       // 4. 销毁 Bean-注册回调接口
       try {
           registerDisposableBeanIfNecessary(beanName, bean, mbd);
       }
   ​
       return exposedObject;
   }
   ```

   Aware接口能让bean获得spring容器资源

   spring中提供的aware接口主要有:

   - BeanNameAware:注入当前bean对应的beanName
   - BeanClassLoaderAware:注入加载当前bean的类加载器
   - BeanFactoryAware:注入当前BeanFactory 容器的 引用

   Bean的扩展

   beanPostProcessor为bean提供扩展点

   ```java
   public interface BeanPostProcessor {
   ​
       // 初始化前置处理
       default Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
           return bean;
       }
       // 初始化后置处理
       default Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
           return bean;
       }
   ​
   }
   ```
   
- postProcessBeforeInitialization:在bean实例化,属性注入完成之后,初始化之前
  
  `InitializingBean#afterPropertiesSet`方法以及自定义的 `init-method` 方法之前执行；
  
- postProcessAfterInitialization:在初始化之后,就是在InitializingBean#afterPropertiesSet和自定义的init-method之后
  

**`InitializingBean` 和 `init-method` 是 Spring 为 Bean 初始化提供的扩展点。**

```java
   public interface InitializingBean {
    // 初始化逻辑
       void afterPropertiesSet() throws Exception;
   }
```

```xml
   <!--指定 init-method 方法，指定初始化方法：-->
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
   ​
       <bean id="demo" class="com.chaycao.Demo" init-method="init()"/>
   ​
   </beans>
```

![image-20240912105415836](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240912105415836.png)



#### **AOP**

将一些与业务逻辑无关,但又为业务所用的代码封装,用于减少重复代码,减少了代码的耦合度

aop是基于动态代理实现的,如果要代理的对象，实现了某个接口，那么 Spring AOP 会使用 **JDK Proxy**，去创建代理对象，而对于没有实现接口的对象，就无法使用 JDK Proxy 去进行代理了，这时候 Spring AOP 会使用 **Cglib** 生成一个被代理对象的子类来作为代理.

简而言之，AOP就是在不改变原有代码的基础上进行增强（在原来的代码执行操作前后进行附加操作），也可以理解为所说的代理模式，本质上都是对代码进行增强，而且AOP是基于动态代理实现的。

![image-20240912131754896](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240912131754896.png)

AOP切面编程需要先了解下面几个概念

- **目标**:需要被通知的对象
- **代理**:向目标对象应用通知后的实现类
- **连接点**:目标对象所属的类中,所有方法都是连接点
- **切入点**:被增强的连接点(所有的切入点都是连接点,不是所有的连接点都是切入点)
- **通知**:对切入点进行增强的代码逻辑
- **切面**:切入点和通知
- **织入**:将通知应用到目标对象,进而生成代理对象的过程

![image-20240918104533796](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240918104533796.png)

##### xml文档配置形式

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
</beans>
```

##### 使用接口来实现AOP

需要实现Advice接口（有时间再回来写，我没用过这种啊）

##### 使用注解来实现AOP

先要在主启动类上添加注解(@EnableAspectJAutoProxy)开启AOP支持

```java
@EnableAspectJAutoProxy
@ComponentScan("org.example.entity")
@Configuration
public class MainConfiguration {
}
```

在增强类的类名上加@Aspect注解，并且通过@Component注解将该类注册成一个Bean，在类中可以写增强的方法并将该方法加入**切点**（也就是被增强的连接点，在通俗一点就是被增强的这个方法，目标对象所属类中的所有方法都叫连接点）。当我们想获取切入点信息时，可以通过JoinPoint 来获取

```java
@Aspect
@Component
public class TestAop{
    
    @Before("execution(* org.example.entity.Student.study())")//这里是一个el表达式
    public void beforeOp(){
        System.out.println("我是前置操作")
    }
    
    //这是一个获取接入点信息的测试方法
    @Before("execution(* org.example.entity.Student.study())")//这里是一个el表达式
    public void beforeOp(JionPoint jionPoint){
        System.out.println("参数："+Arrays.toString(point.getArgs()))
        System.out.println("我是前置操作")
    }
}
```



#### Spring MVC

MVC: model、view、controller

![image-20240912134955510](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240912134955510.png)

随着 Spring 轻量级开发框架的流行，Spring 生态圈出现了 Spring MVC 框架， Spring MVC 是当前最优秀的 MVC 框架。相比于 Struts2 ， Spring MVC 使用更加简单和方便，开发效率更高，并且 Spring MVC 运行速度更快。

MVC 是一种设计模式，Spring MVC 是一款很优秀的 MVC 框架。Spring MVC 可以帮助我们进行更简洁的 Web 层的开发，并且它天生与 Spring 框架集成。Spring MVC 下我们一般把后端项目分为 Service 层（处理业务）、Dao 层（数据库操作）、Entity 层（实体类）、Controller 层(控制层，返回数据给前台页面)。

Spring MVC 核心组件

DispatchServlet：中心处理器

HandlerMapping：处理器映射器

HandlerAdapter:适配器处理器

Handler ：请求处理器

ViewResolver：视图解析器

View：视图

![image-20240912155234458](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240912155234458.png)

1. DispatchServlet：中央处理器在接收到请求后会调用HandlerMapping，根据URL来匹配能处理的Handler
2. DispatchServlet调用HandlerAdapter执行Handler
3. handler处理完以后会返回给DispatchServlet一个ModelAndView。
4. DispatchServlet会根据逻辑View找到实际的View
5. DispatchServlet把model给view返回前端渲染

Spring中用了哪些设计模式

我知道的：

**工厂模式**

**单例模式**

**代理模式**

#### Spring中循环依赖

先搞清楚什么是循环依赖：

循环依赖就是几个Bean互相注入，例子如下：

CircularDependencyA依赖CircularDependencyB 而CircularDependencyB又依赖CircularDependencyA，如此形成闭环，互相等待对方初始化完成导致循环。

```java
@Component
public class CircularDependencyA {
    @Autowired
    private CircularDependencyB circB;
}
​
@Component
public class CircularDependencyB {
    @Autowired
    private CircularDependencyA circA;
}
```

##### 三级缓存

在Spring中 是采用三级缓存来解决循环依赖这个问题的：

事实上三级缓存就是三个存放不同时期Bean的Map集合，源码如下:

```java
// 一级缓存
/** Cache of singleton objects: bean name to bean instance. */
private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);
​
// 二级缓存
/** Cache of early singleton objects: bean name to bean instance. */
private final Map<String, Object> earlySingletonObjects = new HashMap<>(16);
​
// 三级缓存
/** Cache of singleton factories: bean name to ObjectFactory. */
private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);
​
```

- 一级缓存存放的是已经初始化好的bean对象
- 二级缓存存放的是已经实例化好的bean，但是还未经过赋值和初始化
- 三级缓存存放的是最原始的bean，objectFactory调用getObject()方法

获取Bean的流程

1. 先去singletonObjects 一级缓存中取bean，如果存在就返回
2. 如果不存在或对象正在创建中则去二级缓存earlySingletonObjects中取
3. 如果还不存在，则去三级缓存singletonFactories中获取，然后存入二级缓存中，在三级缓存中删除该bean

```java
@Nullable
    protected Object getSingleton(String beanName, boolean allowEarlyReference) {
        Object singletonObject = this.singletonObjects.get(beanName);
        if (singletonObject == null && this.isSingletonCurrentlyInCreation(beanName)) {
            singletonObject = this.earlySingletonObjects.get(beanName);
            if (singletonObject == null && allowEarlyReference) {
                synchronized(this.singletonObjects) {
                    singletonObject = this.singletonObjects.get(beanName);
                    if (singletonObject == null) {
                        singletonObject = this.earlySingletonObjects.get(beanName);
                        if (singletonObject == null) {
                            ObjectFactory<?> singletonFactory = (ObjectFactory)this.singletonFactories.get(beanName);
                            if (singletonFactory != null) {
                                singletonObject = singletonFactory.getObject();
                                this.earlySingletonObjects.put(beanName, singletonObject);
                                this.singletonFactories.remove(beanName);
                            }
                        }
                    }
                }
            }
        }
        return singletonObject;
    }
```



> Spring在创建Bean时，如果允许循环依赖，Spring就会将刚实例化完成但是属性还没初始化完的Bean对象给提前暴露出去，通过addSingletonFactory方法向三级缓存中添加一个objectFactory对象
>
> ```java
> protected void addSingletonFactory(String beanName, ObjectFactory<?> singletonFactory) {
>      Assert.notNull(singletonFactory, "Singleton factory must not be null");
>      synchronized(this.singletonObjects) {
>          if (!this.singletonObjects.containsKey(beanName)) {
>              this.singletonFactories.put(beanName, singletonFactory);
>              this.earlySingletonObjects.remove(beanName);
>              this.registeredSingletons.add(beanName);
>          }
>      }
>  }
> ```

实际上在没有AOP的情况下只用第一级缓存和第三级缓存也是能解决循环依赖的，但是如果涉及到AOP时，二级缓存就必须要了，因为即使有多次调用bean的引用的情况，也始终是使用同一个代理对象，避免了一个bean有多个代理对象的问题。

> 在第三级缓存中，如果bean没被代理则返回bean对象，如果被AOP代理则返回bean的代理对象

懒加载解决循环依赖

首先来明确一下什么是懒加载，懒加载也就是所谓延时加载，如果我们不启用懒加载时，会在项目启动时就加载好项目中所有的类等。而懒加载就是不让他在项目启动时就加载，而是在项目实际运行过程中用到的时候才加载。

非懒加载使项目启动缓慢，因为要加载大量的对象，但启动后使用时效率较快，而懒加载使得项目启动速度很快，但使用过程中需要加载类时会较慢。懒加载还有一点就是会延迟出错，比如有些对象在加载时会出现问题，非懒加载的方式在项目启动时就会将这些问题暴露出来，而懒加载在启动时不会报错，只有等用到该出错对象时才会出现问题。

当出现循环依赖时，例如下代码,A中注入了B：

```java
@Service
public class A {
    private B b;
    public A(@Lazy B b) {
        this.b = b;
    }	
}
```

当加载A时，懒加载会生成一个B的代理对象注入A中，之后开始执行 B 的实例化、初始化，在注入 B 中的 A 属性时，此时 A 已经创建完毕了，就可以将 A 给注入进去。

####  Spring事务

#### Spring任务调度





