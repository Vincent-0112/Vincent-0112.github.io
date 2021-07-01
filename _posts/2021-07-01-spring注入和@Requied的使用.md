---
layout: post
title:  "spring注入和@Requied的使用"
date:   2021-07-01 15:08:29 +0900
categories: spring
---
# 都是怎么去检查所需的依赖
8% 在业务方法中
9% 使用初始化方法和断言
9% 在xml中使用依赖检查属性
13% 使用构造方法注入
15% 使用InitializeBean和断言
17% 使用Spring @Requried注解
29% 不去检查需要的依赖

上面有意思的是居然有29%的人不去检查需要的依赖注入，让我们康康是为啥？

# 构造方法注入
让我们回顾下构造方法注入，任何一个有带参数构造方法的类在不传参的情况下被构建。java中我们有一个默认/隐式的构造方法被加入到类中而不需要自己手工添加。这个默认/隐式的构造方法不带参数，因此只要你不添加一个带参数的构造方法或者指定没有任何参数的构造方法，将会导致初始化你的类不需要任何参数。换言之，当有参数的构造方法时，我们可以强行在注入的时候要求传入参数。
```
public class Service {

  public Collaborator collaborator;

  // constructor with arguments, you *have* to
  // satisfy the argument to instantiate this class
  public Service(Collaborator collaborator) {
    this.collaborator = collaborator;
  }
}
```
当我们需要去检查依赖的时候，可以使用这种方式。如果我们修改以上的代码去包含断言，我们可以100%的确保在没有collaborators注入时，类永远不会被初始化：
```
public Service(Collaborator collaborator) {
  if (collaborator == null) {
    throw new IllegalArgumentException("Collaborator cannot be null");
  }
  this.collaborator = collaborator;
}
```
换言之，如果使用构造方法注入结合断言，我们不需要依赖检查机制。

# 为什么大家几乎不使用构造器注入
为什么如此少的人去使用构造器注入确保依赖注入，如果是最简单的方式一定会有人做！这里有两个原因：
- 一点历史原因
- Spring框架本生的原因

# 历史原因
在2003年，当spring第一次以开源项目被发布，它主要关注Setter注入。其他框架也倡导依赖注入的方式，其中之一就是PicoContainer，它强烈关注构造器注入








Java变量的初始化顺序为：静态变量或静态语句块–>实例变量或初始化语句块–>构造方法–>@Autowired

Field injection is not recommended 
 Inspection info: Spring Team recommends: "Always use constructor based dependency injection in your beans. Always use assertions for mandatory dependencies".
总是在您的bean中使用构造函数建立依赖注入。总是使用断言强制依赖



https://www.jianshu.com/p/4694b1a82546
https://www.cnblogs.com/joemsu/p/7688307.html

> 官方博客 https://spring.io/blog/2007/07/11/setter-injection-versus-constructor-injection-and-the-use-of-required/


whether or not people were checking required dependencies and if so, what mechanisms they used.I quickly followed up on this question asking the community what transaction management strategy it used.
 The first a lot of people poll-on they were using the @Required annotation
 The second poll–on transaction management,quickly showed that a lot of people were using the @Transactional annotation. 
we really hoped people would not stick to Spring 1.x and in fact, people massively upgraded.

How are you checking required dependencies
8%	I check them in my business methods
9%	Using init-method and an assert mechanism (c.f. Assert)
9%	Using the dependency-check attribute in XML
13%	I don't have to, I use constructor injection
15%	Using InitializingBean and an assert mechanism
17%	Using the Spring 2.0 @Required annotation
29%	I do not check required dependencies



> https://blog.csdn.net/hegongxu/article/details/78114437