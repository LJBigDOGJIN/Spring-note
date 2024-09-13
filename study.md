动态代理

JDK动态代理和CGLIB动态代理

JDK动态代理：

1. 定义一个接口和他的实现类
2. 自定义Handler方法 继承InvocationHandler方法，实现其invoke方法(在invoke方法中调用被代理类的invoke，并能进行一些前置或者后置操作)
3. Proxy.newInstance方法获取代理对象

装饰器模式

可以在不改变原有对象的情况下拓展其功能

