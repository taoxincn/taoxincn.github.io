---
title:  开启CORS解决Geoserver跨域问题
date:   2021-12-24 22:17:00 +0800
categories: [GIS]
tags: [GIS, Geoserver]
---

新部署的Geoserver因为跨域问题导致前端无法调用WFS接口，以下为解决方案：
### 1. 找到`webapps/geoserver/WEB-INF/web.xml`文件，进入vi编辑模式
### 2. 取消过滤器`<filter>`注释
#### 2.1 如果使用Jetty服务器，对以下代码取消注释
```xml
<filter>
  <filter-name>cross-origin</filter-name>
  <filter-class>org.eclipse.jetty.servlets.CrossOriginFilter</filter-class>
  <init-param>
    <param-name>chainPreflight</param-name>
    <param-value>false</param-value>
  </init-param>
  <init-param>
    <param-name>allowedOrigins</param-name>
    <param-value>*</param-value>
  </init-param>
  <init-param>
    <param-name>allowedMethods</param-name>
    <param-value>GET,POST,PUT,DELETE,HEAD,OPTIONS</param-value>
  </init-param>
  <init-param>
    <param-name>allowedHeaders</param-name>
    <param-value>*</param-value>
  </init-param>
</filter>
```
#### 2.2 如果使用Tomcat服务器，对以下代码取消注释
```mxl
<filter>
  <filter-name>cross-origin</filter-name>
  <filter-class>org.apache.catalina.filters.CorsFilter</filter-class>
  <init-param>
    <param-name>cors.allowed.origins</param-name>
    <param-value>*</param-value>
  </init-param>
  <init-param>
    <param-name>cors.allowed.methods</param-name>
    <param-value>GET,POST,PUT,DELETE,HEAD,OPTIONS</param-value>
  </init-param>
  <init-param>
    <param-name>cors.allowed.headers</param-name>
    <param-value>*</param-value>
  </init-param>
</filter>
```
### 3. 取消过滤器映射`<filter-mapping>`注释
```xml
<filter-mapping>
  <filter-name>cross-origin</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
```
### 4. 重启Geoserver服务后即可正常跨域访问