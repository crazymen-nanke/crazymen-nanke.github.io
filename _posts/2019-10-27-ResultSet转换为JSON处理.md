---
layout: post
title:  "ResultSet简单处理"
date:   2019-10-27 12:17:00
categories: Android
tags:  ResultSet
author: NanKe
---

* content
{:toc}
本文主要记录了安卓程序连接数据库后对ResultSet的简单操作。



### 将数据库表里面的数据以JSONArray的形式进行输出

```java
package com.lianghengjia.testdata;

//对MySQL数据库进行了简单的练习
//并且将ResultSet转换成了JSON数据
//实现功能：将数据库表里面的数据以JSONArray的形式进行输出
import android.os.Bundle;
import androidx.appcompat.app.AppCompatActivity;
import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;

import java.sql.*;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        new Thread(new Runnable() {
            @Override
            public void run() {

                System.out.println("JSONArray:" + connectMysql().toString());
            }
        }).start();
    }

    private JSONArray connectMysql() {
        final String DRIVER = "com.mysql.jdbc.Driver";
        final String URL = "jdbc:mysql://101.201.69.188:3306/log_information";
        final String USER = "root";
        final String PASSWORD = "root";
        JSONArray array = new JSONArray();
        try {
            Class.forName(DRIVER);
            Connection connection = DriverManager.getConnection(URL, USER, PASSWORD);
            Statement statement = connection.createStatement();
            ResultSet resultSet = statement.executeQuery("SELECT * FROM user_infmation");

            while (resultSet.next()) {
                ResultSetMetaData metaData = resultSet.getMetaData();
                JSONObject object = new JSONObject();
                int columnCount = metaData.getColumnCount();
                for (int i = 1; i <= columnCount; i++) {
                    String name = metaData.getColumnLabel(i);
                    String value = resultSet.getString(name);
                    object.put(name, value);
                }
                array.put(object);
            }
            connection.close();
            statement.close();
            resultSet.close();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } catch (JSONException e) {
            e.printStackTrace();
        }
        return array;
    }

}


```



### resultSet转化为JSONObject

```java
/**
     * 将resultSet转化为JSONObject
     * @param rs
     * @return
     * @throws SQLException
     * @throws JSONException
     */
 
    public static JSONObject resultSetToJsonObject(ResultSet rs) throws SQLException,JSONException 
 
    { 
 
       // json对象
 
        JSONObject jsonObj = new JSONObject();     
 
       // 获取列数 
 
       ResultSetMetaData metaData = rs.getMetaData(); 
 
       int columnCount = metaData.getColumnCount(); 
 
       // 遍历ResultSet中的每条数据 
 
        if (rs.next()) { 
 
            // 遍历每一列 
 
            for (int i = 1; i <= columnCount; i++) { 
 
                String columnName =metaData.getColumnLabel(i); 
 
                String value = rs.getString(columnName); 
 
                jsonObj.put(columnName, value); 
 
            }   
 
        }
 
       return jsonObj; 
 
    }
 
}
```





### resultSet转化为JSON数组

```java
  /**
     * 将resultSet转化为JSON数组
     * @param rs
     * @return
     * @throws SQLException
     * @throws JSONException
     */
 
    public static JSONArray resultSetToJsonArry(ResultSet rs) throws SQLException,JSONException 
 
    { 
 
       // json数组 
 
       JSONArray array = new JSONArray(); 
 
         
 
       // 获取列数 
 
       ResultSetMetaData metaData = rs.getMetaData(); 
 
       int columnCount = metaData.getColumnCount(); 
 
         
 
       // 遍历ResultSet中的每条数据 
 
        while (rs.next()) { 
 
            JSONObject jsonObj = new JSONObject(); 
 
              
 
            // 遍历每一列 
 
            for (int i = 1; i <= columnCount; i++) { 
 
                String columnName =metaData.getColumnLabel(i); 
 
                String value = rs.getString(columnName); 
 
                jsonObj.put(columnName, value); 
 
            }  
 
            array.put(jsonObj);  
 
        } 
 
         
 
       return array; 
 
    }
```

