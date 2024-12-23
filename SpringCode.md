**模拟spring加载**

- 包扫描

  - BeanDefinition对象

- 实例化bean

  - spring启动器(应该叫这个不确定) 在其构造方法内获取扫描的bean，封装好BeanDefinition对象存入beanDefinitionMap中

    在构造方法中可以先遍历这个BeanDefinitionMap，并根据创建bean的模式来创建单例bean还是多例bean，将创建好的单例bean存入singletonMap中。

  - creatBean()
    - 通过BeanDenfinition对象来实例化bean。
  - getBean()
    - 先从singletonMap中通过beanName尝试获取该bean，若该bean已经存在则直接返回，若不存在则通过BeanDefinition来示例化bean，并存入singletonMap中。
    - 如果bean是多例模式那么每次getBean时都通过createBean()方法来示例化一个新的bean。