---
layout: post
title:  "viewBinding的学习"
date:   2020-09-14 13:30:50
categories: 新知识
tags: 安卓
author: NanKe
---

* content
{:toc}
本文主要记录了viewBinding的使用。



# viewBinding简介：

官方文档地址：https://developer.android.google.cn/topic/libraries/view-binding

通过视图绑定功能，您可以更轻松地编写可与视图交互的代码。在模块中启用视图绑定之后，系统会为该模块中的每个 XML 布局文件生成一个绑定类。绑定类的实例包含对在相应布局中具有 ID 的所有视图的直接引用。

在大多数情况下，视图绑定会替代 `findViewById`。



使用方法：

在模块底下的build.gradle文件里面添加代码：

```java
android{
    ...
    viewBinding{
        enabled = true
    }    
    ...
}
```



在活动中使用：

假设现在我们的活动名称是MainActivity，其对应的布局文件名称是activity_main。当我们使用viewBinding的时候，系统会为布局自动生成以驼峰原则命名尾部为Binding的类，在这个类里面使用getRoot()方法会返回根视图的引用。使用inflate()方法添加视图。

代码如下：

````java
...
private ActivityMainBinding binding;

protected void onCreate(Bundle savedInstanceState) {
       //在活动中使用viewBinding
        binding = ActivityMainBinding.inflate(getLayoutInflater());
        setContentView(binding.getRoot());
        binding.name.setText("test viewBinding");
    }
...
````

在碎片中使用：

```java
...
private FragmentBlankBinding binding; 
 @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        binding = FragmentBlankBinding.inflate(inflater, container, false);
        return binding.getRoot();
    }
 @Override
    public void onActivityCreated(@Nullable Bundle savedInstanceState) {
        super.onActivityCreated(savedInstanceState);
        binding.text.setText("碎片中使用viewBinding");
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        binding = null;
    }
...
```

