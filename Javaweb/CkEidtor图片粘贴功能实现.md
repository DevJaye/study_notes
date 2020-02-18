# CKeditor实现图片上传和图片粘贴功能
## ckeditor前期准备
- 实现CKeditor的版本为为CKeditor4.13,可以本地引用或者云端引用
- `<script type="text/javascript " src="${pageContext.request.contextPath}/resources/ckeditor/ckeditor.js"></script>`
- `<script src="//cdn.ckeditor.com/4.13.1/standard/ckeditor.js"></script>` 
-`<script src="//cdn.ckeditor.com/4.13.1/full/ckeditor.js"></script>`   
## springboot 配置文件

```properties

spring.mvc.view.prefix=/WEB-INF/views/
spring.mvc.view.suffix=.jsp
spring.mvc.static-path-pattern=/resources/**
server.port=8080

# 设置图片传输大小
spring.servlet.multipart.max-file-size = 10MB
spring.servlet.multipart.max-request-size = 100MB
```

## jar包前期准备
### 关键jar包
```xml
        <!--JSONObject -->
        <dependency>
            <groupId>net.sf.json-lib</groupId>
            <artifactId>json-lib</artifactId>
            <version>2.4</version>
            <classifier>jdk15</classifier>
        </dependency>
        <!--JSONObject -->
```
### 环境全部jar包
```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
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
        <dependency>
            <groupId>taglibs</groupId>
            <artifactId>standard</artifactId>
            <version>1.1.2</version>
        </dependency>
        <dependency>
            <groupId>com.ckeditor</groupId>
            <artifactId>ckeditor-java-core</artifactId>
            <version>3.5.3</version>
        </dependency>

        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-jasper</artifactId>
            <version>9.0.30</version>
        </dependency>
        <dependency>
            <groupId>commons-lang</groupId>
            <artifactId>commons-lang</artifactId>
            <version>2.6</version>
        </dependency>
        <!-- 	JSONObject -->
        <dependency>
            <groupId>net.sf.json-lib</groupId>
            <artifactId>json-lib</artifactId>
            <version>2.4</version>
            <classifier>jdk15</classifier>
        </dependency>
        <!-- 	JSONObject -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
    </dependencies>
```

## 后端存储图片逻辑

### 常量部分

```java
package kg.imge_ck.entity;

public class WebsiteConstant {
    public static String[] IMAGE_EXTENSION_NAME_ARRAY = {".jpg", ".jpeg", ".png", ".gif", ".bmp"};
    public static String PIC_APP_SERVER_URL = "http://localhost:8080/upload/";
//    public static String PIC_APP_FILE_SYSTEM_CKEDITOR_LOCATION = "";

}
```

### 存储模块`SpringMVCConfiguration` 
```java
package kg.imge_ck;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class SpringMVCConfiguration implements WebMvcConfigurer {
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/upload/**")
                .addResourceLocations("/WEB-INF/upload/");
    }
}
```
### controller层,存储逻辑和命名规则
- 此处有2个方法对应前台的上传图片接口和粘贴图片接口 
- 官方文档解释`https://ckeditor.com/docs/ckeditor4/latest/guide/dev_file_upload.html` 
  - `uploadImage`方法是上传图片的方法,此方法官方文档说返回值为空(void),但是必须传入`CKEditorFuncNum` 
    - `CKEditorFuncNum` 前台带过来的随机数
    - 此方法还需注意的需要通过`HttpServletResponse` 回写一段前端代码
    ```js
                out.println("<script type=\"text/javascript\">");
                out.println("window.parent.CKEDITOR.tools.callFunction(" + callback
                        + ",''," + "'文件格式不正确（必须为" + allImageExtensionName + "文件）');");
                out.println("</script>");
    ```
  - `uploadPastesImage`官方文档说必须要有返回值,返回为一个json对象
    - json对象的格式为
      - 无错误回写
    ```json
    {
    "uploaded": 1,
    "fileName": "foo.jpg",
    "url": "/files/foo.jpg"
    }
    ```
      - 有错误回写
    ```json
    {
    "uploaded": 1,
    "fileName": "foo(2).jpg",
    "url": "/files/foo(2).jpg",
    "error": {
        "message": "A file with the same name already exists. The uploaded file was renamed to \"foo(2).jpg\"."
    }
    }
    ```
- controller层完整代码
```java
package kg.imge_ck.controllers;
import kg.imge_ck.entity.WebsiteConstant;
import net.sf.json.JSONObject;
import org.apache.tomcat.util.http.fileupload.servlet.ServletFileUpload;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.context.ServletContextAware;
import org.springframework.web.multipart.MultipartFile;

import javax.servlet.ServletContext;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.*;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.util.HashMap;
import java.util.Map;
import java.util.UUID;

@Controller
@RequestMapping("/product")
public class PublicUtilController implements ServletContextAware {
    private ServletContext servletContext;
 @RequestMapping(method = RequestMethod.GET)
    public String index() {
     return "product/index";
 }

    @RequestMapping(value = "uploadImage", method = RequestMethod.POST)

    private void uploadImage(HttpServletRequest request, HttpServletResponse response, @RequestParam MultipartFile[] upload) {
        response.setCharacterEncoding("UTF-8");

        PrintWriter out = null;
        try {
            out = response.getWriter();
        } catch (IOException e1) {
            //logger.error("response.getWriter()异常=" + e1);
            e1.printStackTrace();
        }
        String callback = request.getParameter("CKEditorFuncNum");

        // 获得response,request
        Map<String, Object> m = new HashMap<String, Object>();

        // 判断客户端<form>标记的enctype属性是否是“multipart/form-data"。
        if (!ServletFileUpload.isMultipartContent(request)) {
            m.put("error", 1);
            m.put("message", "请选择文件!");
            //return m;
            //logger.info("请选择文件!");
        }

        //上传的图片文件名
        String originalFileName = null;
        //上传图片的文件扩展名
        String fileExtensionName = null;
        for (MultipartFile file : upload) {
            // 文件大于10M
            if (file.getSize() > 10 * 1024 * 1024) {
                out.println("<script type=\"text/javascript\">");
                out.println("window.parent.CKEDITOR.tools.callFunction(" + callback
                        + ",''," + "'文件大小不得大于10M');");
                out.println("</script>");
            }
            // 文件小于10M
            originalFileName = file.getOriginalFilename();
            //logger.info("上传的图片文件名=" + originalFileName);
            fileExtensionName = originalFileName.substring(
                    originalFileName.lastIndexOf("."), originalFileName.length()).toLowerCase();
            //logger.info("图片文件扩展名=" + fileExtensionName);

            // 文件支持的扩展名
            String[] imageExtensionNameArray = WebsiteConstant.IMAGE_EXTENSION_NAME_ARRAY;
            String allImageExtensionName = "";
            //默认不包含上传图片文件扩展名
            boolean isContain = false;
            for (int i = 0; i < imageExtensionNameArray.length; i++) {
                if (fileExtensionName.equals(imageExtensionNameArray[i])) {
                    isContain = true;
                }
                if (i == 0) {
                    allImageExtensionName += imageExtensionNameArray[i];
                } else {
                    allImageExtensionName += " , " + imageExtensionNameArray[i];
                }
            }

            String newFileName = UUID.randomUUID().toString() + fileExtensionName;

            Path paths = Paths.get(servletContext.getRealPath("/WEB-INF/upload/" ));
            System.out.println(paths);
//            String uploadPath = WebsiteConstant.PIC_APP_FILE_SYSTEM_CKEDITOR_LOCATION;
            String uploadPath =paths.toString();

            System.out.println(uploadPath);
            if (isContain) {
                // 包含
                File pathFile = new File(uploadPath);
                if (!pathFile.exists()) {
                    // 如果路径不存在，创建
                    pathFile.mkdirs();
                    System.out.println("创建文件夹");
                }
                try {
                    //FileUtils.copyInputStreamToFile(file.getInputStream(), new File(uploadPath, newFileName));
                    System.out.println("开始写入>>>>");
                    InputStream is = file.getInputStream();
                    File toFile = new File(uploadPath, newFileName);
                    OutputStream os = new FileOutputStream(toFile);
                    byte[] buffer = new byte[1024];
                    int length = 0;
                    while ((length = is.read(buffer)) > 0) {
                        os.write(buffer, 0, length);
                    }
                    is.close();
                    os.close();
                    System.out.println("写入结束++++");
                } catch (IOException e) {
                    //logger.error("FileUtils.copyInputStreamToFile uploadPath=" + uploadPath + " newFileName =" + newFileName + " exception=" + e);
                }
                String imageUrl = WebsiteConstant.PIC_APP_SERVER_URL + newFileName;
                System.out.println(imageUrl);
                // 返回"图像信息"选项卡并显示图片 ,在对应的文本框中显示图片资源url
               out.println("<script type=\"text/javascript\">");
                out.println("window.parent.CKEDITOR.tools.callFunction(" + callback + ",'" + imageUrl + "',''" + ")");
                out.println("</script>");

            } else {
                out.println("<script type=\"text/javascript\">");
                out.println("window.parent.CKEDITOR.tools.callFunction(" + callback
                        + ",''," + "'文件格式不正确（必须为" + allImageExtensionName + "文件）');");
                out.println("</script>");
            }
        }
    }

    @RequestMapping(value = "uploadPastesImage", method = RequestMethod.POST)
    @ResponseBody
    private JSONObject uploadPatesImage(HttpServletRequest request, HttpServletResponse response, @RequestParam MultipartFile[] upload) {
        response.setCharacterEncoding("UTF-8");
        System.out.println("进来粘贴模块>>>>>>>>>>");

        JSONObject jsonObject = new JSONObject();
        PrintWriter out = null;
        String callback = request.getParameter("CKEditorFuncNum");

        // 获得response,request
        Map<String, Object> m = new HashMap<String, Object>();

        jsonObject.put("uploaded",1);
        // 判断客户端<form>标记的enctype属性是否是“multipart/form-data"。
        if (!ServletFileUpload.isMultipartContent(request)) {
            m.put("error", 1);
            m.put("message", "请选择文件!");
            //return m;
            //logger.info("请选择文件!");
        }

        //上传的图片文件名
        String originalFileName = null;
        //上传图片的文件扩展名
        String fileExtensionName = null;
        for (MultipartFile file : upload) {
            // 文件大于10M
            if (file.getSize() > 10 * 1024 * 1024) {
                out.println("<script type=\"text/javascript\">");
                out.println("window.parent.CKEDITOR.tools.callFunction(" + callback
                        + ",''," + "'文件大小不得大于10M');");
                out.println("</script>");
            }
            // 文件小于10M
            originalFileName = file.getOriginalFilename();
            //logger.info("上传的图片文件名=" + originalFileName);
            fileExtensionName = originalFileName.substring(
                    originalFileName.lastIndexOf("."), originalFileName.length()).toLowerCase();
            //logger.info("图片文件扩展名=" + fileExtensionName);

            // 文件支持的扩展名
            String[] imageExtensionNameArray = WebsiteConstant.IMAGE_EXTENSION_NAME_ARRAY;
            String allImageExtensionName = "";
            //默认不包含上传图片文件扩展名
            boolean isContain = false;
            for (int i = 0; i < imageExtensionNameArray.length; i++) {
                if (fileExtensionName.equals(imageExtensionNameArray[i])) {
                    isContain = true;
                }
                if (i == 0) {
                    allImageExtensionName += imageExtensionNameArray[i];
                } else {
                    allImageExtensionName += " , " + imageExtensionNameArray[i];
                }
            }

            String newFileName = UUID.randomUUID().toString() + fileExtensionName;

            jsonObject.put("fileName",newFileName);
            System.out.println("json_name设置成功"+newFileName);
            Path paths = Paths.get(servletContext.getRealPath("/WEB-INF/upload/" ));
            System.out.println("json_url设置成功");
//            System.out.println(paths);
//            String uploadPath = WebsiteConstant.PIC_APP_FILE_SYSTEM_CKEDITOR_LOCATION;
            String uploadPath =paths.toString();

//            System.out.println(uploadPath);
            if (isContain) {
                // 包含
                File pathFile = new File(uploadPath);
                if (!pathFile.exists()) {
                    // 如果路径不存在，创建
                    pathFile.mkdirs();
                    System.out.println("创建文件夹");
                }
                try {
                    //FileUtils.copyInputStreamToFile(file.getInputStream(), new File(uploadPath, newFileName));
                    System.out.println("开始写入>>>>");
                    InputStream is = file.getInputStream();
                    File toFile = new File(uploadPath, newFileName);
                    OutputStream os = new FileOutputStream(toFile);
                    byte[] buffer = new byte[1024];
                    int length = 0;
                    while ((length = is.read(buffer)) > 0) {
                        os.write(buffer, 0, length);
                    }
                    is.close();
                    os.close();
                    System.out.println("写入结束++++");
                } catch (IOException e) {
                    //logger.error("FileUtils.copyInputStreamToFile uploadPath=" + uploadPath + " newFileName =" + newFileName + " exception=" + e);
                }
                String imageUrl = WebsiteConstant.PIC_APP_SERVER_URL + newFileName;
                System.out.println(imageUrl);
                jsonObject.put("url",imageUrl);

            }
        }
        System.out.println(jsonObject);
        return jsonObject;

    }
    @Override
    public void setServletContext(ServletContext servletContext) {
        this.servletContext =servletContext;
    }
}
```


## 前端调用
### 使用html基础方式引用

```js

<body>
<textarea name="editor1" id="editor1" rows="10" cols="80"></textarea>
<button onclick="myFunction()">点我</button>
<br>
<form action="/base/save" method="post">
    <input type="text" id="code1" name="code1">
    <input type="submit" value="提交">
</form>
</body>

<script>
    CKEDITOR.replace( 'editor1' );
    function myFunction(){
        var code1 = CKEDITOR.instances.editor1.getData();
        var ucode=encode(code1);
        document.getElementById("code1").value=code1;
    }
</script>

```
### 使用jQuery的方式
- 引入`ckeditor.js` 和`jquery..js` 
- body内引入CKeditor
```js
<body>
<textarea id="editor"></textarea>
```
- 配置CKeditor的属性
```js
<script>
    $(document).ready(function () {
        CKEDITOR.replace('editor', {
            //设置文本框高度
            height: 400,
            //上传图片
            filebrowserImageUploadUrl : "/product/uploadImage?",
            //粘贴图片
            uploadUrl : "/product/uploadPastesImage?"
        });
</script>
```
- 关闭CKeditor内图片插入框多余不需要的功能
```js
        CKEDITOR.on('dialogDefinition', function (e) {
            var dialogName = e.data.name;
            var dialogDefinition = e.data.definition;
            switch (dialogName) {
                case 'image' :
                    // dialogDefinition.removeContents('info');
                    dialogDefinition.removeContents('Link');
                    dialogDefinition.removeContents('advanced');
                    break;
            }
        });
```
- 拿取文本框内数据和写入文本框内数据
- script部分
```js
//拿取数据
        $('#butId').click(function () {
            alert(CKEDITOR.instances.editor.getData());
        });
//写入数据
        $('#butIds').click(function () {
            CKEDITOR.instances.editor.setData('');
        });
```
- body部分

```js
<button type="button" class="but" id="butId">提交</button>
<button type="button" class="buts" id="butIds">清空</button>
```



