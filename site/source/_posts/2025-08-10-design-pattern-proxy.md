---
layout: post
title: 设计模式学习 - 代理模式
date: 2025-08-10 14:30:00
categories: [设计模式]
tags: [design pattern, proxy, subject, real subject]
---

## 代理模式

代理模式（Proxy Pattern）是一种结构型设计模式，它通过引入一个代理对象来间接访问目标对象（真实对象），从而在访问目标对象前后添加额外的逻辑或控制对目标对象的访问。这种模式在需要控制对某个对象的访问、延迟加载、缓存、日志记录或权限验证等场景中非常有用。

### 抽象主题（Subject）：
- 定义了代理对象和真实对象之间的共同接口，使得客户端可以像使用真实对象一样使用代理对象。
### 真实主题（Real Subject）：
- 实现抽象主题接口，是实际处理业务逻辑的对象。
### 代理主题（Proxy）：
- 同样实现抽象主题接口。
- 持有真实主题的引用，并在访问真实主题前后添加额外的逻辑（如权限检查、日志记录、延迟加载等）。

### 工作原理
- 创建代理：客户端通过代理对象来间接访问真实对象，而不是直接实例化真实对象。
- 代理转发请求：当客户端调用代理对象的方法时，代理对象可以在转发请求给真实对象之前或之后执行一些额外的操作。
- 真实对象处理请求：代理对象将请求转发给真实对象，真实对象处理请求并返回结果。
- 代理返回结果：代理对象可以在将结果返回给客户端之前对结果进行进一步处理或修改。

### 优点
- 控制访问：代理模式可以控制对真实对象的访问，例如在访问前进行权限验证或日志记录。
- 延迟加载：代理模式可以实现延迟加载，即只有在真正需要时才创建真实对象，从而节省资源。
- 简化客户端调用：客户端只需要与代理对象交互，而不需要了解真实对象的复杂初始化或访问逻辑。
- 增强功能：代理模式可以在不修改真实对象代码的情况下，为其添加额外的功能或行为。

### 缺点
- 增加系统复杂度：引入代理对象会增加系统的复杂度，因为需要管理代理对象和真实对象之间的关系。
- 性能开销：代理模式可能会在访问真实对象前后添加额外的逻辑，这可能会带来一定的性能开销。

### 示例
``` typescript
namespace proxy {
  
  interface Subject {
    methodA(): void;
    methodB(): void;
  }

  class RealSubject implements Subject {
    methodA(): void {
      console.log('被代理对象：执行方法A');
    }
    methodB(): void {
      console.log('被代理对象：执行方法B');
    }
  }

  class Proxy implements Subject {
    private subject: RealSubject;
    constructor(subject: RealSubject) {
      this.subject = subject; // 依赖注入
    }
    methodA(): void {
      console.log('代理：执行方法A对应的代理操作');
      // 委托
      this.subject.methodA();
    }
    methodB(): void {
      console.log('代理：执行方法A对应的代理操作');
      // 委托
      this.subject.methodB();
    }
  }

  // 测试
  const proxy = new Proxy(new RealSubject());
  proxy.methodA();
  proxy.methodB();
}
```

## 推荐

- [软件设计模式](https://baike.baidu.com/item/%E8%BD%AF%E4%BB%B6%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/2117635)
- [23种设计模式](https://zhuanlan.zhihu.com/p/651451595)