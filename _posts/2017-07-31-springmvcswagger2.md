---
layout:     post
title:      SpringMvc整合springfox-staticdocs生成RESTfull API的文档
subtitle:   SpringMvc整合springfox-staticdocs项目生成RESTfull API的文档
keyword:     SpringMvc,springfox-staticdocs,swagger
description : SpringMvc整合springfox-staticdocs项目生成RESTfull API的文档
date:       2017-07-31
author:     licc
header-img: img/post_bg_debug.png
catalog: true
tags:
    - spring
    - swagger
    - springfox 
---

## 前言  
  前面章节：[springmvc集成swagger2](http://lichangchao.top/2017/07/12/springmvcswagger/) 


## maven依赖
```
      <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-staticdocs</artifactId>
            <version>2.4.0</version>
        </dependency>
      <dependency>
              <groupId>javax.servlet</groupId>
              <artifactId>javax.servlet-api</artifactId>
              <version>3.1.0</version>
               <scope>provided</scope>
      </dependency>
```   
注意：javax.servlet-api 必须是3.0+版本     

## 生成adoc和markdown文件

***设置生成文件编码格式为UTF-8：**   
```java

import io.github.robwin.markup.builder.MarkupLanguage;
import io.github.robwin.swagger2markup.Swagger2MarkupConverter;
import org.apache.commons.lang3.Validate;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.mock.web.MockHttpServletResponse;
import org.springframework.test.web.servlet.MvcResult;
import org.springframework.test.web.servlet.ResultHandler;


public class Swagger2MarkupResultHandlerUtf8 implements ResultHandler {
   private static final Logger LOG = LoggerFactory.getLogger(Swagger2MarkupResultHandlerUtf8.class);

   private final String outputDir;
   private final MarkupLanguage markupLanguage;
   private final String examplesFolderPath;
   private final String encoding = "UTF-8";
   
   Swagger2MarkupResultHandlerUtf8(String outputDir, MarkupLanguage markupLanguage, String examplesFolderPath) {
       this.outputDir = outputDir;
       this.markupLanguage = markupLanguage;
       this.examplesFolderPath = examplesFolderPath;
   }

   /**
    * Creates a Swagger2MarkupResultHandler.Builder
    *
    * @param outputDir the target folder
    * @return a Swagger2MarkupResultHandler.Builder
    */
   public static Builder outputDirectory(String outputDir) {
       Validate.notEmpty(outputDir, "outputDir must not be empty!");
       return new Builder(outputDir);
   }

   /**
    * Apply the action on the given result.
    *
    * @param result the result of the executed request
    * @throws Exception if a failure occurs
    */
   @Override
   public void handle(MvcResult result) throws Exception {
       MockHttpServletResponse response = result.getResponse();
       response.setCharacterEncoding(encoding);
       String swaggerJson = response.getContentAsString();
       Swagger2MarkupConverter.fromString(swaggerJson).withMarkupLanguage(markupLanguage)
               .withExamples(examplesFolderPath).build().intoFolder(outputDir);
   }


   public static class Builder {
       private final String outputDir;
       private String examplesFolderPath;
       private MarkupLanguage markupLanguage = MarkupLanguage.ASCIIDOC;
       
       Builder(String outputDir) {
           this.outputDir = outputDir;
       }

       /**
        * Builds Swagger2MarkupResultHandler which converts the Swagger response into Markup and writes into the given {@code
        * outputDir}.
        *
        * @return a Mock MVC {@code ResultHandler} that will produce the documentation
        * @see org.springframework.test.web.servlet.MockMvc#perform(org.springframework.test.web.servlet.RequestBuilder)
        * @see org.springframework.test.web.servlet.ResultActions#andDo(org.springframework.test.web.servlet.ResultHandler)
        */
       public Swagger2MarkupResultHandlerUtf8 build() {

           return new Swagger2MarkupResultHandlerUtf8(outputDir, markupLanguage,
                   examplesFolderPath);
       }

       /**
        * Specifies the markup language which should be used to generate the files
        *
        * @param markupLanguage the markup language which is used to generate the files
        * @return the Swagger2MarkupConverter.Builder
        */
       public Builder withMarkupLanguage(MarkupLanguage markupLanguage) {
           this.markupLanguage = markupLanguage;
           return this;
       }

       /**
        * Include examples into the Paths document
        *
        * @param examplesFolderPath the path to the folder where the example documents reside
        * @return the Swagger2MarkupConverter.Builder
        */
       public Builder withExamples(String examplesFolderPath) {
           this.examplesFolderPath = examplesFolderPath;
           return this;
       }
   }
}




```





**测试类：**
```java
import io.github.robwin.markup.builder.MarkupLanguage;
import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.MediaType;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.test.context.web.WebAppConfiguration;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import org.springframework.test.web.servlet.result.MockMvcResultMatchers;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.web.context.WebApplicationContext;

@WebAppConfiguration
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"/spring/jmzx-portal-context.xml","/spring/jmzx-portal-shiro-cas.xml"})
public class Swagger2MarkupTest{

    @Autowired
    private WebApplicationContext context;


    private MockMvc mockMvc;

    @Before
    public void setUp() {
        this.mockMvc = MockMvcBuilders.webAppContextSetup(this.context).build();
    }

    @Test
    public void convertSwaggerToAsciiDoc() throws Exception {
        this.mockMvc.perform(MockMvcRequestBuilders.get("/v2/api-docs")
                .accept(MediaType.APPLICATION_JSON))

                .andDo(Swagger2MarkupResultHandlerUtf8.outputDirectory("src/docs/asciidoc/generated").build())
                .andExpect(MockMvcResultMatchers.status().isOk());
    }

  @Test
    public void convertSwaggerToMarkdown() throws Exception {
        this.mockMvc.perform(MockMvcRequestBuilders.get("/v2/api-docs")
                .accept(MediaType.APPLICATION_JSON))
                .andDo(Swagger2MarkupResultHandlerUtf8.outputDirectory("src/docs/markdown/generated")
                        .withMarkupLanguage(MarkupLanguage.MARKDOWN).build())
                .andExpect(MockMvcResultMatchers.status().isOk());
    }
}
```




## maven插件配置
``` xml
 <build>
     <plugins>

         <plugin>
             <groupId>org.asciidoctor</groupId>
             <artifactId>asciidoctor-maven-plugin</artifactId>
             <version>1.5.2</version>
             <dependencies>
                 <dependency>
                     <groupId>org.asciidoctor</groupId>
                     <artifactId>asciidoctorj-pdf</artifactId>
                     <version>1.5.0-alpha.10.1</version>
                 </dependency>
                 <dependency>
                     <groupId>org.jruby</groupId>
                     <artifactId>jruby-complete</artifactId>
                     <version>9.0.0.0.rc1</version>
                 </dependency>
                 <dependency>
                     <groupId>org.asciidoctor</groupId>
                     <artifactId>asciidoctorj</artifactId>
                     <version>1.5.3.2</version>
                 </dependency>
             </dependencies>

             <configuration>
                 <sourceDirectory>src/docs/asciidoc</sourceDirectory>
                 <outputDirectory>target/docs/asciidoc</outputDirectory>
             </configuration>

             <executions>
                 <execution>
                     <id>asciidoc-to-html</id>
                     <phase>generate-resources</phase>
                     <goals>
                         <goal>process-asciidoc</goal>
                     </goals>
                     <configuration>
                         <backend>html5</backend>
                         <doctype>book</doctype>
                         <sourceHighlighter>coderay</sourceHighlighter>
                         <attributes>
                             <imagesdir>./images</imagesdir>
                             <toc>left</toc>
                             <icons>font</icons>
                             <sectanchors>true</sectanchors>
                             <!-- set the idprefix to blank -->
                             <idprefix/>
                             <idseparator>-</idseparator>
                             <docinfo1>true</docinfo1>
                         </attributes>
                     </configuration>
                 </execution>
                 <!--asciidoc生成pdf对中文字符的支持不完善-->
                 <!--<execution>-->
                 <!--<id>generate-pdf-doc</id>-->
                 <!--<phase>generate-resources</phase>-->
                 <!--<goals>-->
                 <!--<goal>process-asciidoc</goal>-->
                 <!--</goals>-->
                 <!--<configuration>-->
                 <!--<backend>pdf</backend>-->
                 <!--&lt;!&ndash; Since 1.5.0-alpha.9 PDF back-end can use 'rouge' as well as 'coderay' source highlighting &ndash;&gt;-->
                 <!--<sourceHighlighter>rouge</sourceHighlighter>-->
                 <!--<attributes>-->
                 <!--<icons>font</icons>-->
                 <!--<pagenums/>-->
                 <!--<toc/>-->
                 <!--<idprefix/>-->
                 <!--<idseparator>-</idseparator>-->
                 <!--</attributes>-->
                 <!--</configuration>-->
                 <!--</execution>-->
             </executions>
         </plugin>
     </plugins>

 </build>
```
***

##  通过maven插件生成html文档   

``
mvn clean && mvn install
``
 




## 参考资料  
> * **[Springmvc4集成springfox, swagger UI, springfox-staticdocs](http://eugeneyang.com/2015/12/18/Springmvc4%E9%9B%86%E6%88%90springfox,%20Swagger%20UI,%20springfox-staticdocs/)**
> * **[SpringBoot项目生成RESTfull API的文档](http://www.jianshu.com/p/af7a6f29bf4f)**
