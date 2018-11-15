# springboot-
记录学习springboot中的问题



从公司（易商数码科技有限公司）项目【半亩园商城】接触到springcloud，渐渐开始了解分布式系统。到今天开始倒学springboot。公司项目使用的是springboot1.5.4的版本，jdk还支持1.7。学习时使用2.x版本

# 一 约定大于配置
springboot可以做到0xml配置，唯一的配置文件application.properties也可以什么内容都不行，全部使用springboot的默认配置
如果要使用像swagger，mybatis等需要配置文件工具时，可以建一个config包，使用@configration注解类，避免使用xml配置，项目会更简洁，清晰

# 二 自定义properties属性
对application.properties中定义的自定义属性，在2.0中可以使用注解@ConfigurationProperties(prefix = "spring.my-example")
将前缀为spring.my-example的属性映射到类MyExample上，类MyExample中的属性需要和spring.my-example后面的属性保持一致，不然映射不到类中
```java
@ConfigurationProperties(prefix = "spring.my-example")
public class MyExample {
	private String fooo;
	
	private String hello;

	private List url;
	
	public String getFooo() {
		return fooo;
	}

	public void setFooo(String fooo) {
		this.fooo = fooo;
	}

	public String getHello() {
		return hello;
	}

	public void setHello(String hello) {
		this.hello = hello;
	}

	public List getUrl() {
		return url;
	}

	public void setUrl(List url) {
		this.url = url;
	}
	
}
```
在启动类上还需要使用注解@EnableConfigurationProperties({MyExample.class})将MyExample注入到spring容器中，不然无法使用@Autowired注入，启动时会报错
```java
@SpringBootApplication
@EnableConfigurationProperties({MyExample.class})
public class AliBootStarterTestApplication {

	public static void main(String[] args) {
		ConfigurableApplicationContext context = SpringApplication.run(AliBootStarterTestApplication.class, args);
		
		for(String name : context.getBeanDefinitionNames()){
			System.out.println(name);
		}
                /*
                 * 利用Binder对象获取，在controller，service中可以使用@Autowired注入
                 */
		Binder binder = Binder.get(context.getEnvironment());
                // 绑定简单配置
		MyExample myExample = binder.bind("spring.my-example", Bindable.of(MyExample.class)).get();
		System.out.println(myExample.getUrl());
	}
}
```
# 使用swagger
使用注解@Configurationt添加配置，使用注解@EnableSwagger2开启spring对swagger的支持
```java
@Configuration
@EnableSwagger2
public class Swagger2 implements EnvironmentAware{
	private String basePackage;
	private String serviceName;
	private String description;
	private String developer;
	private String version;
	@Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage(basePackage))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title(serviceName)
                .description(description)
                .termsOfServiceUrl("http://blog.didispace.com/")
                .contact(developer)
                .version(version)
                .build();
    }

	@Override
	public void setEnvironment(Environment environment) {
		Swagger2Properties target = Binder.get(environment)
		.bind("swagger", Swagger2Properties.class)
		.orElse(null);
		basePackage = target.getBasePackage();
		serviceName = target.getServiceName();
		description = target.getServiceDescription();
		developer = target.getServiceDeveloper();
		version = target.getVersion();
	}
}
```
