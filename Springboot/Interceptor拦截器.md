参考资料：

拦截器基本概念与使用：https://juejin.cn/post/6844904020675559432

拦截器配置文件：https://www.cnblogs.com/wx60079/p/12841733.html

# 拦截器介绍

**拦截器(Interceptor)同** Filter 过滤器一样，它俩都是面向切面编程——AOP 的具体实现（AOP切面编程只是一种编程思想而已）。

# 作用

1. **日志**记录：记录请求信息的日志，以便进行信息监控、信息统计、计算 PV（Page View）等；
2. **权限**检查：如登录检测，进入处理器检测是否登录；
3. 性能**监控**：通过拦截器在进入处理器之前记录开始时间，在处理完后记录结束时间，从而得到该请求的处理时间。（反向代理，如 Apache 也可以自动记录）
4. 通用行为：读取 Cookie 得到用户信息并将用户对象放入请求，从而方便后续流程使用，还有如提取 Locale、Theme 信息等，只要是多个处理器都需要的即可使用拦截器实现。

# 使用

- 实现接口或继承类，重写方法

```bash
接口: org.springframework.web.servlet.HandlerInterceptor
类: org.springframework.web.servlet.handler.HandlerInterceptorAdapter
```

> `preHandler(HttpServletRequest request, HttpServletResponse response, Object handler)` 方法在请求处理之前被调用。该方法在 Interceptor 类中最先执行，用来进行一些前置初始化操作或是对当前请求做预处理，也可以进行一些判断来决定请求是否要继续进行下去。该方法的返回至是 Boolean 类型，当它返回 false 时，表示请求结束，后续的 Interceptor 和 Controller 都不会再执行；当它返回为 true 时会继续调用下一个 Interceptor 的 preHandle 方法，如果已经是最后一个 Interceptor 的时候就会调用当前请求的 Controller 方法。
>
> `postHandler(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView)` 方法在当前请求处理完成之后，也就是 Controller 方法调用之后执行，但是它会在  DispatcherServlet  进行视图返回渲染之前被调用，所以我们可以在这个方法中对 Controller 处理之后的 ModelAndView 对象进行操作。
>
> `afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handle, Exception ex)` 方法需要在当前对应的 Interceptor 类的 postHandler 方法返回值为 true 时才会执行。顾名思义，该方法将在整个请求结束之后，也就是在 DispatcherServlet  渲染了对应的视图之后执行。此方法主要用来进行资源清理。

- 配置拦截器

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LogInterceptor());

        registry.addInterceptor(new OldLoginInterceptor()).addPathPatterns("/admin/oldLogin");

        registry.addInterceptor(new AdminInterceptor()).addPathPatterns("/admin/*").excludePathPatterns("/admin/oldLogin");
    }
}
```

> ```
>  registry.addInterceptor(myInterceptor())
>                 .addPathPatterns("/**") //拦截所有路径
>                 .excludePathPatterns("/login", "/", "/exit", "/get_cpacha")//排除路径
>                 .excludePathPatterns("/xadmin/**");//排除静态资源拦截
> ```