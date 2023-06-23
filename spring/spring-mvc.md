---
description: 从认识SpringMVC的核心组件开始吧！
---

# Spring MVC

## Spring MVC的核心组件

Spring MVC的核心组件包括：

1. DispatcherServlet（调度器）：作为前端控制器，接收所有的HTTP请求并将其分发到相应的处理器。
2. HandlerMapping（处理器映射器）：负责将请求映射到相应的处理器（Controller）。
3. HandlerAdapter（处理器适配器）：根据处理器的类型调用相应的处理器方法，并提供统一的处理器方法调用接口。
4. Handler（处理器，也称为Controller）：处理请求的业务逻辑，接收请求并返回响应。
5. ViewResolver（视图解析器）：将逻辑视图名解析为具体的视图对象（View）。
6. View（视图）：负责渲染模型数据并生成响应结果。
7. Model（模型）：封装处理器处理请求后产生的数据，并传递给视图进行渲染。
8. Interceptor（拦截器）：拦截请求的处理过程，可以在处理器执行前后进行一些公共的预处理或后处理。
9. HandlerExceptionResolver（异常处理器）：处理请求过程中产生的异常，将其转化为合适的响应结果。
10. RequestMappingHandlerMapping：负责将@RequestMapping注解的方法映射为处理器。
11. RequestMappingHandlerAdapter：负责调用@RequestMapping注解的方法，并提供统一的处理方法调用接口。

这些核心组件协同工作，使得Spring MVC能够接收请求、调用处理器、渲染视图并返回响应，实现了基于MVC模式的Web应用开发。

## Spring MVC 工作原理 <a href="#springmvc-gong-zuo-yuan-li-le-jie-ma" id="springmvc-gong-zuo-yuan-li-le-jie-ma"></a>

Spring MVC的工作原理可以简要描述为以下几个步骤：

1. 客户端发送请求：当客户端发送一个HTTP请求到服务器时，请求会首先被DispatcherServlet接收。
2. 请求映射：DispatcherServlet通过HandlerMapping组件来确定请求对应的处理器（Controller）。
3. 处理器调用：DispatcherServlet通过HandlerAdapter组件来调用处理器的相应方法进行处理，并将请求的数据传递给处理器。
4. 处理器执行：处理器执行业务逻辑，可能包括从数据库读取数据、处理数据、调用其他服务等。
5. 视图解析：处理器将处理结果封装到Model中，并返回一个逻辑视图名。
6. 视图渲染：DispatcherServlet通过ViewResolver组件将逻辑视图名解析为具体的视图对象，用于渲染模型数据。
7. 响应生成：视图对象将模型数据渲染到响应结果中，生成最终的响应。
8. 响应返回：DispatcherServlet将响应返回给客户端。

在这个过程中，还有其他的组件参与，如拦截器（Interceptor）用于在处理器执行前后进行一些预处理和后处理操作，异常处理器（HandlerExceptionResolver）用于处理请求过程中产生的异常。

整个过程中，DispatcherServlet充当了核心的前端控制器角色，负责接收请求、分发任务、协调各个组件的工作，将请求和处理器进行绑定，并最终返回响应给客户端。通过使用不同的组件和配置，开发人员可以根据需求灵活定制Spring MVC的行为。
