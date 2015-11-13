<a name="category"/>
## 目录

[命名空间声明](#namespace)<br/>
[<context:annotation-config/>](#annotation_config)

## 详解

<a name="namespace"/>
### 命令空间声明（[返回目录](#category)）

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xmlns="http://www.springframework.org/schema/beans"
        xmlns:mvc="http://www.springframework.org/schema/mvc"
        xmlns:context="http://www.springframework.org/schema/context"
        xsi:schemaLocation="
			http://www.springframework.org/schema/beans
	        http://www.springframework.org/schema/beans/spring-beans.xsd
	        http://www.springframework.org/schema/mvc
	        http://www.springframework.org/schema/mvc/spring-mvc.xsd
	        http://www.springframework.org/schema/context
	        http://www.springframework.org/schema/context/spring-context.xsd
		">

<a name="annotation_config"/>
### <context:annotation-config/>（[返回目录](#category)）

当我们需要使用 Spring 提供的注解时，我们必须要在 Spring 容器中定义相应的 BeanPostProcessor，比如：

当我们需要使用 `@Autowired` 注解时，必须事先在Spring容器中声明 `AutowiredAnnotationBeanPostProcessor` 的Bean：

	<bean class="org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor "/>

使用 `@Required` 注解，就必须声明 `RequiredAnnotationBeanPostProcessor` 的Bean：

	<bean class="org.springframework.beans.factory.annotation.RequiredAnnotationBeanPostProcessor"/>

类似地，使用 `@Resource` 、`@PostConstruct` 、`@PreDestroy` 等注解就必须声明 `CommonAnnotationBeanPostProcessor`；<br/>
使用 `@PersistenceContext` 注解，就必须声明 `PersistenceAnnotationBeanPostProcessor` 。

这样的声明未免太不优雅，而Spring为我们提供了一种极为方便注册这些BeanPostProcessor的方式，即使用 `<context:annotation- config/>` 隐式地向 Spring容器注册`AutowiredAnnotationBeanPostProcessor` 、`RequiredAnnotationBeanPostProcessor` 、`CommonAnnotationBeanPostProcessor` 以及 `PersistenceAnnotationBeanPostProcessor` 这4个BeanPostProcessor。如下：

	<context:annotation-config/>

