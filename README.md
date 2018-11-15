# springboot-
记录学习springboot中的问题



从公司（易商数码科技有限公司）项目【半亩园商城】接触到springcloud，渐渐开始了解分布式系统。到今天开始倒学springboot。公司项目使用的是springboot1.5.4的版本，jdk还支持1.7。学习时使用2.x版本

# 一 约定大于配置
springboot可以做到0xml配置，唯一的配置文件application.properties也可以什么内容都不行，全部使用springboot的默认配置
如果要使用像swagger，mybatis等需要配置文件工具时，可以建一个config包，使用@configration注解类，避免使用xml配置，项目会更简洁，清晰

# 二 自定义properties属性
对application.properties中定义的自定义属性，在2.0中可以使用注解@ConfigurationProperties(prefix = "spring.my-example")

