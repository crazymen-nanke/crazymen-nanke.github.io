---
layout: post
title: "Java程序连接MySQL数据库"
date: 2022-07-13 11:48:15
categories: Java
tags:   Java MySQL JDBC
author: NanKe
---

* content
{:toc}
> 记录用Java程序连接MySQL数据，使用JDBC连接数据库，提供详细代码以及SQL代码



# 前言
`提示：记录使用的数据库版本`

MySQL数据库的版本Server version: 5.1.73-community MySQL Community Server (GPL)

# 一、数据库准备
我们创建名字为test的数据库，并且分别创建名字为dept与employee的表。
插入数据到两张表之后，笔者创建了数据库test用户，并且授予test用户所有操作数据库的权限。

SQL代码如下：
```sql
create database if not exists test;

create table dept(
id int primary key auto_increment,
dept_name varchar(10),
dept_desc varchar(10),
dept_date date
);

insert into dept(dept_name,dept_desc,dept_date) values('研发部','开发软件','2014-12-14'),('财务部','发工资','2014-10-14'),('市场部','销售软件','2015-11-02');


create table employee(
id int primary key auto_increment,
name varchar(10) not null,
gender char(1) not null,
age int not null,
money int,
deptid int
);
insert into employee(name,gender,age,money,deptid) values('王五','男',25,3000,1),('李明','男',23,2500,1),('王二小','男',23,2356,2),('陈发','男',22,3600,2),('小明','男',21,3100,3),('苏奇','男',24,2800,3),('王丽','女',19,1800,1),('李芳','女',18,1900,2);
alter table employee add constraint emp_dept foreign key(deptid) references dept(id);

create user 'test'@'%' identified by 'test';
grant all privileges on test.* to test@'%' identified by 'test';
flush privileges;

```

# 二、Java程序连接
## 1.引入库
连接MySQL数据库必须要使用MySQL数据库公司提供的jar包。点击这个连接可以进行下载：[下载MySQL的jar包](https://repo1.maven.org/maven2/mysql/mysql-connector-java/5.1.30/mysql-connector-java-5.1.30.jar)

1：下载好jar包之后，在项目名称下创建lib文件夹，将下载好的jar包放入lib文件夹。项目树如下所示：
![项目树](https://img-blog.csdnimg.cn/c5c0853ec38043d19308871490317f9c.png)

2：选中mysql-connector-java-5.1.30-bin.jar包，右击鼠标效果图如下：
![点击Add as Library](https://img-blog.csdnimg.cn/60f2e23c62c1417aa170dbcefd45fc3d.png)
3：点击Add as Library，完成jar包的导入.
## 2.编写Java代码

代码如下：
```java

public class Demo1 {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {

        //通过反射获取Driver
        Class.forName("com.mysql.jdbc.Driver");

        //通过DriverManager获取Connection对象
        //getConnection（）这个方法的三个参数分别是：URL连接，数据库用户名，数据库用户名密码。
        /**
         *  create user 'test'@'%' identified by 'test';
         *         grant all privileges on test.* to test@'%' identified by 'test';
         *         flush privileges;
         *  可以在执行上面的SQL语句为数据库创建对应的数据库用户
         *  URL连接这块，请尽量改为自己的IP地址（安装MySQL数据库的主机IP地址）
         * 
         * */
        
        Connection connection = DriverManager.getConnection("jdbc:mysql://81.70.153.180:3306/test?serverTimezone=Asia/Shanghai", "test", "test");

        //使用预编译Statement，执行SQL语句更加安全，其中的？是一个占位符
        PreparedStatement preparedStatement = connection.prepareStatement("select * from employee where id =?");

        //通过下面这个方法将占位符的地方设置具体值。这个方法的第一个参数parameterIndex是从1开始
        preparedStatement.setObject(1, 7);

        //通过执行executeQuery（）方法获取ResultSet结果集对象
        ResultSet resultSet = preparedStatement.executeQuery();

        //使用ResultSet对象的next（）方法获取数据
        while (resultSet.next()) {
            int id = resultSet.getInt("id");
            String name = resultSet.getString("name");
            String gender = resultSet.getString("gender");
            int age = resultSet.getInt("age");
            int money = resultSet.getInt("money");
            System.out.println("id：" + id + "；姓名：" + name + "；性别：" + gender + "；年龄：" + age + "；薪资：" + money);
        }
        
        //使用完记得关闭连接
        resultSet.close();
        preparedStatement.close();
        connection.close();
    }
}

```
运行结果如下：
![运行结果](https://img-blog.csdnimg.cn/1439e8aafcb64e18a6bde7725b709638.png)

---

# 总结
当我们使用JDBC连接数据库的时候尽量使用 PreparedStatement去操作数据库，因为这个对象更加的安全，可以防止SQL注入； PreparedStatement使用setObject（）方法去为占位符设置具体的值，使代码更加的清楚直观，可读性性更高。