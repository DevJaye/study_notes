# Springboot整合UEditor图片上传功能笔记
## 新建springboot项目
- 添加web和thymeleaf包
 - SpringBoot框架，配备了Thymeleaf模板引擎
### 引入jar包
```xml
<properties>
        <java.version>1.8</java.version>
        <!--修改thymeleaf版本-->
        <thymeleaf.version>3.0.3.RELEASE</thymeleaf.version>
        <thymeleaf-layout-dialect.version>2.1.0</thymeleaf-layout-dialect.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>
```

### 将ueditor拷贝纸resources/static下(ueditor下载官方最新的jsp的utf8版)
- 将config.json拷到resources根路径下
- 将ueditor中index中的内容拷贝得到入口的函数的html中
- 入口函数如下:
```java
@Controller
public class UeditorController {

    @RequestMapping("/")
    public String index() {
        return "index";
    }
}
```

### 启动成功后前台控制器报(后台配置项返回格式出错，上传功能将不能正常使用！)
- 这是没有引用官方包内的jsp/lib包内的五个jar包
- 其中maven中央仓库内有三个
```xml
<!--        ueditor必备jar包-->
<!--        1.commons-codec-->
        <dependency>
            <groupId>commons-codec</groupId>
            <artifactId>commons-codec</artifactId>
            <version>1.9</version>
        </dependency>
<!--        2.commons-fileupload-->
        <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.3.1</version>
        </dependency>
<!--        3.commons-io-->
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.4</version>
        </dependency>
```
- 需要通过maven指令安装2个
- maven安装指令如下:
```cmd
# 安装json.jar包指定阻止为com.json id为json ,版本为1.1
mvn install:install-file -Dfile=C:\Users\D_Jaye\utf8-jsp\jsp\lib\json.jar -DgroupId=com.json -DartifactId=json -Dversion=1.1 -Dpackaging=jar
# 安装json.jar包指定阻止为com.百度 id为ueditor ,版本为1.1.2
mvn install:install-file -Dfile=C:\Users\D_Jaye\utf8-jsp\jsp\lib\ueditor-1.1.2.jar -DgroupId=com.baidu -DartifactId=ueditor -Dversion=1.1.2 -Dpackaging=jar
```

```xml
<!--        4.json手动安装-->
        <dependency>
            <groupId>com.json</groupId>
            <artifactId>json</artifactId>
            <version>1.1</version>
        </dependency>

<!--        5.ueditor-1.1.2手动安装-->
        <dependency>
            <groupId>com.baidu</groupId>
            <artifactId>ueditor</artifactId>
            <version>1.1.2</version>
        </dependency>
<++>
