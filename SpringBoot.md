SpringBoot中如何打war包

1. 先在依赖中排除springboot自带的tomcat

   ```maven
   <dependency>
       <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-web</artifactId>
          <exclusions>
             <exclusion>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-tomcat</artifactId>
             </exclusion>
          </exclusions>
   </dependency>
   ```

2. 添加servlet依赖

   ```maven
   <dependency>
      <groupId>jakarta.servlet</groupId>
      <artifactId>jakarta.servlet-api</artifactId>
      <scope>provided</scope>
   </dependency>
   ```

3. 将打包方式改为war包

   ```maven
   <packaging>war</packaging>
   ```

4. 配置主类

   ```maven
   @SpringBootApplication
   public class DemoApplication extends SpringBootServletInitializer {  //继承专用的初始化器
       public static void main(String[] args) {
           SpringApplication.run(DemoApplication.class, args);
       }
   
     	//重写configure方法，完成启动类配置
       @Override
       protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
           return builder.sources(DemoApplication.class);
       }
   }
   ```