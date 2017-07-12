---
layout:     post
title:      springmvc集成swagger2
subtitle:   springmvc+swagger2
date:       2017-07-12
author:     licc
header-img: img/post_bg_debug.png
catalog: true
tags:
    - spring
    - swagger
---

## maven依赖
```
<dependency>
        <groupId>io.springfox</groupId>
        <artifactId>springfox-swagger2</artifactId>
        <version>2.4.0</version>
      </dependency>
      <dependency>
        <groupId>io.springfox</groupId>
        <artifactId>springfox-swagger-ui</artifactId>
        <version>2.4.0</version>
      </dependency>
		<dependency>
			<groupId>io.swagger</groupId>
			<artifactId>swagger-annotations</artifactId>
			<version>1.5.3</version>
		</dependency>
      <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-annotations</artifactId>
        <version>2.4.4</version>
      </dependency>
      <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.4.4</version>
      </dependency>
      <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-core</artifactId>
        <version>2.4.4</version>
      </dependency>
```
## Swagger配置文件

```java
import io.swagger.models.Contact;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

@Configuration
@EnableSwagger2
public class SwaggerConfig {

    @Bean
    public Docket buildDocket(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(buildApiInf())
                .select()
                 .apis(RequestHandlerSelectors.basePackage("com.**.controller"))//controller路径
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo buildApiInf(){
        return new ApiInfoBuilder()
                .title("****")
                .termsOfServiceUrl("****")
                .description("****")
                .build();

    }
}
```
---
## 权限过滤
``` 
项目中访问swagger 需要过滤掉swagger相关路径:
/v2/api-docs,/configuration/ui,/swagger-resources,/configuration/security,/swagger-ui.html,/webjars/**
```
---
## 访问路径
   ```
   http://工程路径/swagger-ui.html
   ```
---


## swagger相关资料   
> * **[swagger注解](https://github.com/licc168/coding-java/blob/master/src/main/java/com/licc/web/_swagger/swagger.md)**
> * **[swagger从入门到精通](https://www.gitbook.com/book/huangwenchao/swagger/details)**
