
# Spring Boot Admin

* ### [简介](#description) 
* ### [快速使用](#fastUse) 

<hr>
<br>
<h3 id="description">简介</h2>
&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp Spring Boot Admin是一款管理和监控Spring Boot应用程序的应用程序。应用程序向我们的Spring Boot Admin Client注册（通过HTTP）或者使用Spring Cloud（例如Eureka）发现。UI只是Spring Boot Actuator端点上的一个AngularJs应用程序。<br>&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp上诉是引自Spring Boot Admin官方文档，而本文做的是使用它提供的消息提醒功能来监控微服务项目的健康状态。
<br><br>
<h3 id="fastUse">快速使用</h2>
&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp 在此Server端和Client端分开来说
<br>
&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp Server端配置:<br>

```xml
    <!--pom引入依赖-->
        <dependency>
            <groupId>de.codecentric</groupId>
            <artifactId>spring-boot-admin-server</artifactId>
            <version>1.5.0</version>
        </dependency>
        <dependency>
            <groupId>de.codecentric</groupId>
            <artifactId>spring-boot-admin-server-ui</artifactId>
            <version>1.5.0</version>
        </dependency>
```
```xml
<!--主配置文件-->
<!--声明Server端地址-->
spring.boot.admin.url=http://127.0.0.1:8080
<!--关闭安全认证-->
management.security.enabled=false
<!--启用slack提醒-->
spring.boot.admin.notify.slack.enabled=true
```
```java
/**
 * 创建自定义提醒类继承 SlackNotifier，重写 doNotify()
 * 当受此Server端管理的Client端上线或离线时会触发该方法
 */
public class SlackReminder extends SlackNotifier {
    @Override
    protected void doNotify(ClientApplicationEvent event){
        <!--微服务名字-->
        String appName = event.getApplication().getName();
        <!--触发状态,这里只有两种状态(UP/OFFLINE)-->
        String appStatus = event.getApplication().getStatusInfo().getStatus();
        System.out.println(appName);
        System.out.println(appStatus);
        }
    }
}
```
```java
/**
 * 把自定义提醒类交给RemindingNotifier
 */
@Configuration
@EnableScheduling
public class NotifierConfiguration {
    private Notifier notifier = new SlackReminder();
    @Bean
    @Primary
    public RemindingNotifier remindingNotifier() {
        RemindingNotifier remindingNotifier = new RemindingNotifier(notifier);
        return remindingNotifier;
    }
}
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Client端配置:<br>
```xml
<!--pom引入依赖-->
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-client</artifactId>
    <version>1.5.0</version>
</dependency>
```
```xml
<!--主配置文件-->
<!--声明Server地址-->
spring.boot.admin.url=http://127.0.0.1:8080
<!--关闭安全认证-->
management.security.enabled=false
```

OVER~