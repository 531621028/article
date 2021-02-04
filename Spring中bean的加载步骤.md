# Spring中bean的加载步骤

## AbstractBeanFactory#doGetBean

1. 转换对应的beanName
   - 去除FactoryBean的修饰符
   - 将别名转换为最终的beanName
2. 尝试从缓存中加载单例
3. bean的实例化
4. 原型模式的以来检查
5. 检测parentBeanFactory
6. 将存储在xml配置文件中的GenericBeanDefinition转换为RootBeanDefinition
7. 寻找依赖
8. 针对不同的scope进行bean的创建
9. 类型转换

org.springframework.beans.factory.support.AbstractBeanFactory#doGetBean主要分两步，首先从缓存中获取，如果没有则开始创建单例