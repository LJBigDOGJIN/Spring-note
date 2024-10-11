### 什么是maven？

Maven 是一个项目管理工具，可以对 Java 项目进行构建、依赖管理。

Maven 也可被用于构建和管理各种项目，例如 C#，Ruby，Scala 和其他语言编写的项目。Maven 曾是 Jakarta 项目的子项目，现为由 Apache 软件基金会主持的独立 Apache 项目（菜鸟上copy下来的，了解一下知道怎么回事就行）

每个maven工程都包含一个pom文件，位于跟目录中，包含项目构建生命周期的详细信息。通过 `pom.xml` 文件，我们可以定义项目的坐标、项目依赖、项目信息、插件信息等等配置。

对于开发者来讲maven的作用主要有这三个：

1. **项目构建**：提供标准的、跨平台的自动化项目构建方式。
2. **依赖管理**：方便快捷的管理项目依赖的资源（jar 包），避免资源间的版本冲突问题。
3. **统一开发结构**：提供标准的、统一的项目结构。

### Maven坐标

项目中的依赖和插件都可以用一个maven坐标来表示，maven坐标包括：

- groupId：定义了当前 Maven 项目隶属的组织或公司。groupId 一般分为多段，通常情况下，第一段为域，第二段为公司名称。域又分为 org、com、cn 等，其中 org 为非营利组织，com 为商业组织，cn 表示中国。以 apache 开源社区的 tomcat 项目为例，这个项目的 groupId 是 org.apache，它的域是 org（因为 tomcat 是非营利项
- artifacted：定义了当前maven项目的名称，项目的唯一标识符，对应项目跟目录的名称。
- version：定义了Maven项目当前所处的版本。
- packaging：定义了maven项目当前的打包方式，默认打包方式是jar包
- classifier：常用于区分同一POM构建的具有不同内容的构件，可以是任意字符串，附加在版本号之后

只要能提供正确的坐标，就能够从maven仓库中找到对应的依赖供我们使用。

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>easyexcel</artifactId>
    <version>3.1.1</version>
</dependency>
```

**` https://mvnrepository.com/`**:maven依赖的官网，可以找到大部分依赖的坐标

### Maven依赖

#### 依赖配置

如果使用Maven构建产生的依赖被其他项目引用，那么该构件就是其他项目的依赖。

如何在maven工程中引入依赖，如下图：

需要在POM.xml文件中引入`<dependecies><dependecies/>`标签，每一个依赖需要用`<dependecy><dependecy>`包裹

```xml
<project>
    <dependencies>
        <dependency>
            <groupId></groupId>
            <artifactId></artifactId>
            <version></version>
            <type>...</type>
            <scope>...</scope>
            <optional>...</optional>
            <exclusions>
                <exclusion>
                  <groupId>...</groupId>
                  <artifactId>...</artifactId>
                </exclusion>
          </exclusions>
        </dependency>
      </dependencies>
</project>
```

**配置说明**：

- dependencies：一个 pom.xml 文件中只能存在一个这样的标签，是用来管理依赖的总标签。
- dependency：包含在 dependencies 标签中，可以有多个，每一个表示项目的一个依赖。
- groupId,artifactId,version(必要)：依赖的基本坐标，对于任何一个依赖来说，基本坐标是最重要的，Maven 根据坐标才能找到需要的依赖。我们在上面解释过这些元素的具体意思，这里就不重复提了。
- type(可选)：依赖的类型，对应于项目坐标定义的 packaging。大部分情况下，该元素不必声明，其默认值是 jar。
- scope(可选)：依赖的范围，默认值是 compile。
- optional(可选)：标记依赖是否可选
- exclusions(可选)：用来排除传递性依赖,例如 jar 包冲突

#### 依赖范围

**classpath** 用于指定 `.class` 文件存放的位置，类加载器会从该路径中加载所需的 `.class` 文件到内存中。

Maven 在编译、执行测试、实际运行有着三套不同的 classpath：

- **编译 classpath**：编译主代码有效
- **测试 classpath**：编译、运行测试代码有效
- **运行 classpath**：项目运行时有效

Maven 的依赖范围如下：

- **compile**：编译依赖范围（默认），使用此依赖范围对于编译、测试、运行三种都有效，即在编译、测试和运行的时候都要使用该依赖 Jar 包。
- **test**：测试依赖范围，从字面意思就可以知道此依赖范围只能用于测试，而在编译和运行项目时无法使用此类依赖，典型的是 JUnit，它只用于编译测试代码和运行测试代码的时候才需要。
- **provided**：此依赖范围，对于编译和测试有效，而对运行时无效。比如 `servlet-api.jar` 在 Tomcat 中已经提供了，我们只需要的是编译期提供而已。
- **runtime**：运行时依赖范围，对于测试和运行有效，但是在编译主代码时无效，典型的就是 JDBC 驱动实现。
- **system**：系统依赖范围，使用 system 范围的依赖时必须通过 systemPath 元素显示地指定依赖文件的路径，不依赖 Maven 仓库解析，所以可能会造成建构的不可移植。

#### 依赖传递性

##### 依赖冲突

当引入同一个依赖的不同版本时（同一个groupId和artifacted），只有后引入的那个依赖生效：

```xml
<dependency>
    <groupId>in.hocg.boot</groupId>
    <artifactId>mybatis-plus-spring-boot-starter</artifactId>
    <version>1.0.48</version>
</dependency>
<!-- 只会使用 1.0.49 这个版本的依赖 -->
<dependency>
    <groupId>in.hocg.boot</groupId>
    <artifactId>mybatis-plus-spring-boot-starter</artifactId>
    <version>1.0.49</version>
</dependency>
```

项目的两个依赖同时引入了某个依赖。

```xml
依赖链路一：A -> B -> C -> X(1.0)
依赖链路二：A -> D -> X(2.0)
```

如上两个依赖链路上有两个不同版本的X，为了避免依赖重复，Maven只会选择其中的一个进行解析。

Maven在遇到这种依赖路径上重复的问题时，会遵循路径最短优先和声明顺序优先两大原则，解决冲突的过程也被称为maven依赖调节

**路径最短优先**

```sql
//由于依赖路径二上的X包路径最短，所以X2.0会被解析使用
依赖链路一：A -> B -> C -> X(1.0) // dist = 3
依赖链路二：A -> D -> X(2.0) // dist = 2
```

**声明顺序优先**：

以上最短路径优先固然能解决一定的问题，但是当两条依赖路径等长时就又会出现问题，此时已经没有最短路径。所以此时会根据依赖的声明顺序来决定使用哪个依赖，如下两条依赖路径

```xml
依赖链路一：A -> B -> X(1.0) // dist = 2
依赖链路二：A -> D -> X(2.0) // dist = 2
```

在依赖路径长度相等的前提下，在 `pom.xml` 中依赖声明的顺序决定了谁会被解析使用，顺序最前的那个依赖优胜。该例中，如果 B 的依赖声明在 D 之前，那么 X (1.0)就会被解析使用。

```xml
<!-- A pom.xml -->
<dependencies>
    ...
    dependency B
    ...
    dependency D
</dependencies>
```

##### 排除依赖

如下代码，不同版本不同链路引用

```xml
依赖链路一：A -> B -> C -> X(1.5) // dist = 3
依赖链路二：A -> D -> X(1.0) // dist = 2
```

按照之前讲的方法，肯定是解析使用X1.0的依赖版本，但是！！！这会一些问题：如果 D 依赖用到了 1.5 版本的 X 中才有的一个类，运行项目就会报`NoClassDefFoundError`错误。如果 D 依赖用到了 1.5 版本的 X 中才有的一个方法，运行项目就会报`NoSuchMethodError`错误。这种情况我们可以使用依赖排除，将低版本的依赖排除掉，这样就会加载高版本的依赖（通常情况下，都是排除低版本依赖，因为有可能会用到高版本依赖中的某些方法，还有一个原因是一般依赖的高版本都会兼容低版本的使用。）,那么如何排除依赖呢，如下代码标签：

```xml
<dependency>
    ......
    <exclusions>
      <exclusion>
        <artifactId>x</artifactId>
        <groupId>org.apache.x</groupId>
      </exclusion>
    </exclusions>
</dependency>

```

### Maven仓库

maven仓库用来存储构件，构件在maven仓库中的逻辑存储方式是坐标和依赖。每一个坐标都代表着一个构件，我们可以根据坐标来引入构件。

仓库分为远程仓库和本地仓库，所谓本地仓库可以理解为在我们本地计算机上的一个目录文件，远程仓库是由其他人或组织维护的仓库，里面存放着大量开源软件包

- 中央仓库是远程仓库的一种，是由开源组织维护的仓库，有大量的开源软件包
- 私服是一种特殊的远程 Maven 仓库，它是架设在局域网内的仓库服务，私服一般被配置为互联网远程仓库的镜像，供局域网内的 Maven 用户使用。
- 其他公共仓库：有一些公共仓库是为了加速访问（比如阿里云 Maven 镜像仓库）或者部分构件不存在于中央仓库中。

### Maven生命周期

maven规定了三个生命周期



