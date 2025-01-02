**模拟spring加载**

- 包扫描

  - @ComponentScan

    在容器中

  - BeanDefinition对象

- 实例化bean

  - spring启动器(应该叫这个不确定) 在其构造方法内获取扫描的bean，封装好BeanDefinition对象存入beanDefinitionMap中

    在构造方法中可以先遍历这个BeanDefinitionMap，并根据创建bean的模式来创建单例bean还是多例bean，将创建好的单例bean存入singletonMap中。

  - creatBean()
    - 通过BeanDenfinition对象来实例化bean。
    - 创建bean时，判断该bean中是否有要注入的对象，若有则将该对象注入
  - getBean()
    - 先从singletonMap中通过beanName尝试获取该bean，若该bean已经存在则直接返回，若不存在则通过BeanDefinition来示例化bean，并存入singletonMap中。
    - 如果bean是多例模式那么每次getBean时都通过createBean()方法来示例化一个新的bean。

**spring依赖注入**

@Autowired

在spring创建bean的过程中会判断该bean中有没有被注入其他对象，会在createBean的时候将该bean注入

先根据类型找到bean ，再根据名字查找是否有对应的bean

**BeanPostProcess机制**

在spring中允许程序员通过编码的方式对实例化bean的时候进行一些前置和后置操作。

在BeanPostProcess接口中提供了以下两种方法：

- postProcessBeforeInstantiation  ：前置操作
- postProcessAfterInstantiation  ： 后置操作

我们可以自定义一些处理类根据自己的需求来实现这两个方法

​			

.class  --> 推断构造方法--》普通对象 --》 依赖注入 --》 初始化前--》初始化--》初始化后（AOP）--》放入单例池Map





推断构造方法

- 先判断要使用哪一个构造方法
- 再判断入参类型 （先byType，再byName）

有无参构造方法使用无参构造方法，有多个有参时会报错

**AOP**

AOP在初始化后这个阶段实现代理，但是因为初始化后不会再次的去做依赖注入，那么我们如果代理过程中使用super.test()调用父类的方法时，仍然拿不到被代理类中注入的Bean，所以代理时会提供一个被代理Bean类型的 target，在代理该对象时，会将初始化前实例化的普通对象赋值给target，这样我们通过target来拿到的被注入的bean就是有值的。

**循环依赖**

creatingBean 用来存储正在创建中的bean，从而判断是否出现循环依赖

先来了解一下什么是循环依赖：

假设现在有A、B两个Bean，在spring容器初始化时会加载这些Bean，在加载A的过程中发现A中注入了B，那么spring就会去加载B，但是在加载B的过程中又发现B中注入了A，此时又去，加载A，由于容器中此时并没有实际加载好这两个Bean无法完成依赖注入，所以此时就会陷入循环。这就是所说的循环依赖，如下面两个Bean所示:

```java
@Component
public class A { 
    
    @Autowired
    private B b;
   
}

@Component
public class B { 
    
    @Autowired
    private A a;
   
}
```

为了解决循环依赖问题，Spring使用了三级缓存，

下面来梳理一下整个Bean生命周期如何解决循环依赖

- 第一级缓存：singletonObjects（单例池）
- 第二级缓存：earlySingletonObjects
- 第三级缓存：singletonFactories

creatingSet集合是用来判断是否发生了循环依赖。

在实例化A之前会先将A这个Bean的名称存入creatingSet集合中

 `creatingSet.set('a'); `

1、实例化A

​	这一步骤是会有一个将创建好的原始的普通Bean存入三级缓存的操作，`singletonFactories.put("",Factories);`   (存入三级缓存的目的是为了打破循环，且如果依赖注入过程中发生了循环依赖则需要提前对A进行代理，而代理就是要使用该处三级缓存中的原始普通对象)

2、依赖注入  

> - 去单例池中获取B，如果单例池中没有，则进入创建B的阶段。
>
>   B   的生命周期
>
> - 先往creatingSet中插入该Bean的名称，表示该Bean正在创建过程中
>
> - 实例化  
>
> - B依赖注入
>
>   - 注入a：  先去单例池找，如果能找到则直接填充到a  
>   - 如果单例池中没有A的Bean，则判断creatingSet集合中有没有a（这个a是A 类型的bean的名称），如果有则表明发生了循环依赖。
>   - 发生循环依赖时先去二级缓存`earlySingletonObjects`中找对应A的Bean，如果找到则填充到a
>   - 如果未找到则继续去三级缓存`singletonFactories`找A的Bean（三级缓存中必定能找到对应的Bean，因为在第一个Bean开始创建的时候，就已经将原始的普通对象存入此集合中）
>   - 获取到A的bean后进行AOP  （此处进行了提前AOP，正常情况下如果要代理的话是在初始化后阶段）
>   - AOP后得到A的代理对象 填充到a，将该代理对象存入第二级缓存`earlySingletonObjects`中，从三级缓存`singletonFactories`中remove掉对应bean（实际是存了一个lambda表达式，通过lambda表达式获取的Bean）
>
> - 填充其他属性
>
> - 初始化后阶段的AOP（如果依赖注入时进行了提前AOP代理，则此处会不在进行代理
>
> - 将实例化好的Bean存入单例池
>
> - creatingSet.remove('b')

3、初始化

4、AOP

5、添加到单例池

6、creatingSet.remove('a')

经过以上过程之后不难看出：

- 一级缓存存储的是已经初始化好的Bean
- 二级缓存存储的是 Bean的代理对象（AOP的情况下），未使用AOP的情况下存储的还是三级缓存中 的原始普通Bean
- 三级缓存存储的是最原始的普通Bean



**@Lazy注解**

Lazy注解怎么解决循环依赖：加入@Lazy注解后，如果注入对象时会先产生一个代理对象来填充属性（我姑且认为这个代理对象充当占位符的作用，因为没有实际的作用，仅仅是填充了这个属性，而最终使用时还是会去生成他真正的对象Bean）

还是用上面依赖循环的例子

```java
@Component
public class A { 
    
    @Autowired
    private B b;
   
}

@Component
public class B { 
    
    @Lazy
    private A a; //代码运行到这里时，会先生成一个A的代理对象填充给a
    
    /**
    	在完成B的实例化后，继续进行A的实例化。
    	至于为什么实例化 加入lazy注解后生成代理对象，而实际使用时又填装了真正的A的Bean，这个我不得而知，因为我也没看
    */
   
}
```



**spring整合mybatis的原理**



mybatis在使用时，会通过一个FactoryBean来生成Mapper的代理类



如何将mybatis生成的代理类成为一个Bean：

这里先用jdk生成一个代理对象来简单理解一下

```java
@Component
public class TestFactoryBean implements FactoryBean {
    /*
    FactoryBean接口是设计来生成Mapper的代理对象，其中定义了三个方法，getObject用于生成代理类，getObjectType 是获取代理类的类型
    还有一个方法是生成Bean的形式，默认是单例的
     */
    @Override
    public Object getObject() throws Exception {
        Object proxyInstance = Proxy.newProxyInstance(TestFactoryBean.class.getClassLoader(), new Class[]{UserMapper.class}, new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                System.out.println(method.getName());
                return null;
            }
        });
        return proxyInstance;
    }

    @Override
    public Class<?> getObjectType() {
        return UserMapper.class;
    }
}



public class App 
{
    public static void main( String[] args )
    {

        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);

        UserService userService = (UserService) applicationContext.getBean("userService");
        //在这里 获取到实现FactoryBean接口的类的Bean时，如果是按名称获取，则是获取到重写方法中getObject中的代理类
        //如果在名称前面加入一个&符，则是拿到该类原本的Bean。
        System.out.println(applicationContext.getBean("&testFactoryBean"));
        //输出结果为：com.lzxFactoryBean.TestFactoryBean@1e8ab90f
        System.out.println(applicationContext.getBean("testFactoryBean"));
        //输出结果为：null 


//        userService.test();
    }
}
```

如何拿到mybatis生成的代理对象

需要通过mybatis的sqlSession来获取到mybatis生成的代理对象

```java
@Component
public class TestFactoryBean implements FactoryBean {
	
    private SqlSession sqlSession;
    
    @Autowried
    private void setSqlSession(SqlSessionFactory sqlSessionFactory){
        this.sqlSession=sqlSessionFactory.openSession;
    }
    
    @Override
    public Object getObject() throws Exception {
        Object proxyInstance = Proxy.newProxyInstance(TestFactoryBean.class.getClassLoader(), new Class[]{UserMapper.class}, new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                System.out.println(method.getName());
                return null;
            }
        });
        return proxyInstance;
    }

    @Override
    public Class<?> getObjectType() {
        return UserMapper.class;
    }
}
```





spring扫描

spring扫描入口

​	一般初始化容器启动类时，会传入一个配置类，当容器启动时，spring会先去判断该配置类上是否有@Conguration注解，如果该配置类上没有该注解，那么如果有{Compoent,CompoentScan,Import,ImportResource}这四个注解的其中一个，那么Spring也会认为这是一个配置类。又或者配置类上没有任何注解，但是如果类中有方法配了@Bean注解，那么也会认为这个类是一个配置类

- proxyMode

- ExcludeFilter：排除果滤器 

  > 在@Component注解扫描时，可以使用excludeFilters属性来排除一些不想加载的文件
  >
  > ![image-20250102220220450](D:\TXT\图片文件\image-20250102220220450.png)
  >
  > 在扫描时，spring会排除配置类，因为我们加载时是直接将该类传给容器，不需要再次扫描加载。并且spring在扫描后还有一个查重操作，以免扫描加载同样的Bean

- beanName生成机制，可以自定义配置一个名字生成器的类来实现BeanNameGenerator接口，如果不自定义则使用Spring默认的生成规则。

- IncludeFilter

- LazyInit

  > 配置为true后 ，扫描加载的bean都是懒加载的，如果自己单独在bean上配置了其他方式，那么遵循就近原则



ASM机制

 先读出所有的.class文件 ：

ASM下有一个ClassReader类能够解析对应的类
