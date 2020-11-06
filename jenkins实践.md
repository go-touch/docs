## Jenkins 部署

```bash
安装 java8 yum install -y java-1.8.0-openjdk.x86_64
```

```bash
java -jar jenkins.war --httpPort=8080
```



### 常见问题处理

1. 忘记登录密码

```bash
修改 JENKINS_HOME/users/usernamedir 目录下的config.xml  文件

注：usernamedir 就是你忘记密码对应的用户文件夹
如果是java -jar方式启动的，HOME目录位于/root/.jenkins


修改config.xml，找到 passwordHash部分，替换原有的hash值



  把
  #jbcrypt:$2a$10$DdaWzN64JgUtLdvxWIflcuQu2fgrrMSAMabF5TSrGK5nXitqK9ZMS
  修改为
  #jbcrypt:$2a$10$4NW.9hNVyltZlHzrNOOjlOgfGrGUkZEpBfhkaUrb7ODQKBVmKRcmK
  后者是123456的hash值

重启Jenkins 
/etc/init.d/jenkins restart

用最新的密码 111111 登录 

搞定 。
```

