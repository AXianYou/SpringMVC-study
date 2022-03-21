# 一、SpringMVC概述：

 MVC： 模型（dao、service） 视图（jsp） 控制层（Servlet）



![image-20220309154310009](../java%E8%BD%AF%E4%BB%B6/TyporaPhoto/image-20220309154310009.png)

1、  配置springmvc的核心文件   在web.xml中 

2、编写springmvc-xml 文件

3、 编写Controller 文件

4、 配置tomcat 进行测试

## 步骤：

1、 导入相关依赖： 确保有springmvc依赖

```xml
 <!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.22</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.3.15</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
            <version>2.5</version>
        </dependency>
```

2、导入相关依赖（  确保在运行tomcat时不会报错，找不到相关依赖）

![image-20220309154729528](../java%E8%BD%AF%E4%BB%B6/TyporaPhoto/image-20220309154729528.png)

![image-20220309154841710](../java%E8%BD%AF%E4%BB%B6/TyporaPhoto/image-20220309154841710.png)

3、在web.xml中配置DispatchServlet  

- 有一个<servlet>时，对应的有一个<servlet-mapping>
- 在servlet中   将DispatchServlet 绑定SpringMVC配置文件 (contextConfigLocation)
- 增加 启动级别 <load-on-startup>1</load-on-startup>   为1
- 添加springmvc 的乱码过滤问题

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

<!--    配置注册DispatchServlet  这个是SpringMVC的核心  请求分发器（前端控制器）-->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
<!--       DispatchServlet 要绑定springmvc的配置文件-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
        </init-param>

<!--        启动级别 ：1-->
        <load-on-startup>1</load-on-startup>
    </servlet>
<!--   <url-pattern>/</url-pattern>  中  “  /  ”  和“  /*  ”差别
   / :  只匹配所有的请求，不会匹配相应的jsp页面
   /* :   会匹配所有的请求，包括jsp页面
   -->
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    
    <!--  配置springmvc  乱码过滤问题  -->
    <filter>
        <filter-name>encoding</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encoding</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>


</web-app>
```

4、配置springmvc-servlet 文件、

基本架构：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
    
</beans>
```



- 核心三要素：

  - 处理器映射器

  ```xml
  <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
  ```

  - 处理器适配器

  ```xml
  <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
  ```

  - 视图解析器

  ```xml
  <!--视图解析器:DispatcherServlet给他的ModelAndView-->
  <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
      <!--前缀-->
      <property name="prefix" value="/WEB-INF/jsp/"/>
      <!--后缀-->
      <property name="suffix" value=".jsp"/>
  </bean>
  ```

  总结：

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
                             http://www.springframework.org/schema/beans/spring-beans.xsd">
  
      <!--    处理器映射器-->
  
      <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
      <!--    处理器适配器-->
  
      <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
      <!--    视图解析器-->
  
      <!--视图解析器:DispatcherServlet给他的ModelAndView-->
      <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
          <!--前缀-->
          <property name="prefix" value="/WEB-INF/jsp/"/>
          <!--后缀-->
          <property name="suffix" value=".jsp"/>
      </bean>
  </beans>
  ```

5、创建Controller 文件：

- 继承 Controller接口  实现接口  重写相应的方法   
- 创建一个ModeAndView   并返回一个ModeAndView 

```java
public class HelloController implements Controller {

    /**
     *功能描述:
     * 完成视图跳转
     * @param request request
     * @param response response
     * @return ModelAndView
     * @author zhoubo
     * @date 2022/3/9
    */
    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
        ModelAndView modelAndView = new ModelAndView();
        // 业务代码
        String result = "HelloSpringMVC";
        
        modelAndView.addObject("msg",result);
        //视图跳转 (跳转视图的名字）
        modelAndView.setViewName("test");
        
        return modelAndView;
    }
```

6、最后在springmvc-servlet中进行注册bean

```xml
<bean id="/hello" class="com.zhou.controller.HelloController"/>
```

附加：文件过滤：

```xml
<build>
   <resources>
       <resource>
           <directory>src/main/java</directory>
           <includes>
               <include>**/*.properties</include>
               <include>**/*.xml</include>
           </includes>
           <filtering>false</filtering>
       </resource>
       <resource>
           <directory>src/main/resources</directory>
           <includes>
               <include>**/*.properties</include>
               <include>**/*.xml</include>
           </includes>
           <filtering>false</filtering>
       </resource>
   </resources>
</build>
```





**乱码问题：**

创建 filter包   创建EncodingFilter  实现Filter接口	

注意：不要导错包![image-20220317143600118](../java%E8%BD%AF%E4%BB%B6/TyporaPhoto/image-20220317143600118.png)

```java
@Override
public void init(FilterConfig filterConfig) throws ServletException {

}

@Override
public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
    
    servletRequest.setCharacterEncoding("utf-8");
    servletResponse.setCharacterEncoding("utf-8");

    filterChain.doFilter(servletRequest,servletResponse);
}

@Override
public void destroy() {

}
```

写完之后去web.xml中进行配置文件

```xml
<filter>
    <filter-name>encoding</filter-name>
    <filter-class>com.zhou.filter.EncodingFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>encoding</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```



# 二、JSON

## 介绍：

- 简单理解为 是一种数据交换的格式，是JavaScript 对象的字符串表示方法 

```html
<script type="text/javascript">
    // 编写一个javaScript对象
    var user = {
        name: "秦将",
        age : 3,
        sex : "男"
    }
    console.log(user)


    // 将js对象转换成为json对象
    var s = JSON.stringify(user);
    console.log(s)

    //将 JSON对象转换为JavaScript 对象
    var parse = JSON.parse(s);
    console.log(parse)


</script>
```



步骤：

1、与上述相同  先导入其余相关依赖   在此省略  新依赖为 jackson-databind

```xml
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.13.2</version>
</dependency>
```

2、在springmvc-servlet 中增加JSON乱码问题配置

```xml
<!-- JSON乱码问题配置 -->
<mvc:annotation-driven>
    <mvc:message-converters register-defaults="true">
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <constructor-arg value="UTF-8"/>
        </bean>
        <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
            <property name="objectMapper">
                <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                    <property name="failOnEmptyBeans" value="false"/>
                </bean>
            </property>
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```

UserController  部分改动

```java
@RestController
public class UserController {

    //    @ResponseBody  // 增加该注解时，他就不会走视图解析器，会直接返回一个字符串
    //   有个上面的Rest Controller 后，就不需要再写  @ResponseBody 注解
    @RequestMapping("/j1")
    public String json1() throws JsonProcessingException {

        // jackson  ObjectMapper
        ObjectMapper objectMapper = new ObjectMapper();
        // 创建一个对象
        User user = new User("qinjiang", 3, "男");

        String str = objectMapper.writeValueAsString(user);
        return str;
    }
}
```

