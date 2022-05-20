

# 一、Nacos

## 1、基本概念

**（1）**Nacos 是阿里巴巴推出来的一个新开源项目，是一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台。Nacos 致力于帮助您发现、配置和管理微服务。Nacos 提供了一组简单易用的特性集，帮助您快速实现动态服务发现、服务配置、服务元数据及流量管理。Nacos 帮助您更敏捷和容易地构建、交付和管理微服务平台。 Nacos 是构建以“服务”为中心的现代应用架构 (例如微服务范式、云原生范式) 的服务基础设施。

**（2）**Nacos是以服务为主要服务对象的中间件，Nacos支持所有主流的服务发现、配置和管理。

Nacos主要提供以下四大功能：

1. 服务发现和服务健康监测

2. 动态配置服务

3. 动态DNS服务

4. 服务及其元数据管理

**（4）**Nacos结构图

![img](../图片/6e5b55f7-3252-4dea-81e9-e0ffd86987b4.jpg)

## 2、Nacos下载和安装

**（1）下载地址和版本**

下载地址：https://github.com/alibaba/nacos/releases

下载版本：nacos-server-1.1.4.tar.gz或nacos-server-1.1.4.zip，解压任意目录即可

**（2）启动nacos服务**

\- Linux/Unix/Mac

启动命令(standalone代表着单机模式运行，非集群模式)

启动命令：sh startup.sh -m standalone

 

\- Windows

启动命令：cmd startup.cmd 或者双击startup.cmd运行文件。

访问：http://localhost:8848/nacos

用户名密码：nacos/nacos

# 二、服务注册

## 1、在service模块配置pom

配置Nacos客户端的pom依赖

```xml
<!--服务注册-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

## 2、添加服务配置信息

配置application.properties，在客户端微服务中添加注册Nacos服务的配置信息

```properties
# nacos服务地址
spring.cloud.nacos.discovery.server-addr=127.0.0.1:8848
```

## **3、添加Nacos客户端注解**

在客户端微服务启动类中添加注解 

```java
@EnableDiscoveryClient
```

## **4、创建包和接口**

创建client包

@FeignClient注解用于指定从哪个服务中调用功能 ，名称与被调用的服务名保持一致。

@GetMapping注解用于对被调用的微服务进行地址映射。

@PathVariable注解一定要指定参数名称，否则出错

@Component注解防止，在其他位置注入CodClient时idea报错

```java
package com.guli.edu.client;

@FeignClient("service-vod")//指定要调用的服务器
@Component
public interface VodClient {
	@DeleteMapping(value = "/eduvod/vod/video/{videoId}")//value中是全路径
	public R removeVideo(@PathVariable("videoId") String videoId);//方法要与需要被调用的一致
}
```

## **5、调用微服务**

在调用端的需要的地方调用client中的方法

```java
@Autowired
private VodClient vodClient;
```

