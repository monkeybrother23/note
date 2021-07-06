# pom

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.0.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <!--druid-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.20</version>
        </dependency>
        <!--mysql数据库-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <!--jpa-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
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
            <!-- this is needed or IntelliJ gives junit.jar or junit-platform-launcher:1.3.2 not found errors -->
            <groupId>org.junit.platform</groupId>
            <artifactId>junit-platform-launcher</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

# 基础配置

## banner

### 定制banner

resources 目录下创建一个 banner.txt 

### 关闭banner

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplicationBuilder builder = new SpringApplicationBuilder(Application.class);
        builder.bannerMode(Banner.Mode.OFF).run(args);
    }

}
```

## tomcat配置

### application

```yaml
server:
  port: 9090 # 端口号
  servlet:
    context-path: /demo # 项目名称不配置时，默认为/。如果配置了，就要在访问路径中加上配置的路径
  tomcat:
    basedir: /home/temp
```

# Web开发

## pom

```xml
<dependency> 
    <groupid>org.springframework.boot</groupid>     
    <artifactid>spring-boot-starter-web</artifactid> 
</dependency> 
```

## @Controller

表示这个类是SpringMVC  里的Controller，DispatchServlet会自动扫面这个类。效果：用这个注解，通过return的String类型的数据，视图解析器可以解析jsp,html页面，并且跳转到相应页面。目的就是为了能访问，所以是必须的 

## @ResponseBody

可作用于类或者方法，表示该方法的返回结果直接写入 HTTP response body  中，适用于返回JSON，XML。无法返回jsp页面，或者html。一般通过Ajax程序来获取数据。一般只写在方法上面，因为这个注解是为了，区别同一个类，不同的方法到底是返回页面还是返回数据  

## @ResponseEntity

```java
@GetMapping("/hello")
ResponseEntity<String> hello() {
    return ResponseEntity.ok("Hello World!");
}
// 不同响应状态
@GetMapping("/age")
ResponseEntity<String> age(
  @RequestParam("yearOfBirth") int yearOfBirth) {

    if (isInFuture(yearOfBirth)) {
        return new ResponseEntity<>(
          "Year of birth cannot be in the future", 
          HttpStatus.BAD_REQUEST);
    }

    return new ResponseEntity<>(
      "Your age is " + calculateAge(yearOfBirth), 
      HttpStatus.OK);
}
// 设置http响应头
@GetMapping("/customHeader")
ResponseEntity<String> customHeader() {
    HttpHeaders headers = new HttpHeaders();
    headers.add("Custom-Header", "foo");

    return new ResponseEntity<>(
      "Custom header set", headers, HttpStatus.OK);
}
```

## @RestController

```java
//表示 Controller+ ResponseBody
@RestController
public class TestController {
    @GetMapping(value = {"/test"})
    public HashMap test() {
        HashMap book = new HashMap();
        book.put("name", "罗贯中");
        book.put("price", "100");
        return book;
    }
```

## @PathVariable

```java
@GetMapping(value = "/brand/{id}")
public String toEditPage(@PathVariable("id") String id, Model model) {
    BrandEntity entity = null;
    try {
        entity = brandService.findOne(id);
        model.addAttribute("brand", entity);
    } catch (SQLException e) {
        e.printStackTrace();
    }
    return "brand/add";
}
```

## @RequestParam

用来处理Content-Type: `application/x-www-form-urlencoded`编码的内容。（Http协议中，如果不指定Content-Type，则默认传递的参数就是application/x-www-form-urlencoded类型）

```java
@RequestMapping(method = RequestMethod.GET)  
  public String setupForm(@RequestParam("petId") int petId, ModelMap model) {  
      Pet pet = this.clinic.loadPet(petId);  
      model.addAttribute("pet", pet);  
      return "petForm";  
} 
```

## @RequestBody

@RequestBody接收的参数是来自requestBody中，即请求体。一般用于处理非 Content-Type: `application/x-www-form-urlencoded`编码格式的数据，比如：**application/json**、**application/xml**等类型的数据。
就**application/json**类型的数据而言，使用注解@RequestBody可以将body里面所有的json数据传到后端，后端再进行解析

# 持久层

## druid

### pom

```xml
<!--mysql数据库-->
 <dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency> 
<!--druid-->
<dependency>
   <groupId>com.alibaba</groupId>
   <artifactId>druid-spring-boot-starter</artifactId>
   <version>1.1.20</version>
</dependency>
```

### application

```yaml
spring:
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.jdbc.Driver
    username: root
    password: 123456
    url: jdbc:mysql://127.0.0.1:3306/test?serverTimezone=Asia/Shanghai
    druid:
      initial-size: 5
      max-active: 20
      min-idle: 5
      #获取连接等待超时时间
      max-wait: 60000
      #间隔多久进行一次检测，检测需要关闭的空闲连接
      time-between-eviction-runs-millis: 60000
      #一个连接在池中最小生存的时间
      min-evictable-idle-time-millis: 30000
      validation-query: SELECT 'x'
      test-while-idle: true
      test-on-borrow: false
      test-on-return: false
      #打开PSCache，并指定每个连接上PSCache的大小。oracle设为true，mysql设为false。分库分表较多推荐设置为false
      pool-prepared-statements: false
      max-pool-prepared-statement-per-connection-size: 20
      #配置监控统计拦截的filters,去掉后监控界面sql将无法统计
      filters: stat,wall,slf4j
      web-stat-filter:
        enabled: true
        url-pattern: "/*"
        exclusions: "*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*"
        session-stat-max-count: 1000
        session-stat-enable: true
        profile-enable: true
      stat-view-servlet:
        enabled: true
        url-pattern: "/druid/*"
        login-username: root
        login-password: 123456
        allow: 127.0.0.1
        reset-enable: true
```

## mybatis

### pom

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.1</version>
</dependency>
```

### application

```yaml
mybatis:
  mapper-locations: classpath:mapping/*Mapper.xml
```

### mapper

```java
package com.example.index.project.mapper;

import com.example.index.project.entity.DemoEntity;
import org.apache.ibatis.annotations.Mapper;
import org.springframework.stereotype.Repository;

@Mapper
public interface DemoMapper {
    DemoEntity selectById(String id);
}

```

### xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.index.project.mapper.DemoMapper">
    <resultMap type="com.example.index.project.entity.DemoEntity" id="DemoMap">
        <id column="id" property="id" jdbcType="VARCHAR"/>
        <result column="name" property="name" jdbcType="VARCHAR"/>
        <result column="age" property="age" jdbcType="VARCHAR"/>
    </resultMap>
</mapper>

```

#### select

```xml
 <!--查询单个-->
    <select id="queryById" resultMap="DemoMap">
        select pk_id,
               name,
               age,
               sex,
               hobby
        from demo.demo
        where pk_id = #{pkId,jdbcType=INTEGER}
    </select>
<!--通过实体作为筛选条件查询-->
    <select id="queryAll" resultMap="DemoMap">
        select pk_id, 
               name, 
               age, 
               sex,
               hobby
        from demo.demo
        <where>
            <if test="pkId != null">
                and pk_id = #{pkId}
            </if>
            <if test="name != null and name != ''"> <!--不能为空串-->
                and name LIKE #{name}'%'<!--模糊查询-->
            </if>
            <if test="age != null">
                and age = #{age}
            </if>
        </where>
    </select>
```

#### insert

```xml
<!--新增所有列-->
    <insert id="insert">
        insert into demo.demo(name, age, sex, hobby)
        values (#{name,jdbcType=VARCHAR}, #{age,jdbcType=INTEGER}, 
                #{sex,jdbcType=INTEGER}, #{hobby,jdbcType=VARCHAR})
    </insert>
<!-- 批量新增-->
<insert id="batchSave" parameterType="java.util.List">
    INSERT INTO lp_user_test_batch
    (
    id,
    user_id,
    user_name,
    user_age,
    type,
    create_time,
    update_time
    )
    VALUES
    <foreach collection="list" item="item" index="index" separator=",">
        (
        #{item.id,jdbcType=BIGINT},
        #{item.userId,jdbcType=VARCHAR},
        #{item.userName,jdbcType=VARCHAR},
        #{item.userAge,jdbcType=INTEGER},
        #{item.type,jdbcType=INTEGER},
        #{item.createTime,jdbcType=TIMESTAMP},
        #{item.updateTime,jdbcType=TIMESTAMP}
        )
    </foreach>
</insert>
```

#### update

```xml
 <!--通过主键修改数据-->
    <update id="update">
        update demo.demo
        <set>
            <if test="name != null and name != ''"><!--不能为空串-->
                name = #{name},
            </if>
            <if test="age != null">
                age = #{age},
            </if>
        </set>
        where pk_id = #{pkId,jdbcType=INTEGER}
    </update>

<update id="updateBatch" parameterType="java.util.List">
    update table_name
    set  status=
    <foreach collection="list" item="item" index="index" 
        separator=" " open="case ID" close="end">
        when #{item.id} then #{item.status}
    </foreach>
    where id in
    <foreach collection="list" index="index" item="item" 
        separator="," open="(" close=")">
        #{item.id,jdbcType=BIGINT}
    </foreach>
</update>
```

#### delete

```xml
<!--通过主键删除-->
    <delete id="deleteById">
        delete
        from demo.demo
        where pk_id = #{pkId,jdbcType=INTEGER}
    </delete>
<!--参数为List-->
<delete id="deleteByLogic"  parameterType = "java.util.List">
     delete from user where 1>2
         or id in
      <foreach collection="list"  item="item" open="(" separator="," close=")"  >
           #{item}
      </foreach>
</delete>
<!--参数为数组-->
<delete id="deleteByLogic"  parameterType = "java.util.List">
     delete from user where 1>2
         or id in
     <foreach item="item" collection="array" open="(" separator="," close=")">
            #{item}
    </foreach
</delete>
```



### 分页

##### pom

```xml
<dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper-spring-boot-starter</artifactId>
            <version>1.2.5</version>
</dependency>
```

##### application

```properties
# 指定数据库，不指定的话会默认自动检测数据库类型
pagehelper.helperDialect=mysql
# 是否启用分页合理化。
# 如果启用，当pagenum<1时，会自动查询第一页的数据，当pagenum>pages时，自动查询最后一页数据；
# 不启用的，以上两种情况都会返回空数据
pagehelper.reasonable=true
# 默认值false，分页插件会从查询方法的参数值中，自动根据上面 params 配置的字段中取值，查找到合适的值时就会自动分页。
pagehelper.supportMethodsArguments=true
# 用于从对象中根据属性名取值，
# 可以配置 pageNum,pageSize,count,pageSizeZero,reasonable，不配置映射的用默认值，
# 默认值为pageNum=pageNum;pageSize=pageSize;count=countSql;reasonable=reasonable;pageSizeZero=pageSizeZero
pagehelper.params=count=countSql
```



# 全局异常

## Result

```java
@NoArgsConstructor
@Setter
@Getter
public class Result {
    private Integer code;
    private String msg;
    private String url;
}
```

## ResultCode

```java
@AllArgsConstructor
@Getter
public enum ResultCode {
    SUCCESS(200, "请求成功"),
    Unknown_Exception(100, "未知异常"),
    private int code;
    private String msg;
}
```

## ResultSuccess

```java
@NoArgsConstructor
@Getter
@Setter
public class ResultSuccess<T> extends Result {
    private T data;

    public ResultSuccess(T data) {
        this.data = data;
        this.setCode(200);
        this.setMsg("请求成功");
    }
}
```



## AppException

````java
@Setter
@Getter
public class AppException extends RuntimeException {
    private int errCode = ResultCode.Unknown_Exception.getCode();

    public AppException() {
        super(ResultCode.Unknown_Exception.getMsg());
    }

    public AppException(ResultCode resultCode) {
        super(resultCode.getMsg());
        this.errCode = resultCode.getCode();
    }
}
````

## Handler

```java
@RestControllerAdvice
public class GlobalDefaultExceptionHandler {

    @ExceptionHandler(value = AppException.class)
    public Result domainExceptionHandler(HttpServletRequest request, AppException e) throws Exception {
        Result result = new Result();
        result.setCode(e.getErrCode());
        result.setMsg(e.getMessage());
        result.setUrl(request.getRequestURI());
        return (result);
    }
}
```

## controller

```java
 @GetMapping("/test1")
    public ResultSuccess<User> eee() {
        User user = userService.findById("123");
        if (user != null) {
            return new ResultSuccess<>(user);
        } else {
            throw new AppException(ResultCode.Unknown_Exception);
        }
    }

@GetMapping("/test2")
    public ResultSuccess<User> rrr() {
        throw new AppException(ResultCode.Unknown_Exception);
    }
```

# 文件操作

```java
 /**
     * cuihao
     * @param contentType excel类型的application
     * @param byteArrayOutputStream 文件流
     * @param response HttpServletResponse
     * @param returnName 返回的文件名
     */
    public static void download(String contentType, String returnName, ByteArrayOutputStream byteArrayOutputStream, HttpServletResponse response) throws IOException {
        response.setContentType(contentType);
        response.setCharacterEncoding(StandardCharsets.UTF_8.name());
        //保存的文件名,必须和页面编码一致,否则乱码
        returnName = response.encodeURL(new String(returnName.getBytes(), StandardCharsets.ISO_8859_1));
        response.addHeader("Content-Disposition", "attachment;filename=" + returnName);
        response.setContentLength(byteArrayOutputStream.size());
        //取得输出流
        ServletOutputStream outputStream = response.getOutputStream();
        //写到输出流
        byteArrayOutputStream.writeTo(outputStream);
        //关闭
        byteArrayOutputStream.close();
        //刷数据
        outputStream.flush();
    }
```

## 文件下载

```java
@GetMapping("demoImport")
    public void demoImport(HttpServletResponse response) {
        ServletOutputStream outputStream = null;
        InputStream inputStream = null;
        try {
            response.setHeader("content-Type", "application/vnd.ms-excel");
            // 下载文件的默认名称
            response.setHeader("Content-Disposition", "attachment;filename=" + URLEncoder.encode("kqmb.xlsx", "utf-8"));
            inputStream = getClass().getResourceAsStream("/excel/kqmb.xlsx");
            outputStream = response.getOutputStream();
            byte[] b = new byte[1024];
            int ln = 0;
            while((ln = inputStream.read(b)) != -1){
                outputStream.write(b,0,ln);
            }
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            try {
                if (outputStream != null){
                    outputStream.close();
                }

                if (inputStream != null){
                    inputStream.close();
                }
            }catch (IOException e) {
                e.printStackTrace();
            }


        }
    }

```

## 文件上传

#### 前端

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<label for="file">文件上传</label>
<input id="file" name="file" type="file" onchange="upLoad(this)"/>
</body>
<script>
    function upLoad(file) {
        if ('' === document.getElementById("file").value) {
            console.log("未选择文件");
            return;
        }
        let fileName = file.files[0].name;
        let fileType = fileName.substring(fileName.lastIndexOf(".") + 1, fileName.length);
        //文件后缀格式校验
        //console.log(fileType);
        let fileSize = file.files[0].size;
        //文件大小校验
        //console.log(fileSize);
        let formData = new FormData();
        formData.append("file", file.files[0]);
        let xhr = new XMLHttpRequest();
        xhr.addEventListener("load", uploadComplete, false);
        xhr.open("POST", "http://localhost:9090/albert/upload");
        xhr.send(formData);
    }

    //上传完成
    function uploadComplete(evt) {
        let json = eval('(' + evt.target.responseText + ')');
        //上传完成服务器相应
        console.log(json);
        alert(json.data);
    }
</script>
</html>
```

#### 后端

```java
@PostMapping("importData")
    public void importData(@RequestParam("file") MultipartFile file) {
        if (!file.isEmpty()) {
            System.out.println(file.getOriginalFilename());
        }
    }
```

## excel

```js
//发送请求
function exportData() {
			var param = {}
			param.jsonStr = JSON.stringify(rowData)
			postDownLoadFile({
				url: "http://127.0.0.1:9090/dfmsys/yhmcManage/exportData",
				data: param,
				method: 'post'
			});
	}
//将ajax请求封装成表单请求
	var postDownLoadFile = function (options) {
		var config = $.extend(true, { method: 'post' }, options);
		var $iframe = $('<iframe id="down-file-iframe" />');
		var $form = $('<form target="down-file-iframe" method="' + config.method + '" />');
		$form.attr('action', config.url);
		for (var key in config.data) {
			$form.append('<input type="hidden" name="' + key + '" value="' + config.data[key] + '" />');
		}
		$iframe.append($form);
		$(document.body).append($iframe);
		$form[0].submit();
		$iframe.remove();
	}
```

```xml
<dependency>    
			<groupId>org.apache.poi</groupId>    
			<artifactId>poi</artifactId>    
			<version>4.0.0</version>
		</dependency>
		<dependency>    
			<groupId>org.apache.poi</groupId>    
			<artifactId>poi-ooxml</artifactId>    
			<version>4.0.0</version>
		</dependency
```

```java
@PostMapping("exportData")
    public void exportData(@RequestParam HashMap param, HttpServletResponse response) {
        try {
            String aac001s = param.get("aac001s").toString();
            List<HashMap> list = this.YhkqManageReadService.exportData(aac001s);
            //1.创建Excel对象
            XSSFWorkbook wb = new XSSFWorkbook();
            //2.创建Sheet对象
            Sheet sheet = wb.createSheet();
            //3.定义一些可复用的对象
            int rowIndex = 0;//行的索引
            int cellIndex = 1;//单元格的索引
            Row nRow;
            Cell nCell;
            //4.设置列的宽度（列索引，列宽*256  理解为固定写法）
            sheet.setColumnWidth(1, 15 * 256);
            sheet.setColumnWidth(2, 15 * 256);
            sheet.setColumnWidth(3, 30 * 256);
            sheet.setColumnWidth(4, 15 * 256);
            sheet.setColumnWidth(5, 30 * 256);
            sheet.setColumnWidth(6, 15 * 256);
            sheet.setColumnWidth(7, 60 * 256);
            //5.创建大标题行   大标题：2019年5月份新增用户表
            nRow = sheet.createRow(rowIndex++);//使用的是0,使用完了+1
            //设置大标题行的高度
            nRow.setHeightInPoints(36);
            //6.创建大标题的单元格
            nCell = nRow.createCell(cellIndex);
            //7.合并单元格
            sheet.addMergedRegion(new CellRangeAddress(0, 0, 1, 4));
            //8.设置大标题内容
            String bigTitle = "考勤表";
            nCell.setCellValue(bigTitle);
            //9.创建小标题内容
            String[] titles = new String[]{"姓名", "性别", "公民身份证号码", "岗位", "用工单位", "出勤天数", "考勤时间段"};
            //10.创建小标题行
            nRow = sheet.createRow(rowIndex++);//使用的是1，使用完了再加1
            //设置小标题行高
            nRow.setHeightInPoints(26.25f);
            //12.创建小标题的单元格
            for (String title : titles) {
                nCell = nRow.createCell(cellIndex++);
                //设置小标题内容
                nCell.setCellValue(title);
            }
            //13.获取要生成的数据（数据库内的用户数据）
            for (HashMap map : list) {
                nRow = sheet.createRow(rowIndex++);
                cellIndex=1;
                nCell = nRow.createCell(cellIndex++);
                nCell.setCellValue(map.getOrDefault("AAC003", "").toString());
                nCell = nRow.createCell(cellIndex++);
                nCell.setCellValue(map.getOrDefault("AAC004", "").toString());
                nCell = nRow.createCell(cellIndex++);
                nCell.setCellValue(map.getOrDefault("AAC147", "").toString());
                nCell = nRow.createCell(cellIndex++);
                nCell.setCellValue(map.getOrDefault("ABA461", "无").toString());
                nCell = nRow.createCell(cellIndex++);
                nCell.setCellValue(map.getOrDefault("AAB004", "").toString());
                nCell = nRow.createCell(cellIndex++);
                nCell.setCellValue(map.getOrDefault("KQTS", "").toString());
                nCell = nRow.createCell(cellIndex);
                nCell.setCellValue(map.getOrDefault("KQSJD", "").toString());
            }
            wb.setSheetName(0, "测试");
            //最后，下载新增用户表文件，字节数组的输出流，它可存可取，带缓冲区
            ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
            wb.write(byteArrayOutputStream);
            String contentType = "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet";
            FileUtil.download(contentType, bigTitle + ".xlsx", byteArrayOutputStream, response);
            wb.close();
        } catch (AppException | IOException e) {
            e.getMessage();
        }
    }
```

## pdf预览

```java
 public static void  showContract(HttpServletResponse response, String contractId){
        response.setContentType("application/pdf");
        try (ServletOutputStream outputStream = response.getOutputStream();) {
            FDDSDKClient server = new FDDSDKClient(new JyStorageServiceImpl());
            ContractDownloadRequest d = new ContractDownloadRequest();
            d.setContractId(contractId);
            ContractDownloadResponse res = server.contractDownload(d);
            byte[] bytes = res.getContent();
            outputStream.write(bytes);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```

```js
//预览
function showContract() {
		$("#iframe").attr("src", "");
		$("#iframe").off("load");
		var contractId = "nmgbzj233"
		var xhr = new XMLHttpRequest();
		xhr.open("get", "http://127.0.0.1:9090/dfmsys/employmentInformation/contractRegistration/showContract?contractId=" + contractId, true);
		//设置响应类型为blob类型
		xhr.responseType = "blob";
		xhr.onload = function () {
			if (this.status == "200") {
				var blob = this.response;
				$("#iframe").attr("src", URL.createObjectURL(blob));
			}
		}
		xhr.send();
	}

//直接打印
function doPrint() {
		var contractId = "nmgbzj233"
		var xhr = new XMLHttpRequest();
		xhr.open("get", "http://127.0.0.1:9090/dfmsys/employmentInformation/contractRegistration/showContract?contractId=" + contractId, true);
		//设置响应类型为blob类型
		xhr.responseType = "blob";
		xhr.onload = function () {
			if (this.status == "200") {
				var blob = this.response;
				$("#iframe").attr("src", URL.createObjectURL(blob));
				$("#iframe").load(function () {//等待iframe加载完成后再执行doPrint.每次iframe设置src之后都会重新执行这部分代码。
					document.getElementById("iframe").contentWindow.print();
				});
			}
		}
		xhr.send();
	}
```

# 消息中间件

## pom

```xml
<!--Rabbitmq-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-amqp</artifactId>
        </dependency>
```

## application

```yaml
http://120.79.15.29:15672/
# rabbitmq
spring.rabbitmq.host=120.79.15.29
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
  rabbitmq:
    host: 120.79.15.29
    username: admin
    password: admin
    port: 5672
    virtual-host: test
```

## config

```java
@Configuration
public class AMQPConfig {
    @Bean
    public MessageConverter messageConverter() {
        return new Jackson2JsonMessageConverter();
    }
}
```

## RabbitTemplate

```java
 @Autowired
 RabbitTemplate rabbitTemplate;

 /*点对点消息*/
 @Test
 public void testDirect() {
     Map<String, Object> map = new HashMap<>();
     map.put("msg", "这是第一条消息");
     map.put("data", Arrays.asList("hello", "world", 123, true));
//        对象被默认序列化以后发送出去
     rabbitTemplate.convertAndSend("exchange.direct", "albert", map);
 }

 /*接收数据*/
 @Test
 public void testReceive() {
     Object o = rabbitTemplate.receiveAndConvert("albert");
     System.out.println(o.getClass());
     System.out.println(o);
 }
```

## RabbitListener

```java
@EnableRabbit//开启注解消息
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}

   @RabbitListener(queues = "albert.news")
    public void receice01(Student student) {
        System.out.println("收到消息" + student);
    }

    @RabbitListener(queues = "albert.news")
    public void receice02(Message message) {
        System.out.println(message.getBody());
        System.out.println(message.getMessageProperties());
    }
```

