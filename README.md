# SwaggerOfflineDoc
基于SpringBoot和Swagger2生成离线文档：PDF和Html5格式 <br/>
**具体参考:** https://blog.csdn.net/fly910905/article/details/79131755
Swagger2离线文档：PDF和Html5格式
2018年01月22日 17:52:10 琦彦 阅读数：19790
 版权声明：本文为博主原创文章，未经博主允许不得转载。	https://blog.csdn.net/fly910905/article/details/79131755
Swagger2在线文档

http://blog.csdn.net/fly910905/article/details/79006747

Swagger2离线文档

0.程序结构



 

项目源码-Swagger2离线文档：PDF和Html5格式
 

1.Maven配置

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
 
    <groupId>com.swagger.offline</groupId>
    <artifactId>SwaggerOfflineDoc</artifactId>
    <version>1.0-SNAPSHOT</version>
 
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.2.RELEASE</version>
    </parent>
 
    <properties>
        <snippetsDirectory>${project.build.directory}/generated-snippets</snippetsDirectory>
 
        <asciidoctor.input.directory>${project.basedir}/docs/asciidoc</asciidoctor.input.directory>
        <generated.asciidoc.directory>${project.build.directory}/asciidoc</generated.asciidoc.directory>
        <asciidoctor.html.output.directory>${project.build.directory}/asciidoc/html</asciidoctor.html.output.directory>
        <asciidoctor.pdf.output.directory>${project.build.directory}/asciidoc/pdf</asciidoctor.pdf.output.directory>
 
    </properties>
 
    <dependencies>
 
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.8</version>
        </dependency>
 
        <!--WEB层-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--测试-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>
        <!--Swagger2-->
        <!--在线文档-->
        <!--swagger本身不支持spring mvc的，springfox把swagger包装了一下，让他可以支持springmvc-->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.6.1</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.6.1</version>
        </dependency>
        <!--离线文档-->
        <dependency>
            <groupId>org.springframework.restdocs</groupId>
            <artifactId>spring-restdocs-mockmvc</artifactId>
            <version>1.1.2.RELEASE</version>
            <scope>test</scope>
        </dependency>
        <!--springfox-staticdocs 生成静态文档-->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-staticdocs</artifactId>
            <version>2.6.1</version>
        </dependency>
    </dependencies>
 
    <build>
        <finalName>SwaggerOfflineDoc</finalName>
        <plugins>
            <!--<plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>-->
            <!--Maven通过Maven Surefire Plugin插件执行单元测试-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
            </plugin>
            <!-- Run the generated asciidoc through Asciidoctor to generate
                 other documentation types, such as PDFs or HTML5 -->
            <!--通过Asciidoctor使得asciidoc生成其他的文档格式，例如：PDF 或者HTML5-->
            <plugin>
                <groupId>org.asciidoctor</groupId>
                <artifactId>asciidoctor-maven-plugin</artifactId>
                <version>1.5.3</version>
                <!-- Include Asciidoctor PDF for pdf generation -->
                <!--生成PDF-->
                <dependencies>
                    <dependency>
                        <groupId>org.asciidoctor</groupId>
                        <artifactId>asciidoctorj-pdf</artifactId>
                        <version>1.5.0-alpha.14</version>
                    </dependency>
                    <!-- Comment this section to use the default jruby artifact provided by the plugin -->
                    <dependency>
                        <groupId>org.jruby</groupId>
                        <artifactId>jruby-complete</artifactId>
                        <version>1.7.21</version>
                    </dependency>
                </dependencies>
 
                <!-- Configure generic document generation settings -->
                <!--文档生成配置-->
                <configuration>
                    <sourceDirectory>${asciidoctor.input.directory}</sourceDirectory>
                    <sourceDocumentName>index.adoc</sourceDocumentName>
                    <attributes>
                        <doctype>book</doctype>
                        <toc>left</toc>
                        <toclevels>3</toclevels>
                        <numbered></numbered>
                        <hardbreaks></hardbreaks>
                        <sectlinks></sectlinks>
                        <sectanchors></sectanchors>
                        <generated>${generated.asciidoc.directory}</generated>
                    </attributes>
                </configuration>
                <!-- Since each execution can only handle one backend, run
                   separate executions for each desired output type -->
                <!--因为每次执行只能处理一个后端，所以对于每个想要的输出类型，都是独立分开执行-->
                <executions>
                    <!--html5-->
                    <execution>
                        <id>output-html</id>
                        <phase>test</phase>
                        <goals>
                            <goal>process-asciidoc</goal>
                        </goals>
                        <configuration>
                            <backend>html5</backend>
                            <outputDirectory>${asciidoctor.html.output.directory}</outputDirectory>
                        </configuration>
                    </execution>
                    <!--pdf-->
                    <execution>
                        <id>output-pdf</id>
                        <phase>test</phase>
                        <goals>
                            <goal>process-asciidoc</goal>
                        </goals>
                        <configuration>
                            <backend>pdf</backend>
                            <outputDirectory>${asciidoctor.pdf.output.directory}</outputDirectory>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
 
 
</project>
 

 

 

2.index.adoc

路径：项目名/docs/asciidoc/index.adoc
include::{generated}/overview.adoc[]
include::{generated}/definitions.adoc[]
include::{generated}/paths.adoc[]
3.Swagger2配置

package com.swagger.offline.config;
 
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;
 
 
/**
 * @version V1.0
 * @Title: Swagger配置类
 * @ClassName: com.newcapec.config.swagger.Swagger2Configuration.java
 * @Description:
 * @Copyright 2016-2017  - Powered By 研发中心
 * @author: 王延飞
 * @date:2017-12-11 8:20
 */
 
@Configuration
@EnableSwagger2
public class SwaggerConfiguration {
    @Bean
    public Docket buildDocket() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(buildApiInfo())
                .select()
                //要扫描的API(Controller)基础包
                .apis(RequestHandlerSelectors.basePackage("com.swagger.offline.controller"))
                .paths(PathSelectors.any())
                .build();
    }
 
    /**
     * @param
     * @return springfox.documentation.service.ApiInfo
     * @Title: 构建API基本信息
     * @methodName: buildApiInfo
     * @Description:
     * @author: 王延飞
     * @date: 2017-12-11  8:44
     */
    private ApiInfo buildApiInfo() {
 
        return new ApiInfoBuilder()
                .title("用户信息API文档")
                .description("这里除了查看接口功能外，还提供了调试测试功能")
                .contact("王延飞")
                .version("1.0")
                .build();
 
    }
}
 

 

 

4.实体类

import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
 
@ApiModel(value = "User", description = "用户信息描述")
public class User {
    /**
     * 学号
     */
    @ApiModelProperty("证件号")
    private int id;
    /**
     * 姓名
     */
    @ApiModelProperty("姓名")
    private String name;
    /**
     * 年龄
     */
    @ApiModelProperty("年龄")
    private int age;
    /**
     * 性别
     */
    @ApiModelProperty("性别")
    private String sex;
    /**
     * 住址
     */
    @ApiModelProperty("家庭住址")
    private String address;
    public int getId() {
        return id;
    }
    public void setId(int id) {
        this.id = id;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
    public String getSex() {
        return sex;
    }
    public void setSex(String sex) {
        this.sex = sex;
    }
    public String getAddress() {
        return address;
    }
    public void setAddress(String address) {
        this.address = address;
    }
    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", age=" + age +
                ", sex='" + sex + '\'' +
                ", address='" + address + '\'' +
                '}';
    }
}
 

5.Controller

 

import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import org.springframework.http.MediaType;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;
/**
 * @Title:
 * @ClassName: UserController.java
 * @Description:
 *
 * @Copyright 2016-2018 - Powered By 研发中心
 * @author: 王延飞
 * @date:  2018-01-22 16:08
 * @version V1.0
 */
@Controller
@RequestMapping(value = "/user", produces =  MediaType.APPLICATION_JSON_VALUE)
@Api(value = "用户信息查询", description = "用户基本信息操作API", tags = "UserApi", consumes = MediaType.APPLICATION_JSON_VALUE, produces = MediaType.APPLICATION_JSON_VALUE)
public class UserController {
    @ApiOperation(value = "/getUser", notes = "根据姓名查询用户信息")
    @RequestMapping(value = "getUser", method = RequestMethod.GET, produces = MediaType.APPLICATION_JSON_VALUE)
    @ResponseBody
    public User getUser(@RequestParam("name") String name){
        User user = new User();
        user.setId(123456);
        user.setName(name);
        user.setAge(25);
        user.setAddress("河南郑州");
        user.setSex("男");
        return user;
    }
    @ApiOperation(value = "/addUser", notes = "添加一个用户")
    @RequestMapping(value = "addUser", method = RequestMethod.POST, consumes = MediaType.APPLICATION_JSON_VALUE)
    @ResponseBody
    public User addUser(@RequestBody User user){
        return user;
    }
}
6.测试类

 

import com.alibaba.fastjson.JSON;
import io.github.robwin.markup.builder.MarkupLanguage;
import io.github.robwin.swagger2markup.GroupBy;
import io.github.robwin.swagger2markup.Swagger2MarkupConverter;
import org.junit.After;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.restdocs.AutoConfigureRestDocs;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.MediaType;
import org.springframework.restdocs.mockmvc.MockMvcRestDocumentation;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;
import springfox.documentation.staticdocs.SwaggerResultHandler;
import static org.springframework.restdocs.mockmvc.RestDocumentationRequestBuilders.get;
import static org.springframework.restdocs.mockmvc.RestDocumentationRequestBuilders.post;
import static org.springframework.restdocs.operation.preprocess.Preprocessors.preprocessResponse;
import static org.springframework.restdocs.operation.preprocess.Preprocessors.prettyPrint;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
/**
 * @Title:
 * @ClassName: SwaggerStaticDocTest.java
 * @Description:
 *
 * @Copyright 2016-2018  - Powered By 研发中心
 * @author: 王延飞
 * @date:  2018-01-22 16:06
 * @version V1.0
 */
@AutoConfigureMockMvc
@AutoConfigureRestDocs(outputDir = "target/generated-snippets")
@RunWith(SpringRunner.class)
@SpringBootTest
public class SwaggerStaticDocTest {
    private String snippetDir = "target/generated-snippets";
    private String outputDir  = "target/asciidoc";
    @Autowired
    private MockMvc mockMvc;
    @After
    public void Test() throws Exception {
        // 得到swagger.json,写入outputDir目录中
        mockMvc.perform(get("/v2/api-docs").accept(MediaType.APPLICATION_JSON))
                .andDo(SwaggerResultHandler.outputDirectory(outputDir).build())
                .andExpect(status().isOk())
                .andReturn();
        // 读取上一步生成的swagger.json转成asciiDoc,写入到outputDir
        // 这个outputDir必须和插件里面<generated></generated>标签配置一致
        Swagger2MarkupConverter.from(outputDir + "/swagger.json")
                .withPathsGroupedBy(GroupBy.TAGS)// 按tag排序
                .withMarkupLanguage(MarkupLanguage.ASCIIDOC)// 格式
                .withExamples(snippetDir)
                .build()
                .intoFolder(outputDir);// 输出
    }
    @Test
    public void TestApi() throws Exception {
        mockMvc.perform(get("/user/getUser").param("name", "FLY")
                .accept(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andDo(MockMvcRestDocumentation.document("getUser", preprocessResponse(prettyPrint())));
        User user = new User();
        user.setId(123456);
        user.setName("FLY");
        user.setAge(25);
        user.setAddress("河南郑州");
        user.setSex("男");
        mockMvc.perform(post("/user/addUser").contentType(MediaType.APPLICATION_JSON)
                .content(JSON.toJSONString(user))
                .accept(MediaType.APPLICATION_JSON))
                .andExpect(status().is2xxSuccessful())
                .andDo(MockMvcRestDocumentation.document("addUser", preprocessResponse(prettyPrint())));
    }
}
 

7.测试

 

mvn clean test
生成的PDF和HTML文件：target/asciidoc/html and target/asciidoc/pdf


 

html格式

 



 
