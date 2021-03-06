# V-Board: 一站式管理协作平台

![](https://img.shields.io/badge/language-Python-green.svg)
![](https://img.shields.io/badge/Powered%20By-Django%20Bootstrap%20JQuery-blue)
![](https://img.shields.io/badge/Database-MySQL-green.svg)

## 项目概述

​	V-Board看板是由装满卡片、由你和你的团队使用的各种列表的列表。他核心要素也就只有看板、列表、卡片这三者。从这三个最基础的组件派生出许多有趣且实用的功能。符合大多数用户不同的需求。从个人的日程管理，记录生活琐事，工作计划到团队的项目开发，团队合作都可以提供一种简单有效的管理和记录方式。可以私用或者公用，不论是用作团队协作的进度展示，还是日程管理，V-board都可以完成。

[在线预览（预览最新版本）]( http://101.37.173.44:8000/ )

## 项目结构

> 暂定

## 开发日志

### 2019-11-8

> 搭建基础框架，配置基础环境，配置Django setting.py (模板、主语言、时区、静态文件等)

#### 配置 Django 模板寻找目录

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')], # 添加此行增加模板目录
        'APP_DIRS': True,# 使其可以在APP目录下寻找
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

#### 配置 Django 静态文件寻找目录

```python
# APP下的静态文件位置
STATIC_URL = '/static/'
# 全局静态文件位置
STATICFILES_DIRS = [os.path.join(BASE_DIR, 'static')]
```

#### 配置 MySQL

> 此处开发需在 MySQL中新建用户 stranger 配置其密码为123456（仅开发使用）
>
> 创建新数据库 v-board 供项目使用

在Django setting.py 中 配置 MySQL为默认数据库

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': "v-board",
        'USER': "stranger",
        "PASSWORD":'*..*',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}
```

### 2019-11-11

> 配置项目首页，搭建服务器预览设置

####  项目首页配置

配置相关URL设置，以及对应的视图`path('', index)` ：将域名下首页设置为index函数返回的视图

项目首页使用开放模板搭建，遵从相关许可

模板地址:https://github.com/BlackrockDigital/startbootstrap-stylish-portfolio 

#### 服务器搭建

服务器为阿里CentOs 7, python 环境 3.7，数据库为默认 *MariaDB*  (MySQL的子集)

### 2019-11-16

> 实现登陆注册模块

#### 前端页面设置

项目登陆注册页面使用网上的模板

模板地址： http://www.dmaku.com/demo-moban-2071.html 

在此模板基础上添加相应的响应事件以及更改样式

#### 数据库表配置

> 项目未使用 Django 自带的模型，实现原生SQL实现数据库操作

- usr_info表：保存用户的个人信息等

	```sql
	CREATE TABLE `usr_login` (
		`email` VARCHAR(50) NOT NULL,
		`password` VARCHAR(100) NOT NULL,
		`UID` INT(11) UNSIGNED NOT NULL,
		PRIMARY KEY (`email`),
		INDEX `FK__memberinfo` (`UID`),
		CONSTRAINT `FK__memberinfo` FOREIGN KEY (`UID`) REFERENCES `usr_info` (`UID`)
	)
	COMMENT='用户登录信息'
	COLLATE='utf8mb4_0900_ai_ci'
	ENGINE=InnoDB
	;
	```

- usr_login表：用户登录信息

	```sql
	CREATE TABLE `usr_info` (
		`UID` INT(11) UNSIGNED NOT NULL AUTO_INCREMENT COMMENT '唯一编号',
		`name` VARCHAR(50) NOT NULL COMMENT '用户名',
		`avatar` VARCHAR(100) NOT NULL DEFAULT '' COMMENT '头像路径',
		`description` VARCHAR(500) NOT NULL DEFAULT '' COMMENT '自我描述',
		PRIMARY KEY (`UID`)
	)
	COMMENT='保存用户的个人信息等'
	COLLATE='utf8mb4_0900_ai_ci'
	ENGINE=InnoDB
	AUTO_INCREMENT=1
	;
	```

#### 保持登录状态实现

需要实现用户登录后保持登录状态，HTTP是无状态协议，所以需要使用Django的Session（会话）功能实现

参考： http://www.liujiangblog.com/course/django/111 

#### 后端逻辑代码

新建APP：login实现登录注册功能