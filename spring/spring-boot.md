---
description: 从Spring和Spring Boot的区别开始吧！
---

# Spring Boot

## Spring和Spring Boot的区别

Spring和Spring Boot是两个相关但有一些区别的框架。

1. Spring：Spring是一个开源的Java应用程序框架，提供了一种轻量级的解决方案来开发企业级应用程序。它包含了众多的模块，如核心容器、数据访问、Web开发、AOP、消息传递等，可以根据需求选择使用。Spring框架需要进行较多的配置和集成，以便构建一个完整的应用程序。
2. Spring Boot：Spring Boot是基于Spring框架的快速开发框架，旨在简化Spring应用程序的配置和部署过程。它提供了一种约定优于配置的方式，通过自动配置和默认值来减少开发人员的工作量。Spring Boot提供了内嵌的Servlet容器，可以独立运行Spring应用程序，无需额外的服务器配置。它还提供了丰富的起步依赖（Starter Dependencies）来快速集成常用的技术栈，简化了应用程序的构建和部署。

总结起来，Spring是一个全功能的应用程序开发框架，需要手动进行配置和集成，适用于复杂的企业级应用程序开发。而Spring Boot则是一个简化的快速开发框架，通过约定优于配置的方式，提供了快速启动和部署应用程序的能力，适用于快速开发小型和中型应用程序。

需要注意的是，Spring Boot是建立在Spring框架之上的，可以将其视为Spring框架的扩展和简化。因此，使用Spring Boot仍然可以充分利用Spring框架的各种功能和模块。

## Spring Boot的自动装配原理

Spring Boot的自动装配（Auto Configuration）是通过条件化注解和类路径扫描来实现的。下面简要介绍一下Spring Boot自动装配的原理，结合源码说明：

1. 条件化注解：Spring Boot使用了大量的条件化注解来控制自动装配的行为。例如，`@ConditionalOnClass`注解表示当特定类存在于类路径上时才进行自动装配，`@ConditionalOnProperty`注解表示当特定属性被设置时才进行自动装配等。这些条件化注解被应用于自动装配的配置类和Bean定义上。
2. 自动配置类：Spring Boot定义了许多自动配置类，这些类包含了对常见应用场景的自动装配逻辑。自动配置类使用条件化注解来控制装配的条件。例如，`DataSourceAutoConfiguration`类用于自动配置数据源，`WebMvcAutoConfiguration`类用于自动配置Web MVC等。这些自动配置类通常使用`@Configuration`注解标记。
3. 类路径扫描：Spring Boot通过类路径上的`META-INF/spring.factories`文件来发现自动配置类。在该文件中，每个自动配置类都被列出，并与其关联的条件化注解一起指定。Spring Boot在启动时会扫描类路径上的`spring.factories`文件，加载自动配置类，并根据条件进行装配。
4. SpringApplication启动类：在Spring Boot应用程序的启动类中，通常使用`@SpringBootApplication`注解，该注解是一个组合注解，包含了`@Configuration`、`@EnableAutoConfiguration`和`@ComponentScan`注解。`@EnableAutoConfiguration`注解用于启用自动配置功能，它会自动扫描并加载类路径上的自动配置类。

总的来说，Spring Boot的自动装配原理是基于条件化注解和类路径扫描的机制。通过条件化注解控制装配的条件，并通过类路径扫描发现和加载自动配置类，从而实现自动装配的功能。这样，开发人员可以在Spring Boot应用程序中使用默认的自动配置，也可以通过自定义配置来覆盖和扩展默认的自动配置行为。

## 自定义Spring Boot Starter模块

以下是一个简单的示例代码，展示如何创建一个自定义的Spring Boot Starter模块：

1. 创建一个Maven或Gradle项目，并添加所需的依赖。
2. 创建一个自动配置类，该类需要使用`@Configuration`和`@ConditionalOnClass`注解进行标记，并实现必要的自动配置逻辑。以下是一个示例：

```java
@Configuration
@ConditionalOnClass({MyService.class})
@EnableConfigurationProperties(MyProperties.class)
public class MyAutoConfiguration {
    private final MyProperties properties;

    public MyAutoConfiguration(MyProperties properties) {
        this.properties = properties;
    }

    @Bean
    @ConditionalOnMissingBean
    public MyService myService() {
        return new MyService(properties.getMessage());
    }
}

```

3. 创建一个属性类，用于配置自定义Starter模块的属性。以下是一个示例：

```java
@ConfigurationProperties(prefix = "my")
public class MyProperties {
    private String message;

    // Getter和Setter方法
}

```

4. 在`src/main/resources`目录下创建一个`META-INF/spring.factories`文件，用于声明自动配置类。文件内容如下：

```java
org.springframework.boot.autoconfigure.EnableAutoConfiguration=com.example.MyAutoConfiguration
```

5. 打包你的Starter模块，并将其发布到本地或远程仓库供其他项目使用。

以上示例中，我们创建了一个名为`my-spring-boot-starter`的Starter模块，它包含了自动配置类`MyAutoConfiguration`和属性类`MyProperties`。在自动配置类中，我们通过`@ConditionalOnClass`注解指定了一个类`MyService`，当该类在类路径中存在时，自动配置将生效。在属性类中，我们使用了`@ConfigurationProperties`注解来绑定配置文件中的属性。

