# 				搭建项目框架（SpringBoot 1.5.9 版本 ）



## 一.创建基础SpringBoot启动项目

1.通过Idea创建空的maven项目

![image-20210330094537607](%E6%90%AD%E5%BB%BA%E9%A1%B9%E7%9B%AE%E6%A1%86%E6%9E%B6%EF%BC%88SpringBoot%201.5.9%20%E7%89%88%E6%9C%AC%20%EF%BC%89.assets/image-20210330094537607.png)

![image-20210330094716423](%E6%90%AD%E5%BB%BA%E9%A1%B9%E7%9B%AE%E6%A1%86%E6%9E%B6%EF%BC%88SpringBoot%201.5.9%20%E7%89%88%E6%9C%AC%20%EF%BC%89.assets/image-20210330094716423.png)



2.已创建好空Maven项目初始结构

![image-20210330094800366](%E6%90%AD%E5%BB%BA%E9%A1%B9%E7%9B%AE%E6%A1%86%E6%9E%B6%EF%BC%88SpringBoot%201.5.9%20%E7%89%88%E6%9C%AC%20%EF%BC%89.assets/image-20210330094800366.png)



3.pom文件配置

```java
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.9.RELEASE</version>
        <relativePath/>
    </parent>

    <!--打包方式-->
    <packaging>jar</packaging>

    <!--项目名和描述-->
    <name>zsh_query</name>
    <description>Demo project for Spring Boot</description>
    <!--项目信息-->
    <groupId>org.jumutang.com</groupId>
    <artifactId>coupon_mng</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <!--java版本-->
        <java.version>1.8</java.version>
    </properties>

    <!--maven依赖-->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    <!--打包构建项目配置-->
    <build>
        <plugins>
            <!-- 跳过单元测试 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.20.1</version>
                <configuration>
                    <skipTests>true</skipTests>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```



4.Application启动类配置

```java
package org.jumutang.com;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.builder.SpringApplicationBuilder;
import org.springframework.boot.web.support.SpringBootServletInitializer;

@SpringBootApplication
public class Application extends SpringBootServletInitializer {

    /**
     * spring-boot启动
     * 内嵌tomcat容器中启动,直接main运行当前方法或者maven_springboot插件run方法
     * */
    public static void main(String[] args) {
        SpringApplication.run(Application.class,args);
    }

    /**
     * springboot项目部署外部tomcat容器,重写当前configure方法
     * */
    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
        return builder.sources(Application.class);
    }
}

```



5.项目配置Idea启动

![image-20210330100829805](%E6%90%AD%E5%BB%BA%E9%A1%B9%E7%9B%AE%E6%A1%86%E6%9E%B6%EF%BC%88SpringBoot%201.5.9%20%E7%89%88%E6%9C%AC%20%EF%BC%89.assets/image-20210330100829805.png)

![image-20210330100910528](%E6%90%AD%E5%BB%BA%E9%A1%B9%E7%9B%AE%E6%A1%86%E6%9E%B6%EF%BC%88SpringBoot%201.5.9%20%E7%89%88%E6%9C%AC%20%EF%BC%89.assets/image-20210330100910528.png)

![image-20210330101022714](%E6%90%AD%E5%BB%BA%E9%A1%B9%E7%9B%AE%E6%A1%86%E6%9E%B6%EF%BC%88SpringBoot%201.5.9%20%E7%89%88%E6%9C%AC%20%EF%BC%89.assets/image-20210330101022714.png)

## 二.注入数据源

### 一.SpringBoot原生数据源

pom文件增加如下依赖：

```java
		<!--Mysql数据库驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>

        <!--SpringBoot.jdbc数据源连接-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
```

创建applicatoin.yml配置文件

```java
spring:
  datasource:
    url: jdbc:mysql://127.0.0.1:3306/db
    username: username_your
    password: password_your
    driver-class-name: com.mysql.jdbc.Driver
```

pom文件增加单元测试依赖,用于测试默认数据源配置是否正常

```
		<!--springBoot单元测试-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
```

单元测试代码：

```java
import org.jumutang.com.Application;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.SQLException;

@RunWith(SpringRunner.class)
@SpringBootTest(classes = Application.class)
public class ApplicationTest {

    @Autowired
    private DataSource dataSource;

    /**
     * @desc 测试dataSrouce数据源是否配置成功
     * @author muyr
     * @date 2021/03/30
     * */
    @Test
    public void testDataSrouceConfigIsRight() throws SQLException {
        Connection connection = dataSource.getConnection();
        System.out.println(connection);
        connection.close();
    }

}

```

数据源连接正常：

![image-20210330103230369](%E6%90%AD%E5%BB%BA%E9%A1%B9%E7%9B%AE%E6%A1%86%E6%9E%B6%EF%BC%88SpringBoot%201.5.9%20%E7%89%88%E6%9C%AC%20%EF%BC%89.assets/image-20210330103230369.png)

### 二.切换Druid数据源

修改applicaton.yml

```java
spring:
  datasource:
    ##默认自动装配Jdbc数据源
    #url: jdbc:mysql://127.0.0.1:3306/db
    #username: username_your
    #password: password_your
    #driver-class-name: com.mysql.jdbc.Driver
    ##Druid数据源
    druid:
      driver-class-name: com.mysql.jdbc.Driver
      url: jdbc:mysql://127.0.0.1:3306/db
      username: username_your
      password: password_your
      #初始化大小，最小，最大
      initial-size: 5
      min-idle: 10
      max-active: 50
      #配置最大超时等待时间
      max-wait: 60000
      # 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
      time-between-eviction-runs-millis: 60000
      # 配置一个连接在池中最小生存的时间，单位是毫秒
      min-evictable-idle-time-millis: 30000
      validation-query: SELECT 1 FROM DUAL
      test-while-idle: true
      test-on-return: false
      test-on-borrow: false
      # 打开PSCache，并且指定每个连接上PSCache的大小
      pool-prepared-statements: false
      # 配置监控统计拦截的filters，去掉后监控界面sql无法统计，'wall'用于防火墙
      filters: stat,wall,slf4j
      # 通过connectProperties属性来打开mergeSql功能；慢SQL记录
      connection-properties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000
      #合并多个数据源监控数据
      use-global-data-source-stat: true
```

pom文件添加druid依赖

```java
		<!-- 添加druid依赖 -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>
```

启动单元测试，打印数据源连接信息

![image-20210330112019905](%E6%90%AD%E5%BB%BA%E9%A1%B9%E7%9B%AE%E6%A1%86%E6%9E%B6%EF%BC%88SpringBoot%201.5.9%20%E7%89%88%E6%9C%AC%20%EF%BC%89.assets/image-20210330112019905.png)

#### druid监控

完整application.yml文件的配置：

```
spring:
  datasource:
    ##默认自动装配Jdbc数据源
    #url: jdbc:mysql://127.0.0.1:3306/db
    #username: username_your
    #password: password_your
    #driver-class-name: com.mysql.jdbc.Driver
    platform: mysql                               # 数据库类型
    type: com.alibaba.druid.pool.DruidDataSource  # 指定数据源类型
    ##Druid数据源
    druid:
      driver-class-name: com.mysql.jdbc.Driver
      url: jdbc:mysql://127.0.0.1:3306/db
      username: username_your
      password: password_your
      # 初始化大小，最小，最大
      initial-size: 5
      min-idle: 10
      max-active: 50
      # 配置获取连接等待超时的时间(单位：毫秒)
      max-wait: 60000
      # 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
      time-between-eviction-runs-millis: 20000
      # 配置一个连接在池中最小生存的时间，单位是毫秒
      min-evictable-idle-time-millis: 30000
      max-evictable-idle-time-millis: 90000
      # 用来测试连接是否可用的SQL语句,默认值每种数据库都不相同,这是mysql
      validation-query: SELECT 1 FROM DUAL
      # 应用向连接池申请连接，并且testOnBorrow为false时，连接池将会判断连接是否处于空闲状态，如果是，则验证这条连接是否可用
      test-while-idle: true
      # 如果为true，默认是false，应用向连接池申请连接时，连接池会判断这条连接是否是可用的
      test-on-borrow: false
      # 如果为true（默认false），当应用使用完连接，连接池回收连接的时候会判断该连接是否还可用
      test-on-return: false
      # 是否缓存preparedStatement，也就是PSCache。PSCache对支持游标的数据库性能提升巨大，比如说oracle
      pool-prepared-statements: false
      # 要启用PSCache，必须配置大于0，当大于0时， poolPreparedStatements自动触发修改为true，
      # 在Druid中，不会存在Oracle下PSCache占用内存过多的问题，
      # 可以把这个数值配置大一些，比如说100
      #maxOpenPreparedStatements: 20
      # 通过connectProperties属性来打开mergeSql功能；慢SQL记录
      connection-properties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000
      # 合并多个数据源监控数据
      use-global-data-source-stat: true
      # 连接池中的minIdle数量以内的连接，空闲时间超过minEvictableIdleTimeMillis，则会执行keepAlive操作
      keepAlive: true
      # Spring 监控，利用aop 对指定接口的执行时间，jdbc数进行记录
      #aop-patterns: "com.springboot.template.dao.*"
      # 配置监控统计拦截的filters，去掉后监控界面sql无法统计，'wall'用于防火墙
      ########### 启用内置过滤器（第一个 stat必须，否则监控不到SQL）##########
      filters: stat,wall,slf4j
      filter:
        # 开启druiddatasource的状态监控
        stat:
          enabled: true
          db-type: mysql
          # 开启慢sql监控，超过2s 就认为是慢sql，记录到日志中
          log-slow-sql: true
          slow-sql-millis: 2000
        # 日志监控，使用slf4j 进行日志输出
        slf4j:
          enabled: true
          statement-log-error-enabled: true
          statement-create-after-log-enabled: false
          statement-close-after-log-enabled: false
          result-set-open-after-log-enabled: false
          result-set-close-after-log-enabled: false
      ########## 配置WebStatFilter，用于采集web关联监控的数据 ##########
      web-stat-filter:
        enabled: true                   # 启动 StatFilter
        url-pattern: /*                 # 过滤所有url
        exclusions: "*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*" # 排除一些不必要的url
        session-stat-enable: true       # 开启session统计功能
        session-stat-max-count: 1000    # session的最大个数,默认100
      ########## 配置StatViewServlet（监控页面），用于展示Druid的统计信息 ##########
      stat-view-servlet:
        enabled: true                   # 启用StatViewServlet
        url-pattern: /druid/*           # 访问内置监控页面的路径，内置监控页面的首页是/druid/index.html
        reset-enable: false              # 不允许清空统计数据,重新计算
        login-username: root            # 配置监控页面访问密码
        login-password: 123
        allow: 127.0.0.1           # 允许访问的地址，如果allow没有配置或者为空，则允许所有访问
        deny:                                        # 拒绝访问的地址，deny优先于allow，如果在deny列表中，就算在allow列表中，也会被拒绝
```

注意：druid的spring监控默认已注释！需要依赖springBoot-Aop模块

```java
<!--SpringBoot 的aop 模块-->
        <!--备注：Druid的spring监控需要依赖此模块-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>
```

#### druid监控打印执行SQL日志

application.yml文件新增配置，指定日志输出级别

```java
# 优先级 从高到低 ERROR、WARN、INFO、DEBUG
logging:
  level:
    #指定了路径:driud.sql.Statement下日志打印级别为debug
    druid.sql.Statement: debug
```

修改application.yml已配置参数：

```java
      filter:
        # 开启druiddatasource的状态监控
        stat:
		  ...
          #slow-sql-millis: 2000 #正常配置
          slow-sql-millis: 1 #单元测试.druid是否正常打印输出可执行sql日志 
```

单元测试：

```java
 /**
     * @desc 执行sql,打印慢sql日志
     * @author muyr
     * @date 2021/03/30
     * */
    @Test
    public void testExcuteSql(){

        Integer count = jdbcTemplate.queryForObject("select count(*) from t_orderinfo where c_status = 1 ",Integer.class);
        System.out.println(count);
    }
```

druid控制台打印sql日志：

![image-20210330141047332](%E6%90%AD%E5%BB%BA%E9%A1%B9%E7%9B%AE%E6%A1%86%E6%9E%B6%EF%BC%88SpringBoot%201.5.9%20%E7%89%88%E6%9C%AC%20%EF%BC%89.assets/image-20210330141047332.png)

## 三.引入日志配置

### logback-sprng.xml文件配置

```
<?xml version="1.0" encoding="UTF-8"?>
<!--
scan:自动加载判断，当配置文件发生改变时，将会被重新加载。默认为true
scanPeriod:检测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认为毫秒，当scan=true时这个值生效，默认时间间隔为1分钟
debug:当被设置为true时，将打印出logback内部日志信息，实时查看logback运行信息，默认为false
-->
<configuration scan="false" scanPeriod="30 seconds" debug="false">

    <!--logback.xml的加载顺序早于springboot的application.yml,所以应使用logback-spring.xml文件-->

    <!--从配置文件中获取变量参数-->
    <springProperty scope="context" name="applicationName" source="spring.application.name"/>

    <!--上下文-->
    <contextName> ${applicationName} </contextName>

    <!--配置规则类的位置(日志输出语句中格式化输出自定义内容)-->
    <conversionRule conversionWord="ip" converterClass="org.jumutang.com.common.logback.IPLogConfig" />

    <!--获取tomcat的端口号信息-->
    <conversionRule conversionWord="tomcat_port" converterClass="org.jumutang.com.common.logback.GetTomcatPort" />

    <!--根据系统环境动态日志文件输出位置-->
    <define name="path" class="org.jumutang.com.common.logback.OSNameLogConfig" />

    <!--日志文件输出目录-->
    <property name="logFilePath" value="${path}/${applicationName}/" />

    <!--日志输出格式-->
    <!-- %X{变量名} 通过slf4j的MDC，当前线程中放入变量，使用前put，使用后remove-->
    <property name="logPrintFormat" value="%d %contextName %ip:%tomcat_port [%thread] %X{logUUID} %level %logger{36}.%M[%line] - %msg %n" />

    <!--控制台 -->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
                <pattern>${logPrintFormat}</pattern>
        </encoder>
    </appender>
    <!--输出日志-->
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!--新输出日志以追加方式在日志文件末尾-->
        <append>true</append>
        <encoder>
            <pattern>${logPrintFormat}</pattern>
            <charset>UTF-8</charset>
        </encoder>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--日志文件保存格式-->
            <fileNamePattern>${logFilePath}/info/%d{yyyyMMdd}_%i.log</fileNamePattern>
            <!--历史日志保留天数-->
            <maxHistory>60</maxHistory>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <!--单个日志大小-->
                <maxFileSize>20MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
    </appender>
    <!--输出错误日志-->
    <appender name="ERROR" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <append>true</append>
        <encoder>
            <pattern>${logPrintFormat}</pattern>
            <charset>UTF-8</charset>
        </encoder>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${logFilePath}/error/%d{yyyyMMdd}_%i.log</fileNamePattern>
            <!-- 按时间回滚的同时，按文件大小来回滚 -->
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>20MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
        <!--过滤指定级别的日志-->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <!--过滤 Error-->
            <level>ERROR</level>
            <!--匹配到就允许-->
            <onMatch>ACCEPT</onMatch>
            <!--没有匹配到就禁止-->
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>
    <!--根据taskId生成对应的文件单独保存-->
    <appender name="SIFT" class="ch.qos.logback.classic.sift.SiftingAppender">
        <!--discriminator鉴别器，根据taskId这个key对应的value鉴别日志事件，然后委托给具体appender写日志-->
        <discriminator>
            <key>taskId</key>
            <defaultValue>default</defaultValue>
        </discriminator>
        <sift>
            <!--根据taskId生成文件-->
            <appender name="File-${taskId}" class="ch.qos.logback.core.rolling.RollingFileAppender">
                <append>true</append>
                <encoder>
                    <pattern>${logPrintFormat}</pattern>
                    <charset>UTF-8</charset>
                </encoder>
                <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
                    <fileNamePattern>${logFilePath}/taskId/${taskId}/%d{yyyyMMdd}_%i.log</fileNamePattern>
                    <maxHistory>60</maxHistory>
                    <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                        <maxFileSize>20MB</maxFileSize>
                    </timeBasedFileNamingAndTriggeringPolicy>
                </rollingPolicy>
            </appender>
        </sift>
    </appender>
    <!-- 异步输出日志 -->
    <appender name ="ASYNC" class= "ch.qos.logback.classic.AsyncAppender">
        <!-- 不丢失日志.默认的,如果队列的80%已满,则会丢弃TRACT、DEBUG、INFO级别的日志 -->
        <discardingThreshold>0</discardingThreshold>
        <!-- 更改默认的队列的深度,该值会影响性能.默认值为256 -->
        <queueSize>512</queueSize>
        <!-- 添加附加的appender,最多只能添加一个 -->
<!--        <appender-ref ref ="SIFT"/>-->
        <appender-ref ref ="FILE"/>
        <!-- 新增这行为了打印栈堆信息(否则日志文件中可能会输出'?'字符，因为未携带堵栈堆信息) -->
        <includeCallerData>true</includeCallerData>
    </appender>

    <!--五个常用打印级别从低至高依次为TRACE、DEBUG、INFO、WARN、ERROR-->

    <!--指定包/类的级别，控制台只打印debug级别日志-->
    <logger name="java.sql.Connection" level="DEBUG" additivity="true" />
    <logger name="java.sql.Statement" level="DEBUG"  additivity="true" />
    <logger name="java.sql.PreparedStatement" level="DEBUG"  additivity="true" />

    <logger name="org.jumutang.com" level="INFO" additivity="true">
        <!--错误日志输出（勿动）-->
        <appender-ref ref="ERROR"/>
        <!--以下2中输出日志方式,任选一个(两者可同时使用,但ASYNC的SIFT中会输出部分重复的FILE中info的日志)-->
        <!--正常日志文件输出-->
<!--        <appender-ref ref="FILE"/>-->
        <!--异步(防止线程的堵塞,在持续高IO下写入操作)且带taskId(可动态根据不同taskId写入不同的日志文件)的日志文件写入(未携带taskId的日志输出默认'default')-->
        <appender-ref ref="ASYNC"/>
    </logger>

    <logger name="druid.sql.Statement" level="debug"></logger>
    <logger name="druid.sql.DataSource" level="debug"></logger>
    <logger name="druid.sql.Connection" level="debug"></logger>
    <logger name="druid.sql.ResultSet" level="debug"></logger>

    <!--<root>也是logger元素，但它是根logger，所有logger的最上级，只有一个level属性-->
    <!--root和logger的name表示范围，level表示在当前name范围下输出日志的最低level级别-->
    <!--
        个人理解总结：root为父级，logger子级，所有logger都继承root（包含root下的appender）
            若logger中的additivity=false，即logger自行通过appender输出日志，若无不输出；
            若additivity=true，logger中的日志会向上传递到root，且不受root的level级别限制，通过root的appender进行输出，
                logger的appender非空，那么logger的appender会叠加root的appender(这里的appender抽象的看作一个输出源，就是其他logger的输出向上传递到root的，与root下是否存在appender无关)进行输出
                [假如FILE的logger中不输出mybatis的logger的sql语句，那么就要在appender中配置filter过滤掉debug的日志输出]
            例子：mybatis的logger中如果设置additivity=false，那么File的logger中就不会打印sql执行语句，因为root中没有接收到mybatis的logger上传； 反之，mybatis的logger的additivity=true
    -->
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
    </root>

    <!--[勿删此注释]springProfile 注意log4j没有当前属性 (可根据prifile的active环境动态配置)-->
    <!--<springProfile name="dev"></springProfile>-->

</configuration>



```



### 日志格式化输出依赖类

#### GetTomcatPort.java 

 获取运行容器tomcat的端口号

```
package org.jumutang.com.common.logback;

import ch.qos.logback.classic.pattern.ClassicConverter;
import ch.qos.logback.classic.spi.ILoggingEvent;

import javax.management.MBeanServer;
import javax.management.MalformedObjectNameException;
import javax.management.ObjectName;
import javax.management.Query;
import java.lang.management.ManagementFactory;
import java.util.Set;

public class GetTomcatPort extends ClassicConverter {

    @Override
    public String convert(ILoggingEvent iLoggingEvent) {
        return getTomcatPort();
    }


    /**
     * @desc Mbean获取Tomcat的端口号
     * @author muyr
     * @date 2021/03/16
     * */
    private static String getTomcatPort()  {
        try {
            MBeanServer beanServer = ManagementFactory.getPlatformMBeanServer();
            Set<ObjectName> objectNames = beanServer.queryNames(new ObjectName("*:type=Connector,*"),
            Query.match(Query.attr("protocol"), Query.value("HTTP/1.1")));
            String port = objectNames.iterator().next().getKeyProperty("port");
            //log.info("获取容器tomcat当前端口号port:{}",port);
            return port;
        } catch (MalformedObjectNameException e) {
            e.printStackTrace();
            //log.error("获取容器tomcat的端口号port发生异常e:{}",e);
            return "0000";
        }
    }

}

```

#### IPLogConfig.java 

获取程序获取的ip地址信息

```
package org.jumutang.com.common.logback;

import ch.qos.logback.classic.pattern.ClassicConverter;
import ch.qos.logback.classic.spi.ILoggingEvent;
import org.springframework.web.context.request.RequestContextHolder;
import org.springframework.web.context.request.ServletRequestAttributes;

import javax.servlet.http.HttpServletRequest;
import java.net.InetAddress;
import java.net.UnknownHostException;


/**
 * @desc 自定义日志输出配置参数
 * @url https://blog.csdn.net/KingBoyWorld/article/details/78010444 (参考博客)
 * */
public class IPLogConfig extends ClassicConverter {

    public static String getIpAddr(HttpServletRequest request) {
        String ip = request.getHeader("x-forwarded-for");
        if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
            ip = request.getHeader("Proxy-Client-IP");
        }
        if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
            ip = request.getHeader("WL-Proxy-Client-IP");
        }
        if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
            ip = request.getHeader("HTTP_CLIENT_IP");
        }
        if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
            ip = request.getHeader("HTTP_X_FORWARDED_FOR");
        }
        if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
            ip = request.getRemoteAddr();
        }
        return ip.equals("0:0:0:0:0:0:0:1")?"127.0.0.1":ip;
    }


    @Override
    public String convert(ILoggingEvent iLoggingEvent) {

        if( RequestContextHolder.getRequestAttributes() instanceof ServletRequestAttributes){
            ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
            HttpServletRequest request = attributes.getRequest();
            return getIpAddr(request);
        }else{
            try {
                return InetAddress.getLocalHost().getHostAddress();
            } catch (UnknownHostException e) {
                e.printStackTrace();
            }
        }

        return null;
    }
}

```

#### OSNameLogConfig.java 

获取当前系统环境

```
package org.jumutang.com.common.logback;

import ch.qos.logback.core.PropertyDefinerBase;

public class OSNameLogConfig extends PropertyDefinerBase {

    private final static String macPath = "/Users/mumu/Documents/logs/";
    private final static String linuxPath = "/usr/local/services/logs/";
    private final static String windowsPath = "C:/logs/";

    @Override
    public String getPropertyValue() {
        String osName =  osName();
        return  osName;
    }

    public static String osName(){
        String osName = System.getProperty("os.name");
        if (osName.startsWith("Mac OS")) {
            // 苹果
            return macPath;
        } else if (osName.startsWith("Windows")) {
            // windows
            return windowsPath;
        } else {
            // unix or linux
            return linuxPath;
        }
    }

}

```

控制台打印日志：

![image-20210330170424789](%E6%90%AD%E5%BB%BA%E9%A1%B9%E7%9B%AE%E6%A1%86%E6%9E%B6%EF%BC%88SpringBoot%201.5.9%20%E7%89%88%E6%9C%AC%20%EF%BC%89.assets/image-20210330170424789.png)

注意：由于打印日志格式化输出有'tomcat_port'变量，在单元测试中，无法获取tomcat端口号信息导致未打印druid的sql执行日志

此处截图：启动项目，在controller中执行查询处理打印信息

```java
package org.jumutang.com.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RequestMapping(value = "/")
@RestController
public class TestController {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @RequestMapping(value = "/test")
    public String test(){

        Integer count = jdbcTemplate.queryForObject("select count(*) from t_orderinfo where c_status = 1 ",Integer.class);

        return String.valueOf(count);
    }

}

```



### logback-spring.xml文件更新

打印Druid慢sql到日志文件

```java
<?xml version="1.0" encoding="UTF-8"?>
<!--
scan:自动加载判断，当配置文件发生改变时，将会被重新加载。默认为true
scanPeriod:检测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认为毫秒，当scan=true时这个值生效，默认时间间隔为1分钟
debug:当被设置为true时，将打印出logback内部日志信息，实时查看logback运行信息，默认为false
-->
<configuration scan="false" scanPeriod="30 seconds" debug="false">

    <!--logback.xml的加载顺序早于springboot的application.yml,所以应使用logback-spring.xml文件-->

    <!--从配置文件中获取变量参数-->
    <springProperty scope="context" name="applicationName" source="spring.application.name"/>

    <!--上下文-->
    <contextName> ${applicationName} </contextName>

    <!--配置规则类的位置(日志输出语句中格式化输出自定义内容)-->
    <conversionRule conversionWord="ip" converterClass="org.jumutang.com.common.logback.IPLogConfig" />

    <!--获取tomcat的端口号信息-->
    <conversionRule conversionWord="tomcat_port" converterClass="org.jumutang.com.common.logback.GetTomcatPort" />

    <!--根据系统环境动态日志文件输出位置-->
    <define name="path" class="org.jumutang.com.common.logback.OSNameLogConfig" />

    <!--日志文件输出目录-->
    <property name="logFilePath" value="${path}/${applicationName}/" />

    <!--日志输出格式-->
    <!-- %X{变量名} 通过slf4j的MDC，当前线程中放入变量，使用前put，使用后remove-->
    <property name="logPrintFormat" value="%d %contextName %ip:%tomcat_port [%thread] %X{logUUID} %level %logger{36}.%M[%line] - %msg %n" />

    <!--控制台 -->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
                <pattern>${logPrintFormat}</pattern>
        </encoder>
    </appender>
    <!--输出日志-->
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!--新输出日志以追加方式在日志文件末尾-->
        <append>true</append>
        <encoder>
            <pattern>${logPrintFormat}</pattern>
            <charset>UTF-8</charset>
        </encoder>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--日志文件保存格式-->
            <fileNamePattern>${logFilePath}/info/%d{yyyyMMdd}_%i.log</fileNamePattern>
            <!--历史日志保留天数-->
            <maxHistory>60</maxHistory>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <!--单个日志大小-->
                <maxFileSize>20MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
    </appender>
    <!--输出错误日志-->
    <appender name="ERROR" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <append>true</append>
        <encoder>
            <pattern>${logPrintFormat}</pattern>
            <charset>UTF-8</charset>
        </encoder>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${logFilePath}/error/%d{yyyyMMdd}_%i.log</fileNamePattern>
            <!-- 按时间回滚的同时，按文件大小来回滚 -->
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>20MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
        <!--过滤指定级别的日志-->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <!--过滤 Error-->
            <level>ERROR</level>
            <!--匹配到就允许-->
            <onMatch>ACCEPT</onMatch>
            <!--没有匹配到就禁止-->
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>
    <!--根据taskId生成对应的文件单独保存-->
    <appender name="SIFT" class="ch.qos.logback.classic.sift.SiftingAppender">
        <!--discriminator鉴别器，根据taskId这个key对应的value鉴别日志事件，然后委托给具体appender写日志-->
        <discriminator>
            <key>taskId</key>
            <defaultValue>default</defaultValue>
        </discriminator>
        <sift>
            <!--根据taskId生成文件-->
            <appender name="File-${taskId}" class="ch.qos.logback.core.rolling.RollingFileAppender">
                <append>true</append>
                <encoder>
                    <pattern>${logPrintFormat}</pattern>
                    <charset>UTF-8</charset>
                </encoder>
                <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
                    <fileNamePattern>${logFilePath}/taskId/${taskId}/%d{yyyyMMdd}_%i.log</fileNamePattern>
                    <maxHistory>60</maxHistory>
                    <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                        <maxFileSize>20MB</maxFileSize>
                    </timeBasedFileNamingAndTriggeringPolicy>
                </rollingPolicy>
            </appender>
        </sift>
    </appender>
    <!-- 异步输出日志 -->
    <!--异步(防止线程的堵塞,在持续高IO下写入操作)且带taskId(可动态根据不同taskId写入不同的日志文件)的日志文件写入(未携带taskId的日志输出默认'default')-->
    <appender name ="ASYNC" class= "ch.qos.logback.classic.AsyncAppender">
        <!-- 不丢失日志.默认的,如果队列的80%已满,则会丢弃TRACT、DEBUG、INFO级别的日志 -->
        <discardingThreshold>0</discardingThreshold>
        <!-- 更改默认的队列的深度,该值会影响性能.默认值为256 -->
        <queueSize>512</queueSize>
        <!-- 添加附加的appender,最多只能添加一个 -->
<!--        <appender-ref ref ="SIFT"/>-->
        <appender-ref ref ="FILE"/>
        <!-- 新增这行为了打印栈堆信息(否则日志文件中可能会输出'?'字符，因为未携带堵栈堆信息) -->
        <includeCallerData>true</includeCallerData>
    </appender>
    <!--SQL执行日志-->
    <appender name="SQL" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!--新输出日志以追加方式在日志文件末尾-->
        <append>true</append>
        <encoder>
            <pattern>${logPrintFormat}</pattern>
            <charset>UTF-8</charset>
        </encoder>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--日志文件保存格式-->
            <fileNamePattern>${logFilePath}/sql/%d{yyyyMMdd}_%i.log</fileNamePattern>
            <!--历史日志保留天数-->
            <maxHistory>60</maxHistory>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <!--单个日志大小-->
                <maxFileSize>20MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
    </appender>

    <!--
        <logger>
            用来设置某一个包或者具体的某一个类的日志打印级别、以及指定<appender>。<loger>仅有一个name属性，一个可选的level和一个可选的addtivity属性。
            name: 用来指定受此logger约束的某一个包或者具体的某一个类。
            level: 用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL和OFF，还有一个特俗值INHERITED或者同义词NULL`，代表强制执行上级的级别。 如果未设置此属性，那么当前logger将会继承上级的级别。
            addtivity: 是否向上级logger传递打印信息。默认是true。
    -->
    <!-- 只要定义logger,当前目录/类的日志输出级别按照配置level='debug'级别进行输出 -->
    <logger name="druid.sql.Statement" level="debug"></logger>
    <!--<logger name="druid.sql.DataSource" level="debug"></logger>-->
    <!--<logger name="druid.sql.Connection" level="debug"></logger>-->
    <!--<logger name="druid.sql.ResultSet" level="debug"></logger>-->

    <!--将druid的慢sql输出到日志文件中-->
    <logger name="com.alibaba.druid.filter.stat.StatFilter" level="debug" additivity="false">
        <appender-ref ref="SQL" />
    </logger>

    <!--
        据不同环境（prod:生产环境，test:测试环境，dev:开发环境）来定义不同的日志输出，
        在 logback-spring.xml中使用 springProfile 节点来定义.
        application.properties增加环境选择的配置active: dev

        # 选择加载不同环境的配置文件 dev pre prod
        spring.profiles.active=dev
    -->
    <springProfile name="dev,test">
        <!--
           <root>也是<logger>元素，但是它是根logger。只有一个level属性，应为已经被命名为"root".
           level: 用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF，
                  不能设置为INHERITED或者同义词NULL。 默认是DEBUG。
           <logger>和<root>可以包含零个或多个<appender-ref>元素，标识这个appender将会添加到这个logger。
        -->
        <root level="info">
            <appender-ref ref="CONSOLE"/>
            <appender-ref ref="ERROR"/>
            <appender-ref ref="ASYNC"/>
        </root>
    </springProfile>

    <springProfile name="prod">
        <root level="info">
            <appender-ref ref="CONSOLE"/>
            <appender-ref ref="ERROR"/>
            <appender-ref ref="ASYNC"/> 
        </root>
    </springProfile>

    <!--注意：'ASYNC'和'FILE'两者可同时使用,但ASYNC的SIFT中会输出部分重复的FILE中info的日志-->
    <!--<appender-ref ref="ASYNC"/>-->
    <!--<appender-ref ref="FILE"/>-->
    
</configuration>
```



application-dev.yml配置文件更新

```java
#注释当前行
#connection-properties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=1000;druid.stat.logSlowSql=true  #测试发现,当前properties配置的优先级高于filter.stat的优先级(当存在相同属性配置情况下，当前配置会覆盖相同属性)

#filter更新：
     filter:
        # 开启druiddatasource的状态监控
        stat:
          enabled: true
          db-type: mysql
          log-slow-sql: true #打印慢sql日志
          slow-sql-millis: 10 #慢sql执行耗时基准线(超过配置时间为慢sql) 单位:毫秒
        # 日志监控，使用slf4j 进行日志输出
        slf4j:
          enabled: true
          statement-log-error-enabled: true #执行错误SQl
          statement-log-enabled: true #执行SQL('?'占位符)
          statement-executable-sql-log-enable: true #可执行SQL(将sql中的'?'替换为实际执行参数)
          #避免日志臃肿,简化日志信息
          statement-create-after-log-enabled: false
          statement-close-after-log-enabled: false
          result-set-open-after-log-enabled: false
          result-set-close-after-log-enabled: false


```





## 四.项目启动banner文件更新

```java
////////////////////////////////////////////////////////////////////
//                          _ooOoo_                               //
//                         o8888888o                              //
//                         88" . "88                              //
//                         (| ^_^ |)                              //
//                         O\  =  /O                              //
//                      ____/`---'\____                           //
//                    .'  \\|     |//  `.                         //
//                   /  \\|||  :  |||//  \                        //
//                  /  _||||| -:- |||||-  \                       //
//                  |   | \\\  -  /// |   |                       //
//                  | \_|  ''\---/''  |   |                       //
//                  \  .-\__  `-`  ___/-. /                       //
//                ___`. .'  /--.--\  `. . ___                     //
//              ."" '<  `.___\_<|>_/___.'  >'"".                  //
//            | | :  `- \`.;`\ _ /`;.`/ - ` : | |                 //
//            \  \ `-.   \_ __\ /__ _/   .-` /  /                 //
//      ========`-.____`-.___\_____/___.-`____.-'========         //
//                           `=---='                              //
//      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^        //
//             佛祖保佑          永无BUG         永不修改           //
////////////////////////////////////////////////////////////////////

```

## 五.集成mybatis功能模块

### 一.引入mybatis-generator 插件

```java
<!--generator插件，生成mapper文件-->
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.3.2</version>
                <configuration>
                    <!--配置文件的位置-->
                    <configurationFile>
                        src/main/resources/mybatis/mybatis-generator-config/generatorConfig.xml
                    </configurationFile>
                    <verbose>true</verbose>
                    <overwrite>true</overwrite>
                </configuration>
                <dependencies>
                    <!--mysql连接-->
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <version>5.1.44</version>
                    </dependency>
                    <!--mybatisGenator生成模板文件插件-->
                    <dependency>
                        <groupId>com.chrm</groupId>
                        <artifactId>mybatis-generator-lombok-plugin</artifactId>
                        <version>1.0-SNAPSHOT</version>
                        <scope>system</scope>
                        <systemPath>${project.basedir}/src/main/resources/lib/mybatis-generator-lombok-plugin-1.0-SNAPSHOT.jar</systemPath>
                    </dependency>
                </dependencies>
            </plugin>
```



依赖jar文件：mybatis-generator-lombok-plugin-1.0-SNAPSHOT.jar

![image-20210331162857710](%E6%90%AD%E5%BB%BA%E9%A1%B9%E7%9B%AE%E6%A1%86%E6%9E%B6%EF%BC%88SpringBoot%201.5.9%20%E7%89%88%E6%9C%AC%20%EF%BC%89.assets/image-20210331162857710.png)

### 二.pom文件更新依赖

```
<!--springboot整合mybatis-->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.0</version>
        </dependency>

        <!--lombok减少很多重复代码的书写-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.4</version>
        </dependency>
```

### 三.resource下创建目录

![image-20210331163112021](%E6%90%AD%E5%BB%BA%E9%A1%B9%E7%9B%AE%E6%A1%86%E6%9E%B6%EF%BC%88SpringBoot%201.5.9%20%E7%89%88%E6%9C%AC%20%EF%BC%89.assets/image-20210331163112021.png)

注意：mybatis-mapper目录下文件无需创建，由plugins-mybatis-generator创建

#### mybatis-config

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--<settings>
        &lt;!&ndash; 指定 MyBatis 所用日志的具体实现，未指定时将自动查找。 [SLF4J | LOG4J | LOG4J2 | JDK_LOGGING | COMMONS_LOGGING | STDOUT_LOGGING | NO_LOGGING] &ndash;&gt;
        &lt;!&ndash;<setting name="logImpl" value="SLF4J" />&ndash;&gt;
        &lt;!&ndash;指定 MyBatis 增加到日志名称的前缀。&ndash;&gt;
        &lt;!&ndash;<setting name="logPrefix" value="mybatis_log"/>&ndash;&gt;
        &lt;!&ndash;将以下画线方式命名的数据库列映射到 Java 对象的驼峰式命名属性中&ndash;&gt;
        &lt;!&ndash;<setting name= "mapUnderscoreToCamelCase" value="true" />&ndash;&gt;
    </settings>-->

    <!-- 起别名 -->
    <typeAliases>
    </typeAliases>

    <!--mybatis的interceptor拦截器插件-->
    <!--<plugins>
        <plugin interceptor="org.jumutang.com.mybatis_interceptor_plugins.MybatisInterceptorPlgins">
            &lt;!&ndash;默认SQL执行超时时间&ndash;&gt;
            <property name="CUSTOM_COST_TIME" value="-1"/>
            &lt;!&ndash;自定义通知消息实现方法&ndash;&gt;
            <property name="MYBATIS_SQL_INTERCEPTOR_IMPL" value="org.jumutang.com.interceptor_mng.conf.notice.MybatisSqlInterceptorCustomImpl"/>
        </plugin>
    </plugins>-->

</configuration>
```

#### mybatis-generator-config

generatorConfig.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<!-- 配置生成器 -->
<generatorConfiguration>
    <properties resource="mybatis/mybatis-generator-config/init_generator.properties"/>

    <context id="mysql" defaultModelType="flat" targetRuntime="MyBatis3" >

        <property name="autoDelimitKeywords" value="false"/>
        <property name="javaFileEncoding" value="UTF-8"/>

        <property name="javaFormatter" value="org.mybatis.generator.api.dom.DefaultJavaFormatter"/>
        <property name="xmlFormatter" value="org.mybatis.generator.api.dom.DefaultXmlFormatter"/>

        <property name="beginningDelimiter" value="`"/>
        <property name="endingDelimiter" value="`"/>

        <!-- 分页相关 -->
        <plugin type="org.mybatis.generator.plugins.RowBoundsPlugin" />
        <!-- 带上序列化接口 -->
        <plugin type="org.mybatis.generator.plugins.SerializablePlugin" />
        <!-- 自定义的注释生成插件-->
        <plugin type="com.chrm.mybatis.generator.plugins.CommentPlugin">
            <!-- 抑制警告 -->
            <property name="suppressTypeWarnings" value="true" />
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="false" />
            <!-- 是否生成注释代时间戳-->
            <property name="suppressDate" value="true" />
        </plugin>
        <!-- 整合lombok-->
        <plugin type="com.chrm.mybatis.generator.plugins.LombokPlugin" >
            <property name="hasLombok" value="true"/>
        </plugin>

        <commentGenerator >
            <property name="suppressAllComments" value="true"/>
            <property name="suppressDate" value="false" />
        </commentGenerator>

        <jdbcConnection driverClass="${jdbc.driverClasss}" connectionURL="${jdbc.connectionURL}" userId="${jdbc.userId}" password="${jdbc.password}"></jdbcConnection>

        <javaTypeResolver type="org.mybatis.generator.internal.types.JavaTypeResolverDefaultImpl">
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>

        <javaModelGenerator targetPackage="${entity.Package}" targetProject="${entity.Project}">
            <property name="constructorBased" value="false"/>
            <property name="enableSubPackages" value="true"/>
            <property name="immutable" value="false"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>

        <sqlMapGenerator targetPackage="${xml.Package}" targetProject="${xml.Project}">
            <property name="enableSubPackages" value="true"/>
        </sqlMapGenerator>

        <javaClientGenerator targetPackage="${mapper.Package}" type="${mapper.type}" targetProject="${mapper.Project}">
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>

        <table tableName="t_test"
               schema=""
               enableCountByExample="true"
               enableSelectByExample="true"
               enableDeleteByExample="true"
               enableUpdateByExample="true"
               selectByExampleQueryId="true">
            <property name="useActualColumnNames" value="true"/>
        </table>
    </context>

</generatorConfiguration>
```

init_generator.properties

```
#数据库链接配置参数
jdbc.driverClasss=com.mysql.jdbc.Driver
jdbc.connectionURL=jdbc:mysql://test2.juxinbox.com:3306/coupon_mng
jdbc.userId=jmtpublic
jdbc.password=rOO9SYiqeoxwz5VQX

#实体entity生成位置
entity.Project =src/main/java
entity.Package =org.jumutang.com.model.coupon_mng

#对应xml文件的接口实现类生成位置
mapper.type = XMLMAPPER
mapper.Project =src/main/java
mapper.Package =org.jumutang.com.dao.coupon_mng

#对应的xml文件生成位置
xml.Project =src/main/resources
xml.Package =mybatis/mybatis-mapper/coupon_mng

```

##### Mybatis配置文件解析

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<!-- 配置生成器 -->
<generatorConfiguration>
    <!-- 可以用于加载配置项或者配置文件，在整个配置文件中就可以使用${propertyKey}的方式来引用配置项
        resource：配置资源加载地址，使用resource，MBG从classpath开始找，比如com/myproject/generatorConfig.properties
        url：配置资源加载地质，使用URL的方式，比如file:///C:/myfolder/generatorConfig.properties.
        注意，两个属性只能选址一个;
        另外，如果使用了mybatis-generator-maven-plugin，那么在pom.xml中定义的properties都可以直接在generatorConfig.xml中使用
    <properties resource="" url="" />
     -->
    <properties resource="mybatisGeneratorConfig/init_generator.properties"/>

    <!-- 在MBG工作的时候，需要额外加载的依赖包
        location属性指明加载jar/zip包的全路径
        <classPathEntry location="/Program Files/IBM/SQLLIB/java/db2java.zip" />
     -->

    <!--
        context:生成一组对象的环境
        id:必选，上下文id，用于在生成错误时提示
        defaultModelType:指定生成对象的样式
            1，conditional：类似hierarchical；
            2，flat：所有内容（主键，blob）等全部生成在一个对象中；
            3，hierarchical：主键生成一个XXKey对象(key class)，Blob等单独生成一个对象，其他简单属性在一个对象中(record class)
        targetRuntime:
            1，MyBatis3：默认的值，生成基于MyBatis3.x以上版本的内容，包括XXXBySample；
            2，MyBatis3Simple：类似MyBatis3，只是不生成XXXBySample；
        introspectedColumnImpl：类全限定名，用于扩展MBG
    -->
    <context id="mysql" defaultModelType="flat" targetRuntime="MyBatis3" >

        <!-- 自动识别数据库关键字，默认false，如果设置为true，根据SqlReservedWords中定义的关键字列表；
            一般保留默认值，遇到数据库关键字（Java关键字），使用columnOverride覆盖
         -->
        <property name="autoDelimitKeywords" value="false"/>
        <!-- 生成的Java文件的编码 -->
        <property name="javaFileEncoding" value="UTF-8"/>
        <!-- 格式化java代码 -->
        <property name="javaFormatter" value="org.mybatis.generator.api.dom.DefaultJavaFormatter"/>
        <!-- 格式化XML代码 -->
        <property name="xmlFormatter" value="org.mybatis.generator.api.dom.DefaultXmlFormatter"/>

        <!-- beginningDelimiter和endingDelimiter：指明数据库的用于标记数据库对象名的符号，比如ORACLE就是双引号，MYSQL默认是`反引号； -->
        <property name="beginningDelimiter" value="`"/>
        <property name="endingDelimiter" value="`"/>

        <!-- 注释 -->
        <commentGenerator >
            <!-- 是否取消注释 -->
            <property name="suppressAllComments" value="true"/>
            <!-- 是否生成注释代时间戳-->
            <property name="suppressDate" value="false" />
        </commentGenerator>

        <!-- 必须要有的，使用这个配置链接数据库-->
        <jdbcConnection driverClass="${jdbc.driverClasss}" connectionURL="${jdbc.connectionURL}" userId="${jdbc.userId}" password="${jdbc.password}">
            <!-- 这里面可以设置property属性，每一个property属性都设置到配置的Driver上 -->
        </jdbcConnection>

        <!-- java类型处理器
            用于处理DB中的类型到Java中的类型，默认使用JavaTypeResolverDefaultImpl；
            注意一点，默认会先尝试使用Integer，Long，Short等来对应DECIMAL和 NUMERIC数据类型；
        -->
        <javaTypeResolver type="org.mybatis.generator.internal.types.JavaTypeResolverDefaultImpl">
            <!--
                true：使用BigDecimal对应DECIMAL和 NUMERIC数据类型
                false：默认,
                    scale>0;length>18：使用BigDecimal;
                    scale=0;length[10,18]：使用Long；
                    scale=0;length[5,9]：使用Integer；
                    scale=0;length<5：使用Short；
             -->
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>


        <!-- java模型创建器，是必须要的元素
            负责：1，key类（见context的defaultModelType）；2，java类；3，查询类
            targetPackage：生成的类要放的包，真实的包受enableSubPackages属性控制；
            targetProject：目标项目，指定一个存在的目录下，生成的内容会放到指定目录中，如果目录不存在，MBG不会自动建目录
         -->
        <javaModelGenerator targetPackage="${javaModelGenerator.targetPackage}" targetProject="${javaModelGenerator.targetPackage}">
            <!--  for MyBatis3/MyBatis3Simple
                自动为每一个生成的类创建一个构造方法，构造方法包含了所有的field；而不是使用setter；
             -->
            <property name="constructorBased" value="false"/>
            <!-- 在targetPackage的基础上，根据数据库的schema再生成一层package，最终生成的类放在这个package下，默认为false -->
            <property name="enableSubPackages" value="true"/>
            <!-- for MyBatis3 / MyBatis3Simple
                是否创建一个不可变的类，如果为true，
                那么MBG会创建一个没有setter方法的类，取而代之的是类似constructorBased的类-->
            <property name="immutable" value="false"/>
            <!-- 设置一个根对象，
                如果设置了这个根对象，那么生成的keyClass或者recordClass会继承这个类；在Table的rootClass属性中可以覆盖该选项
                注意：如果在key class或者record class中有root class相同的属性，MBG就不会重新生成这些属性了，包括：
                    1，属性名相同，类型相同，有相同的getter/setter方法；-->
            <!--<property name="rootClass" value="com._520it.mybatis.domain.BaseDomain"/>-->
            <!-- 设置是否在getter方法中，对String类型字段调用trim()方法 -->
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>
        <!-- 生成SQL map的XML文件生成器，
            注意，在Mybatis3之后，我们可以使用mapper.xml文件+Mapper接口（或者不用mapper接口），
                或者只使用Mapper接口+Annotation，所以，如果 javaClientGenerator配置中配置了需要生成XML的话，这个元素就必须配置
            targetPackage/targetProject:同javaModelGenerator
         -->
        <sqlMapGenerator targetPackage="${sqlMapGenerator.targetProject}" targetProject="src/main/resources">
            <!-- 在targetPackage的基础上，根据数据库的schema再生成一层package，最终生成的类放在这个package下，默认为false -->
            <property name="enableSubPackages" value="true"/>
        </sqlMapGenerator>
        <!-- 对于mybatis来说，即生成Mapper接口，注意，如果没有配置该元素，那么默认不会生成Mapper接口
            targetPackage/targetProject:同javaModelGenerator
            type：选择怎么生成mapper接口（在MyBatis3/MyBatis3Simple下）：
                1，ANNOTATEDMAPPER：会生成使用Mapper接口+Annotation的方式创建（SQL生成在annotation中），不会生成对应的XML；
                2，MIXEDMAPPER：使用混合配置，会生成Mapper接口，并适当添加合适的Annotation，但是XML会生成在XML中；
                3，XMLMAPPER：会生成Mapper接口，接口完全依赖XML；
            注意，如果context是MyBatis3Simple：只支持ANNOTATEDMAPPER和XMLMAPPER
        -->
        <javaClientGenerator targetPackage="${javaClientGenerator.targetPackage}" type="XMLMAPPER" targetProject="src/main/java">
            <!-- 在targetPackage的基础上，根据数据库的schema再生成一层package，最终生成的类放在这个package下，默认为false -->
            <property name="enableSubPackages" value="true"/>
            <!-- 可以为所有生成的接口添加一个父接口，但是MBG只负责生成，不负责检查
            <property name="rootInterface" value=""/>
             -->
        </javaClientGenerator>

        <!-- 选择一个table来生成相关文件，可以有一个或多个table，必须要有table元素
            选择的table会生成一下文件：
            1，SQL map文件
            2，生成一个主键类；
            3，除了BLOB和主键的其他字段的类；
            4，包含BLOB的类；
            5，一个用户生成动态查询的条件类（selectByExample, deleteByExample），可选；
            6，Mapper接口（可选）

            tableName（必要）：要生成对象的表名；
            注意：大小写敏感问题。正常情况下，MBG会自动的去识别数据库标识符的大小写敏感度，在一般情况下，MBG会
                根据设置的schema，catalog或tablename去查询数据表，按照下面的流程：
                1，如果schema，catalog或tablename中有空格，那么设置的是什么格式，就精确的使用指定的大小写格式去查询；
                2，否则，如果数据库的标识符使用大写的，那么MBG自动把表名变成大写再查找；
                3，否则，如果数据库的标识符使用小写的，那么MBG自动把表名变成小写再查找；
                4，否则，使用指定的大小写格式查询；
            另外的，如果在创建表的时候，使用的""把数据库对象规定大小写，就算数据库标识符是使用的大写，在这种情况下也会使用给定的大小写来创建表名；
            这个时候，请设置delimitIdentifiers="true"即可保留大小写格式；

            可选：
            1，schema：数据库的schema；
            2，catalog：数据库的catalog；
            3，alias：为数据表设置的别名，如果设置了alias，那么生成的所有的SELECT SQL语句中，列名会变成：alias_actualColumnName
            4，domainObjectName：生成的domain类的名字，如果不设置，直接使用表名作为domain类的名字；可以设置为somepck.domainName，那么会自动把domainName类再放到somepck包里面；
            5，enableInsert（默认true）：指定是否生成insert语句；
            6，enableSelectByPrimaryKey（默认true）：指定是否生成按照主键查询对象的语句（就是getById或get）；
            7，enableSelectByExample（默认true）：MyBatis3Simple为false，指定是否生成动态查询语句；
            8，enableUpdateByPrimaryKey（默认true）：指定是否生成按照主键修改对象的语句（即update)；
            9，enableDeleteByPrimaryKey（默认true）：指定是否生成按照主键删除对象的语句（即delete）；
            10，enableDeleteByExample（默认true）：MyBatis3Simple为false，指定是否生成动态删除语句；
            11，enableCountByExample（默认true）：MyBatis3Simple为false，指定是否生成动态查询总条数语句（用于分页的总条数查询）；
            12，enableUpdateByExample（默认true）：MyBatis3Simple为false，指定是否生成动态修改语句（只修改对象中不为空的属性）；
            13，modelType：参考context元素的defaultModelType，相当于覆盖；
            14，delimitIdentifiers：参考tableName的解释，注意，默认的delimitIdentifiers是双引号，如果类似MYSQL这样的数据库，使用的是`（反引号，那么还需要设置context的beginningDelimiter和endingDelimiter属性）
            15，delimitAllColumns：设置是否所有生成的SQL中的列名都使用标识符引起来。默认为false，delimitIdentifiers参考context的属性

            注意，table里面很多参数都是对javaModelGenerator，context等元素的默认属性的一个复写；
         -->
        <table tableName="t_order_info" >

            <!-- 参考 javaModelGenerator 的 constructorBased属性-->
            <property name="constructorBased" value="false"/>

            <!-- 默认为false，如果设置为true，在生成的SQL中，table名字不会加上catalog或schema； -->
            <property name="ignoreQualifiersAtRuntime" value="false"/>

            <!-- 参考 javaModelGenerator 的 immutable 属性 -->
            <property name="immutable" value="false"/>

            <!-- 指定是否只生成domain类，如果设置为true，只生成domain类，如果还配置了sqlMapGenerator，那么在mapper XML文件中，只生成resultMap元素 -->
            <property name="modelOnly" value="false"/>

            <!-- 参考 javaModelGenerator 的 rootClass 属性
            <property name="rootClass" value=""/>
             -->

            <!-- 参考javaClientGenerator 的  rootInterface 属性
            <property name="rootInterface" value=""/>
            -->

            <!-- 如果设置了runtimeCatalog，那么在生成的SQL中，使用该指定的catalog，而不是table元素上的catalog
            <property name="runtimeCatalog" value=""/>
            -->

            <!-- 如果设置了runtimeSchema，那么在生成的SQL中，使用该指定的schema，而不是table元素上的schema
            <property name="runtimeSchema" value=""/>
            -->

            <!-- 如果设置了runtimeTableName，那么在生成的SQL中，使用该指定的tablename，而不是table元素上的tablename
            <property name="runtimeTableName" value=""/>
            -->

            <!-- 注意，该属性只针对MyBatis3Simple有用；
                如果选择的runtime是MyBatis3Simple，那么会生成一个SelectAll方法，如果指定了selectAllOrderByClause，那么会在该SQL中添加指定的这个order条件；
             -->
            <property name="selectAllOrderByClause" value="age desc,username asc"/>

            <!-- 如果设置为true，生成的model类会直接使用column本身的名字，而不会再使用驼峰命名方法，比如BORN_DATE，生成的属性名字就是BORN_DATE,而不会是bornDate -->
            <property name="useActualColumnNames" value="false"/>


            <!-- generatedKey用于生成生成主键的方法，
                如果设置了该元素，MBG会在生成的<insert>元素中生成一条正确的<selectKey>元素，该元素可选
                column:主键的列名；
                sqlStatement：要生成的selectKey语句，有以下可选项：
                    Cloudscape:相当于selectKey的SQL为： VALUES IDENTITY_VAL_LOCAL()
                    DB2       :相当于selectKey的SQL为： VALUES IDENTITY_VAL_LOCAL()
                    DB2_MF    :相当于selectKey的SQL为：SELECT IDENTITY_VAL_LOCAL() FROM SYSIBM.SYSDUMMY1
                    Derby      :相当于selectKey的SQL为：VALUES IDENTITY_VAL_LOCAL()
                    HSQLDB      :相当于selectKey的SQL为：CALL IDENTITY()
                    Informix  :相当于selectKey的SQL为：select dbinfo('sqlca.sqlerrd1') from systables where tabid=1
                    MySql      :相当于selectKey的SQL为：SELECT LAST_INSERT_ID()
                    SqlServer :相当于selectKey的SQL为：SELECT SCOPE_IDENTITY()
                    SYBASE      :相当于selectKey的SQL为：SELECT @@IDENTITY
                    JDBC      :相当于在生成的insert元素上添加useGeneratedKeys="true"和keyProperty属性
            <generatedKey column="" sqlStatement=""/>
             -->

            <!--
                该元素会在根据表中列名计算对象属性名之前先重命名列名，非常适合用于表中的列都有公用的前缀字符串的时候，
                比如列名为：CUST_ID,CUST_NAME,CUST_EMAIL,CUST_ADDRESS等；
                那么就可以设置searchString为"^CUST_"，并使用空白替换，那么生成的Customer对象中的属性名称就不是
                custId,custName等，而是先被替换为ID,NAME,EMAIL,然后变成属性：id，name，email；

                注意，MBG是使用java.util.regex.Matcher.replaceAll来替换searchString和replaceString的，
                如果使用了columnOverride元素，该属性无效；

            <columnRenamingRule searchString="" replaceString=""/>
             -->


            <!-- 用来修改表中某个列的属性，MBG会使用修改后的列来生成domain的属性；
                column:要重新设置的列名；
                注意，一个table元素中可以有多个columnOverride元素哈~
             -->
            <columnOverride column="username">
                <!-- 使用property属性来指定列要生成的属性名称 -->
                <property name="property" value="userName"/>

                <!-- javaType用于指定生成的domain的属性类型，使用类型的全限定名
                <property name="javaType" value=""/>
                 -->

                <!-- jdbcType用于指定该列的JDBC类型
                <property name="jdbcType" value=""/>
                 -->

                <!-- typeHandler 用于指定该列使用到的TypeHandler，如果要指定，配置类型处理器的全限定名
                    注意，mybatis中，不会生成到mybatis-config.xml中的typeHandler
                    只会生成类似：where id = #{id,jdbcType=BIGINT,typeHandler=com._520it.mybatis.MyTypeHandler}的参数描述
                <property name="jdbcType" value=""/>
                -->

                <!-- 参考table元素的delimitAllColumns配置，默认为false
                <property name="delimitedColumnName" value=""/>
                 -->
            </columnOverride>

            <!-- ignoreColumn设置一个MGB忽略的列，如果设置了改列，那么在生成的domain中，生成的SQL中，都不会有该列出现
                column:指定要忽略的列的名字；
                delimitedColumnName：参考table元素的delimitAllColumns配置，默认为false

                注意，一个table元素中可以有多个ignoreColumn元素
            <ignoreColumn column="deptId" delimitedColumnName=""/>
            -->
        </table>

    </context>

</generatorConfiguration>
```

### 四.application.yml文件增加支持mybatis

```
mybatis:
  #实体类对象目录
  type-aliases-package: org.jumutang.com.model.*
  #xml文件映射目录
  mapper-locations: classpath:mybatis/mybatis-mapper/**/*.xml
  #mybatis的配置文件目录
  config-location: classpath:mybatis/mybatis-config/mybatis-config.xml
```

### 五.application启动类开启扫描mapper对象目录

```
@MapperScan("org.jumutang.com.dao.**")
```

![image-20210331163723067](%E6%90%AD%E5%BB%BA%E9%A1%B9%E7%9B%AE%E6%A1%86%E6%9E%B6%EF%BC%88SpringBoot%201.5.9%20%E7%89%88%E6%9C%AC%20%EF%BC%89.assets/image-20210331163723067.png)

## 六.集成redis功能

### 一.pom文件更新redis依赖配置

```
<!--spring boot 与redis应用基本环境配置 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
        <!--适用于redisTemplate的连接池配置(jedis必须是2.9或以后的版本)-->
        <!--redisTempalte的存对象数据序列化工具【已经集成了,切勿引入'jackson-databind',导致冲突报错!!!】-->
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-pool2</artifactId>
        </dependency>
```

### 二.配置文件增加redis

```
//前缀spring

  redis:
    database: 0 # Redis数据库索引（默认为0）
    host: your_host # Redis服务器地址
    port: 6379 # Redis服务器连接端口
    password: your_password # Redis服务器连接密码（默认为空）
    pool:
      max-active: 8 #连接池最大连接数（使用负值表示没有限制）
      max-idle: 8 # 连接池中的最大空闲连接
      max-wait: -1 # 连接池最大阻塞等待时间（使用负值表示没有限制）
      min-idle: 2 # 连接池中的最小空闲连接
    timeout: 300 # 连接超时时间（毫秒）
```

### 三.RedisConfig配置类

```
package org.jumutang.com.common.redis;

import com.fasterxml.jackson.annotation.JsonAutoDetect;
import com.fasterxml.jackson.annotation.PropertyAccessor;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.Jackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;


@Configuration
public class RedisConfig {

    /**
     * @desc retemplate相关配置
     */
    @Bean
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<Object, Object> redisTemplate = new RedisTemplate<Object, Object>();
        redisTemplate.setConnectionFactory(redisConnectionFactory);

        //使用Jackson2JsonRedisSerializer来序列化和反序列化redis的value值（默认使用JDK的序列化方式）
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);

        ObjectMapper objectMapper = new ObjectMapper();
        // 指定要序列化的域，field,get和set,以及修饰符范围，ANY是都有包括private和public
        objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        // 指定序列化输入的类型，类必须是非final修饰的，final修饰的类，比如String,Integer等会跑出异常
        objectMapper.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);

        jackson2JsonRedisSerializer.setObjectMapper(objectMapper);

        //使用StringRedisSerializer来序列化和反序列化redis的key值
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        // 值采用json序列化
        redisTemplate.setValueSerializer(jackson2JsonRedisSerializer);

        // 设置hash key 和value序列化模式
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashValueSerializer(jackson2JsonRedisSerializer);

        redisTemplate.afterPropertiesSet();
        return redisTemplate;
    }


}
```

### 四.RedisDaoImpl类

```
package org.jumutang.com.common.redis;


import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.data.redis.support.atomic.RedisAtomicLong;
import org.springframework.stereotype.Repository;

import java.util.List;
import java.util.Map;
import java.util.Set;
import java.util.concurrent.TimeUnit;


/**
 * Created by Administrator on 2017/10/16.
 */
@Repository
@Slf4j
public class RedisDaoImpl {

    @Autowired
    protected StringRedisTemplate stringRedisTemplate;
    //自动序列化对象[不需要手动处理]
    @Autowired
    protected RedisTemplate<Object,Object> redisTemplate;

    //判断当前key是否存在
    public boolean IsKeyExist(String key){
        return stringRedisTemplate.hasKey(key);
    }

    //存入value并指定过期时间
    public void SaveKeyValue(String key,String values,long time,TimeUnit timeUnit){
        if(time<=0){
            stringRedisTemplate.opsForValue().set(key, values);
        }else {
            //向redis里存入数据和设置缓存时间
            stringRedisTemplate.opsForValue().set(key, values,time,timeUnit);
        }
    }

    //对指定key的value增加+1或者减-1
    public void IncrementKeyValue (String key ,int number){
        stringRedisTemplate.boundValueOps(key).increment(number);
    }

    //对指定key的value增加+1或者减-1
    public void IncrementKeyValue(String key) {
        RedisAtomicLong entityIdCounter = new RedisAtomicLong(key, redisTemplate.getConnectionFactory());
        entityIdCounter.set(entityIdCounter.addAndGet(1));
    }

    //获取指定key的value值
    public String  QueryKeyValue(String key){
        //根据key获取缓存中的val
       String keyValue = stringRedisTemplate.opsForValue().get(key);
        return keyValue;
    }

    //存入对象[jackson序列化对象]
    public void saveObj(String key,Object value,long time ,TimeUnit timeUnit){
        if(time<=0){
            redisTemplate.opsForValue().set(key,value);
        }else{
            redisTemplate.opsForValue().set(key,value,time,timeUnit);
        }

    }

    //取出对象[jackson序列化对象]
    public Object queryObj(String key){
        return  redisTemplate.opsForValue().get(key);
    }

    //移除当前key
    public void removeKey(String key){
        stringRedisTemplate.delete(key);
    }

    //模糊查询keys
    public Set<Object> keys(Object pattern){
        return redisTemplate.keys(pattern);
    }

    //资料https://www.cnblogs.com/alter888/p/9082264.html
    //存储list 将所有指定的值插入存储在键的列表的头部。如果键不存在，则在执行推送操作之前将其创建为空列表。（从左边插入）
    public long leftPushList(String key ,Object object){
        return  redisTemplate.opsForList().leftPush(key,object);
    }

    //读取list,返回存储在键中的列表的指定元素。偏移开始和停止是基于零的索引，其中0是列表的第一个元素（列表的头部），1是下一个元素
    public List<Object> rangeList(String key, long start, long end){
        return redisTemplate.opsForList().range(key,start,end);
    }

    //获取list长度,返回存储在键中的列表的长度。如果键不存在，则将其解释为空列表，并返回0。当key存储的值不是列表时返回错误。
    public long sizeList(String key){
        return redisTemplate.opsForList().size(key);
    }

    //重新设置list当前索引处的值
    public void setList(String key,long index,Object object){
         redisTemplate.opsForList().set(key,index,object);
    }

    //从存储在键中的列表中删除等于值的元素的第一个计数事件。
    //计数参数以下列方式影响操作：
    //count>0：删除等于从头到尾移动的值的元素。
    //count<0：删除等于从尾到头移动的值的元素。
    //count =0：删除等于value的所有元素。
    public void removeList(String  key, long count, Object value){
        redisTemplate.opsForList().remove(key,count,value);
    }

    //弹出最右边的元素，弹出之后该值在列表中将不复存在
    //移除最后一个元素
    public void rightPopList(String key){
        redisTemplate.opsForList().rightPop(key);
    }


    //存入多个变量_Map集合
    public void SaveMapParams(String key, Map<Object, Object> map) {
        stringRedisTemplate.opsForHash().putAll(key, map);
    }

    //存入单个变量
    public void SaveMapParam(String key, String paramsName, String paramsValue, long timeEnd, TimeUnit timeUnit) {
        stringRedisTemplate.opsForHash().put(key, paramsName, paramsValue);
        if (timeEnd > 0) {
            //设置过期时间
            stringRedisTemplate.expire(key, timeEnd, timeUnit);
        }
    }

    //查询当前map集合中指定key是否存在
    public boolean QueryMapKeyExits(String mapKey, String key) {
        return stringRedisTemplate.opsForHash().hasKey(mapKey, key);
    }

    //获取单个指定map中的key的返回值
    public Object QueryMapKeyValue(String mapKey, String key) {
        return stringRedisTemplate.opsForHash().get(mapKey, key);
    }

    //删除单个指定map中的key
    public Object removeMapKeyValue(String mapKey, String key) {
        return stringRedisTemplate.opsForHash().delete(mapKey, key);
    }

    ////根据key获取过期时间并换算成指定单位
    public Long getExpire(String key){
       return stringRedisTemplate.getExpire(key, TimeUnit.SECONDS);
    }

    //setNx
    public boolean setIfAbsent(String key, int number, long time, TimeUnit timeUnit) {
        boolean flag = redisTemplate.opsForValue().setIfAbsent(key, number);
        if (flag) {
            //设置过期时间
            stringRedisTemplate.expire(key, time, timeUnit);
        }
        return flag;
    }
}


```

## 七.项目框架附属功能新增优化

### 1.Interceptor拦截器

#### 拦截器实现配置

```
package org.jumutang.com.common.interceptor;

import org.jumutang.com.util.logtask.logtaskid.ILogTask;
import org.jumutang.com.util.logtask.logtaskid.ILogTaskImpl;
import org.jumutang.com.util.logtask.loguuid.ILogUUID;
import org.jumutang.com.util.logtask.loguuid.ILogUUIDImpl;
import org.jumutang.com.util.tokenverify.ITokenVerify;
import org.jumutang.com.util.tokenverify.ITokenVerifyImpl;
import org.springframework.web.method.HandlerMethod;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;



/**
 * Created by Administrator on 2018/1/4.
 * @solve Interceptor中spring注入失败解决 (https://blog.csdn.net/github_39525088/article/details/75453970)
 */
public class Interceptor implements HandlerInterceptor {

    /**
     * @desc 生成logUUID
     * */
    private ILogUUID ILogUUID;

    private ILogTask ILogTask;

    private ITokenVerify tokenVerify;

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object o) throws Exception {
        ILogUUID = new ILogUUIDImpl();
        ILogTask = new ILogTaskImpl((HandlerMethod) o);
        tokenVerify = new ITokenVerifyImpl(request,response,(HandlerMethod) o);

        //日志记录标识logUUID
        ILogUUID.addLogUUID();
        //日志分割标识taskID
        ILogTask.addLogTaskId();

        if( tokenVerify.haveToken() ) return tokenVerify.checkToken();

        return true;
    }

    @Override
    public void postHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, ModelAndView modelAndView) throws Exception {

    }

    @Override
    public void afterCompletion(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) throws Exception {
        ILogUUID.removeLogUUID();
        ILogTask.removeLogTaskId();
    }

}

```

webMvcConfigure 配置拦截器

```
package org.jumutang.com.common.interceptor;

import lombok.extern.slf4j.Slf4j;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter;

/**
 * Created by Administrator on 2018/1/4.
 */

@Configuration
public class WebMvcConfigure extends WebMvcConfigurerAdapter {

    private Logger logger = LoggerFactory.getLogger(WebMvcConfigure.class);

    /**
     * @readMe 要在拦截器中使用spring注入,首先必须注册拦截器到spring容器中
     * */
    @Bean
    public HandlerInterceptor myInterface(){
        return new Interceptor();
    }

    /**
     * 静态资源配置 [默认优先搜索当前配置目录下资源文件是否存在]
     * 默认: META-INF/resources > resources > static > public
     * */
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        logger.info("匹配规则/**请求addResourceHandlers-->/web/目录");
        //配置访问请求handleer和当前资源文件目录位置
        registry.addResourceHandler("/**").addResourceLocations("classpath:/static/");
        //Swagger2接口api文档页面访问地址
        registry.addResourceHandler("swagger-ui.html").addResourceLocations("classpath:/META-INF/resources/");
        super.addResourceHandlers(registry);
    }


    /**
     * 配置拦截器interceptor [拦截指定规则请求]
     * */
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        logger.info("匹配规则/**addInterceptors-->请求到拦截器");
        //addPathPatterns 用于添加拦截规则
        String [] addPathPatterns = {"/**"};
        //excludePatterns 用于排除拦截 [访问请求不经过interceptor拦截器]
        //Swagger2文档接口
        String [] excludePatterns = {"/swagger-resources/**"};
        registry.addInterceptor(myInterface()).addPathPatterns(addPathPatterns).excludePathPatterns(excludePatterns);
        super.addInterceptors(registry);
    }


    /**
     * 页面跳转 [根据视图名配置自定义跳转路径]
     * 直接根据访问路径配置跳转地址
     * */
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        logger.info("匹配规则指定viewName视图名addViewControllers-->指定地址Html");
        super.addViewControllers(registry);
    }




}

```



#### 自定义注解

##### LogBack

```
package org.jumutang.com.common.annotation;

import org.springframework.core.Ordered;
import org.springframework.core.annotation.Order;

import java.lang.annotation.*;

/**
 * @desc 是否切割打印日志到指定目录
 * @author Created by MuYongRong on 2018/5/11.
 */
//ElementType.TYPE //类、接口、枚举
//        ElementType.FIELD //属性
//        ElementType.METHOD //方法
//        ElementType.PARAMETER //参数
//        ElementType.CONSTRUCTOR //构造器
//        ElementType.LOCAL_VARIABLE //局部变量
//        ElementType.ANNOTATION_TYPE //注解
//        ElementType.PACKAGE //包

@Target({ElementType.METHOD, ElementType.FIELD, ElementType.TYPE})
// 保留时间，一般注解就是为了框架开发时代替配置文件使用，JVM运行时用反射取参数处理，所以一般都为RUNTIME类型
@Retention(RetentionPolicy.RUNTIME )
//指明该注解类型被自动继承。如果一个annotation注解被@Inherited修饰，那么该注解作用于的类 的子类也会使用该annotation注解。
@Inherited
// 用于描述其它类型的annotation应该被作为被标注的程序成员的公共API，因此可以被例如javadoc此类的工具文档化
@Documented
//最高优先级
@Order(Ordered.HIGHEST_PRECEDENCE)
public @interface LogBack {

    String taskId() default "" ;


}

```

##### TokenVerify

```
package org.jumutang.com.common.annotation;

import org.springframework.core.Ordered;
import org.springframework.core.annotation.Order;

import java.lang.annotation.*;

/**
 * @desc 是否需要接口凭证校验
 * @author Created by MuYongRong on 2018/5/11.
 */
//ElementType.TYPE //类、接口、枚举
//        ElementType.FIELD //属性
//        ElementType.METHOD //方法
//        ElementType.PARAMETER //参数
//        ElementType.CONSTRUCTOR //构造器
//        ElementType.LOCAL_VARIABLE //局部变量
//        ElementType.ANNOTATION_TYPE //注解
//        ElementType.PACKAGE //包

@Target({ElementType.METHOD, ElementType.FIELD,ElementType.TYPE})
// 保留时间，一般注解就是为了框架开发时代替配置文件使用，JVM运行时用反射取参数处理，所以一般都为RUNTIME类型
@Retention(RetentionPolicy.RUNTIME )
//指明该注解类型被自动继承。如果一个annotation注解被@Inherited修饰，那么该注解作用于的类 的子类也会使用该annotation注解。
@Inherited
// 用于描述其它类型的annotation应该被作为被标注的程序成员的公共API，因此可以被例如javadoc此类的工具文档化
@Documented
//最高优先级
@Order(Ordered.HIGHEST_PRECEDENCE)
public @interface TokenVerify {

    //token校验
    String desc() default "" ;

}

```

#### 接口及实现类

##### ILogBack

```
package org.jumutang.com.util.logtask.logtaskid;

import org.springframework.web.method.HandlerMethod;

public interface ILogTask {

    void addLogTaskId();

    void removeLogTaskId();

    String returnTaskId();
}
	
```

###### ILogTaskImpl

```
package org.jumutang.com.util.logtask.logtaskid;

import org.jumutang.com.common.annotation.LogBack;
import org.slf4j.MDC;
import org.springframework.util.StringUtils;
import org.springframework.web.method.HandlerMethod;

import static org.jumutang.com.common.constant.Constant.TASKID;

public class ILogTaskImpl implements ILogTask {

    private HandlerMethod handlerMethod;

    public ILogTaskImpl(HandlerMethod handlerMethod) {
        this.handlerMethod = handlerMethod;
    }

    @Override
    public void addLogTaskId() {
        //判断是否单独写日志到文件
        String taskId = returnTaskId();
        if( !StringUtils.isEmpty(taskId) ){
            //日志分割taskId
            MDC.put(TASKID,taskId);
        }
    }

    @Override
    public String returnTaskId() {
        /**
         * 更新logback的taskId优先级： 1.优先查询方法体；2.其次查询类
         * */
        //方法
        if(handlerMethod.getMethod().isAnnotationPresent(LogBack.class)){
            LogBack logBack = handlerMethod.getMethodAnnotation(LogBack.class);
            return logBack.taskId();
        }else{
            //类
            boolean tokenAnnotation =  handlerMethod.getBean().getClass().isAnnotationPresent(LogBack.class);
            if( tokenAnnotation ){
                LogBack logBack = handlerMethod.getBean().getClass().getAnnotation(LogBack.class);
                return logBack.taskId();
            }else {
                return null;
            }
        }
    }

    @Override
    public void removeLogTaskId() {
        String taskId = returnTaskId();
        if( !StringUtils.isEmpty(taskId) ){
            MDC.remove(TASKID);
        }
    }
}

```

##### ILogUUID

```
package org.jumutang.com.util.logtask.loguuid;

public interface ILogUUID {

    String createLogUUID();

    void addLogUUID();

    void removeLogUUID();

}

```

###### ILogUUIDImpl

```
package org.jumutang.com.util.logtask.loguuid;

import org.slf4j.MDC;

import static org.jumutang.com.common.constant.Constant.LOGUUID;

public class ILogUUIDImpl implements ILogUUID {


    @Override
    public void addLogUUID() {
        MDC.put(LOGUUID, createLogUUID() );
    }

    @Override
    public void removeLogUUID() {
        MDC.remove(LOGUUID);

    }

    @Override
    public String createLogUUID() {
        return java.util.UUID.randomUUID().toString().replaceAll("-", "").toUpperCase().substring(0,12);
    }
}

```

##### ITokenVerify

```
package org.jumutang.com.util.tokenverify;

public interface ITokenVerify {

    boolean haveToken();

    boolean checkToken();

    void failCheck();

    void successCheck();

}

```

###### ITokenVerifyImpl

```
package org.jumutang.com.util.tokenverify;

import com.alibaba.fastjson.JSONObject;
import org.jumutang.com.common.annotation.TokenVerify;
import org.jumutang.com.util.JWTUtil;
import org.jumutang.com.util.QueryAnnotationUtil;
import org.springframework.util.StringUtils;
import org.springframework.web.method.HandlerMethod;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.PrintWriter;
import java.util.Map;

import static org.jumutang.com.util.JWTUtil.initParams;

public class ITokenVerifyImpl implements  ITokenVerify {

    private HttpServletRequest request;
    private HttpServletResponse response;
    private HandlerMethod handlerMethod;

    public ITokenVerifyImpl(HttpServletRequest request, HttpServletResponse response, HandlerMethod handlerMethod) {
        this.request = request;
        this.response = response;
        this.handlerMethod = handlerMethod;
    }

    @Override
    public boolean haveToken() {
        /*校验是否有自定义注解*/
        if(!StringUtils.isEmpty(new QueryAnnotationUtil(handlerMethod).QueryAnnotation(TokenVerify.class))) return true;

        return false;
    }

    @Override
    public boolean checkToken() {
        String token = request.getParameter("token");
        if( StringUtils.isEmpty(token) || "".equals(token) ){
            token = request.getHeader("token");
        }
        boolean tokenCheck = false;

        try {
            //初始化获取Token中的参数
            Map<String,String> map = initParams(token);

            //校验token正确性
            tokenCheck = JWTUtil.verify(token,map.get("unique"));

            if(tokenCheck){
                successCheck();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }

        failCheck();

        return tokenCheck;
    }

    @Override
    public void failCheck() {
        response.reset();

        response.setCharacterEncoding("UTF-8");
        response.setContentType("application/json; charset=utf-8");
        PrintWriter out = null ;
        try{
            out = response.getWriter();
            JSONObject jsonObject = new JSONObject();
            jsonObject.put("code","-1");
            jsonObject.put("msg","登录过期，请重新登录");
            //返回全局变量,接口已过期!
            out.append(jsonObject.toString());
        }
        catch (Exception e){
            e.printStackTrace();
        }finally {
            out.close();
        }
    }

    @Override
    public void successCheck() {

    }
}

```



#### 工具类

##### JWTUtil

```
package org.jumutang.com.util;

import com.auth0.jwt.JWT;
import com.auth0.jwt.JWTCreator;
import com.auth0.jwt.algorithms.Algorithm;
import com.auth0.jwt.exceptions.JWTDecodeException;
import com.auth0.jwt.interfaces.Claim;
import com.auth0.jwt.interfaces.DecodedJWT;
import com.auth0.jwt.interfaces.Verification;
import lombok.extern.slf4j.Slf4j;
import org.springframework.util.StringUtils;

import java.util.Date;
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.TimeUnit;


@Slf4j
public class JWTUtil {

    /**
     * 加密获取token
     * @param params token中保存的参数配置信息
     * @param expire token过期时间
     */
    public static String sign(Map<String,String> params ,String secret , long expire,TimeUnit timeUnit) {
        try {
            //加入用户密码,使这个token加密的层级更高
            Algorithm algorithm = null;
            if( !StringUtils.isEmpty(secret) ){
                algorithm =  Algorithm.HMAC256(secret);
            }else{
                algorithm =  Algorithm.none();
            }

            //jwt生成token的对象初始化
            JWTCreator.Builder builder = JWT.create();
            if(!StringUtils.isEmpty(params)) {
                //在token中存入指定参数信息
                for(Map.Entry<String,String> p : params.entrySet() ){
                    //为了防止base64反解析后能获取token中的明文数据,这里用secret把参数进行复加密
                    //builder.withClaim(p.getKey(),DesUtil.encrypt(p.getValue(),secret));
                    //若不加密,请只保存非敏感信息在头部文件中
                    builder.withClaim(p.getKey(), p.getValue());
                }
            }
            //配置加密token的过期时间
            builder.withExpiresAt(new Date(System.currentTimeMillis()+timeUnit.toMillis(expire)));
            //返回加密后token
            return builder.sign(algorithm);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }

    /**
     * 校验token是否正确
     * @param token 密钥
     * @return 是否正确
     */
    public static boolean verify(String token,String secret) {
        try {
            Algorithm algorithm = null;
            if( !StringUtils.isEmpty(secret) ){
                algorithm =  Algorithm.HMAC256(secret);
            }else{
                algorithm =  Algorithm.none();
            }
            Verification verification = JWT.require(algorithm);
            DecodedJWT jwt = verification.build().verify(token);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 获得token中的信息无需secret解密也能获得
     */
    public static String getPoyload(String params,String token) {
        try {
            DecodedJWT jwt = JWT.decode(token);
            return jwt.getClaim(params).asString();
        } catch (JWTDecodeException e) {
            e.printStackTrace();
            return null;
        }
    }

    /**
    * @Description: 初始化加载token中参数
    * @url
    * @Param: [token]
    * @Author: myr
    * @Date: 2019/3/13 10:06
    * @return: java.util.Map<java.lang.String,java.lang.String>
    */
    public static Map<String,String>  initParams (String token){
        Map<String,String> map = new HashMap<>();
        try {
            DecodedJWT jwt = JWT.decode(token);
            for( Map.Entry<String,Claim> m : jwt.getClaims().entrySet() ){
                map.put(m.getKey(),m.getValue().asString());
            }
        } catch (JWTDecodeException e) {
            e.printStackTrace();
        }
        return map;
    }


}

```

pom文件更新(jwt)

```
  <!--JWT-->
        <dependency>
            <groupId>com.auth0</groupId>
            <artifactId>java-jwt</artifactId>
            <version>3.2.0</version>
        </dependency>
```

##### QueryAnnotationUtil

```
package org.jumutang.com.util;

import org.springframework.web.method.HandlerMethod;

import java.lang.annotation.Annotation;

public class QueryAnnotationUtil {

    private HandlerMethod handlerMethod;

    public QueryAnnotationUtil(HandlerMethod handlerMethod) {
        this.handlerMethod = handlerMethod;
    }

    /**
     * @desc 校验方法或类是否指定注解
     * @author muyr
     * @date 2021/04/06
     * */
    public Annotation QueryAnnotation(Class<? extends Annotation> annotationClass){
        //方法
        if(handlerMethod.getMethod().isAnnotationPresent(annotationClass)){
            return handlerMethod.getMethodAnnotation(annotationClass);
        }else{
            //类
            boolean tokenAnnotation =  handlerMethod.getBean().getClass().isAnnotationPresent(annotationClass);
            if( tokenAnnotation ){
                return  handlerMethod.getBean().getClass().getAnnotation(annotationClass);
            }else {
                return null;
            }
        }
    }

}

```





### 2.Aop切面事务

### 3.统一异常处理及返回reusltUtil

### 4.接口调用Token令牌