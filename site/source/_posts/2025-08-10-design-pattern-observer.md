---
layout: post
title: 设计模式学习 - 观察者模式
date: 2025-08-10 11:00:00
categories: [设计模式]
tags: [design pattern, observer, subject]
---

## 观察者模式

观察者模式（Observer Pattern）是一种行为设计模式，它定义了一种对象之间的一对多依赖关系。当一个对象（称为“主题”或“可观察对象”）的状态发生改变时，所有依赖于它的对象（称为“观察者”）都会得到通知并自动更新。这种模式在需要保持对象间松耦合且需要动态订阅和发布事件的场景中非常有用。

### 主题（Subject）：
- 维护一个观察者列表。
- 提供方法来添加（attach）、移除（detach）观察者。
- 当状态改变时，通知所有观察者。
### 观察者（Observer）：
- 定义一个更新接口，用于接收主题的通知。
- 当被通知时，观察者可以执行相应的操作以响应主题状态的变化。

### 工作原理
- 订阅（Attachment）：观察者通过调用主题的添加方法，将自己注册到主题的观察者列表中。
- 通知（Notification）：当主题的状态发生改变时，它会遍历观察者列表，并调用每个观察者的更新方法。
- 更新（Update）：观察者在收到通知后，根据主题的新状态执行相应的操作。

### 优点
- 松耦合：主题和观察者之间是松耦合的，它们只需要知道彼此的接口，而不需要知道具体的实现细节。
- 动态关系：可以在运行时动态地添加或移除观察者，这使得系统更加灵活和可扩展。
- 广播通信：主题可以方便地通知所有观察者，而不需要显式地指定接收者。

### 缺点
- 内存泄漏：如果不小心，可能会导致观察者对象无法被垃圾回收，从而造成内存泄漏。例如，如果观察者被添加到主题的观察者列表中，但之后没有被正确移除。
- 通知顺序：观察者接收通知的顺序可能是不确定的，这取决于它们在观察者列表中的顺序。如果顺序很重要，那么可能需要额外的逻辑来处理。

### 示例
``` typescript
namespace observer {
  class Message {
    constructor(public data: string) {}
  }

  interface Subject {
    attachObserver(observer: Observer): void;
    detachObserver(observer: Observer): void;
    notifyObservers(message: Message): void;
  }

  interface Observer {
    update(message: Message): void;
  }

  class ConcreteSubject implements Subject {
    private observers: Observer[] = [];
    attachObserver(observer: Observer) {
      this.observers.push(observer);
    }
    detachObserver(observer: Observer) {
      this.observers = this.observers.filter(item => item !== observer);
    }
    notifyObservers(message: Message) {
      this.observers.forEach(observer => {
        observer.update(message);
      })
    }
  }

  class ConcreteObserverA implements Observer {
    update(message: Message) {
      console.log('A 接收消息：', message.data)
    }
  }

  // 测试
  const subject = new ConcreteSubject();
  subject.attachObserver(new ConcreteObserverA());
  subject.notifyObservers(new Message('消息内容'))
}
```

## 推荐

- [软件设计模式](https://baike.baidu.com/item/%E8%BD%AF%E4%BB%B6%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/2117635)
- [23种设计模式](https://zhuanlan.zhihu.com/p/651451595)