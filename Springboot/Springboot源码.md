参考链接：https://zhuanlan.zhihu.com/p/136469945

##### 启动

###### 启动常用类

```bash
$ ApplicationContextInitializer
$ ApplicationRunner
$ CommandLineRunner
$ SpringApplicationRunListener
```

###### 链路

```bash
SpringApplication.run() -> run(new Class[]{primarySource}, args) -> new SpringApplication(primarySources)).run(args)
```

```java
new SpringApplication(primarySources)).run(args) // 1. 创建SpringApplication对象  2.运行run()方法
```

- 创建SpringApplication对象

```java
public SpringApplication(ResourceLoader resourceLoader, Class... primarySources) { 

this.sources = new LinkedHashSet(); 
this.bannerMode = Mode.CONSOLE; 
this.logStartupInfo = true; 
this.addCommandLineProperties = true; 
this.addConversionService = true; 
this.headless = true; 
this.registerShutdownHook = true; 
this.additionalProfiles = new HashSet(); 
this.isCustomEnvironment = false; 
this.resourceLoader = resourceLoader; 
Assert.notNull(primarySources, "PrimarySources must not be null"); 
// 保存主配置类（这里是一个数组，说明可以有多个主配置类） 
this.primarySources = new LinkedHashSet(Arrays.asList(primarySources)); 
// 判断当前是否是一个 Web 应用 
this.webApplicationType = WebApplicationType.deduceFromClasspath(); 
// 从类路径下找到 META/INF/Spring.factories 配置的所有 ApplicationContextInitializer，然后保存起来 
this.setInitializers(this.getSpringFactoriesInstances(ApplicationContextInitializer.class)); 
// 从类路径下找到 META/INF/Spring.factories 配置的所有 ApplicationListener，然后保存起来 
this.setListeners(this.getSpringFactoriesInstances(ApplicationListener.class)); 
// 从多个配置类中找到有 main 方法的主配置类（只有一个） 
this.mainApplicationClass = this.deduceMainApplicationClass(); 
    
} 
```

- 运行run()方法

```java
public ConfigurableApplicationContext run(String... args) { 

// 创建计时器 
StopWatch stopWatch = new StopWatch(); 
stopWatch.start(); 
// 声明 IOC 容器 
ConfigurableApplicationContext context = null; 
Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList(); 
this.configureHeadlessProperty(); 
// 从类路径下找到 META/INF/Spring.factories 获取 SpringApplicationRunListeners 
SpringApplicationRunListeners listeners = this.getRunListeners(args); 
// 回调所有 SpringApplicationRunListeners 的 starting() 方法 
listeners.starting(); 
Collection exceptionReporters; 
try { 
// 封装命令行参数 
ApplicationArguments applicationArguments = new DefaultApplicationArguments(args); 
// 准备环境，包括创建环境，创建环境完成后回调 SpringApplicationRunListeners#environmentPrepared()方法，表示环境准备完成 
ConfigurableEnvironment environment = this.prepareEnvironment(listeners, applicationArguments); 
this.configureIgnoreBeanInfo(environment); 
// 打印 Banner 
Banner printedBanner = this.printBanner(environment); 
// 创建 IOC 容器（决定创建 web 的 IOC 容器还是普通的 IOC 容器） 
context = this.createApplicationContext(); 
exceptionReporters = this.getSpringFactoriesInstances(SpringBootExceptionReporter.class, new Class[]{ConfigurableApplicationContext.class}, context); 
/*
 * 准备上下文环境，将 environment 保存到 IOC 容器中，并且调用 applyInitializers() 方法
 * applyInitializers() 方法回调之前保存的所有的 ApplicationContextInitializer 的 initialize() 方法
 * 然后回调所有的 SpringApplicationRunListener#contextPrepared() 方法 
 * 最后回调所有的 SpringApplicationRunListener#contextLoaded() 方法 
 */
this.prepareContext(context, environment, listeners, applicationArguments, printedBanner); 
// 刷新容器，IOC 容器初始化（如果是 Web 应用还会创建嵌入式的 Tomcat），扫描、创建、加载所有组件的地方 
this.refreshContext(context); 
// 从 IOC 容器中获取所有的 ApplicationRunner 和 CommandLineRunner 进行回调 
this.afterRefresh(context, applicationArguments); 
stopWatch.stop(); 
if (this.logStartupInfo) { 
(new StartupInfoLogger(this.mainApplicationClass)).logStarted(this.getApplicationLog(), stopWatch); 
} 
// 调用 所有 SpringApplicationRunListeners#started()方法 
listeners.started(context); 
this.callRunners(context, applicationArguments); 
} catch (Throwable var10) { 
this.handleRunFailure(context, var10, exceptionReporters, listeners); 
throw new IllegalStateException(var10); 
} 
try { 
listeners.running(context); 
return context; 
} catch (Throwable var9) { 
this.handleRunFailure(context, var9, exceptionReporters, (SpringApplicationRunListeners)null); 
throw new IllegalStateException(var9); 
} 
} 

//run() 阶段主要就是回调本节开头提到过的4个监听器中的方法与加载项目中组件到 IOC 容器中，而所有需要回调的监听器都是从类路径下的 META/INF/Spring.factories 中获取，从而达到启动前后的各种定制操作。
```

