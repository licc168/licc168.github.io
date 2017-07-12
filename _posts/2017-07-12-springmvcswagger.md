---
layout:     post
title:      springmvc集成swagger2
subtitle:   springmvc+swagger2
date:       2017-07-12
author:     BY
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

## 注解

> * @Api：用在类上，说明该类的作用
> * @ApiOperation：用在方法上，说明方法的作用
> * @ApiImplicitParams：用在方法上包含一组参数说明
> * @ApiImplicitParam：用在@ApiImplicitParams注解中，指定一个请求参数的各个方面
> * paramType：参数放在哪个地方
> * header--&gt;请求参数的获取：@RequestHeader
> * query--&gt;请求参数的获取：@RequestParam
> * path（用于restful接口）--&gt;请求参数的获取：@PathVariable
> * body（不常用）
> * form（不常用）

> * name：参数名
> * dataType：参数类型
> * required：参数是否必须传
> * value：参数的意思
> * defaultValue：参数的默认值

> * @ApiResponses：用于表示一组响应
> * @ApiResponse：用在@ApiResponses中，一般用于表达一个错误的响应信息
> * code：数字，例如400
> * message：信息，例如"请求参数没填好"
> * response：抛出异常的类
> * @ApiModel：描述一个Model的信息（这种一般用在post创建的时候，使用@RequestBody这样的场景，请求参数无法使用@ApiImplicitParam注解进行描述的时候）
> * @ApiModelProperty：描述一个model的属性

## 访问路径
   ```工程路径/swagger-ui.html```
