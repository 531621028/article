[TOC]

# Spring boot 启动流程

## 1.整体概览		

​		首先我们查看Spring boot项目的启动的入口，一般是一个main函数为SpringApplication.run(xxxApplication.class, args)这一行代码，通过对这一行代码的跟踪调试我们会发现，他会初始换一个SpringApplication的对象，并将当前的类做一个基础的配置类参数传递进去，<u>在后面的代码中我们可以看到代码的启动会根据启动类上的注解进行一系列的配置，此处暂不展开</u>。代码如下：

```java
public static ConfigurableApplicationContext run(Class<?>[] primarySources,
			String[] args) {
		return new SpringApplication(primarySources).run(args);
	}
```

​		通过对代码继续跟踪，我们可以发现这样一个run方法，从方法中的代码可以看出一个spring boot项目启动的主要流程，代码与关键步骤的解释如下：

```java
public ConfigurableApplicationContext run(String... args) {
		StopWatch stopWatch = new StopWatch();
		stopWatch.start();
		ConfigurableApplicationContext context = null;
		Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList<>();
		// 不知道做什么的暂时不用管
  	configureHeadlessProperty();
  
  	// 从系统中获取配置中注册的所有SpringApplicationRunListener
		SpringApplicationRunListeners listeners = getRunListeners(args);
  	// 启动所有的监听器
		listeners.starting();
		try {
      // 将传入的参数转换为DefaultApplicationArguments对象
			ApplicationArguments applicationArguments = new DefaultApplicationArguments(
					args);
      // 准备spring启动上下文的环境配置信息
			ConfigurableEnvironment environment = prepareEnvironment(listeners,
					applicationArguments);
      // 不知道做什么的暂时不用管
			configureIgnoreBeanInfo(environment);
      // 打印启动的图标
			Banner printedBanner = printBanner(environment);
      // 创建ApplicationContext上下文
			context = createApplicationContext();
      // 从系统中获取配置中注册的所有SpringBootExceptionReporter
			exceptionReporters = getSpringFactoriesInstances(
					SpringBootExceptionReporter.class,
					new Class[] { ConfigurableApplicationContext.class }, context);
      // 通过前面的配置参数准备上下文
			prepareContext(context, environment, listeners, applicationArguments,
					printedBanner);
      // 刷新上下文
			refreshContext(context);
      // 刷新上下文之后的一些操作
			afterRefresh(context, applicationArguments);
			stopWatch.stop();
			if (this.logStartupInfo) {
				new StartupInfoLogger(this.mainApplicationClass)
						.logStarted(getApplicationLog(), stopWatch);
			}
			listeners.started(context);
			callRunners(context, applicationArguments);
		}
		catch (Throwable ex) {
			handleRunFailure(context, ex, exceptionReporters, listeners);
			throw new IllegalStateException(ex);
		}

		try {
			listeners.running(context);
		}
		catch (Throwable ex) {
			handleRunFailure(context, ex, exceptionReporters, null);
			throw new IllegalStateException(ex);
		}
		return context;
	}
```

​		通过上面的代码我们知道了一个大概的流程，后面针对里面的每一个流程进行说明

