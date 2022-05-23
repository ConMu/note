参考链接：https://zhuanlan.zhihu.com/p/136469945

##### Springboot配置注解

```bash
$ @Value # 相当于传统 xml 配置文件中的 value 字段
$ @ConfigurationProperties # 标有 @ConfigurationProperties 的类的所有属性和配置文件中相关的配置项进行绑定
$ @Import # 注解支持导入普通 java 类，并将其声明成一个bean。主要用于将多个分散的 java config 配置类融合成一个更大的 config 类
$ @Conditional # 实现只有在特定条件满足时才启用一些配置 @Conditional(MyCondition.class) 其中MyCondition.class需要实现Condition接口并重写matches方法
$ @SpringbootApplication # 组合注解  1.这个类是 SpringBoot 的主配置类。  2.SpringBoot 就应该运行这个类的 main 方法来启动 SpringBoot 应用
	$ @SpringbootConfiguration # == @Configuration
	$ @ComponentScan # 开启组件扫描，配置用于 Configuration 类的组件扫描指令，可以 basePackageClasses 或basePackages 来定义要扫描的特定包
	$ @EnableAutoConfiguration # 开启自动配置
		$ @AutoConfigurationPackage # 将主配置类（@SpringBootConfiguration标注的类）的所在包及下面所有子包里面的所有组件扫描到Spring容器中
```

