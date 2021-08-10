## 配置

### maven/conf/settings

#### localRepository

```xml
<localRepository>E:\repository\albert</localRepository>
```

#### mirror

```xml
<mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>https://maven.aliyun.com/repository/public/</url>
      <mirrorOf>public</mirrorOf>       
</mirror>
```
#### 本地jar
##### pom
```xml
        <dependency>
            <groupId>myjar</groupId>-----------------(参数二)
            <artifactId>OrgPublic</artifactId>-----------(参数三)
            <version>1.0</version>------------(参数四)
        </dependency>
```
##### mvn
```shell
mvn install:install-file -Dfile="C:\Users\Administrator\Desktop\OrgPublic.jar" -DgroupId=myjar -DartifactId=OrgPublic -Dversion=1.0 -Dpackaging=jar
```
