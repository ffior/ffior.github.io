---
layout: post
title: 设计模式学习 - 职责链模式
date: 2025-08-10 15:00:00
categories: [设计模式]
tags: [design pattern, chainOfResponsibility]
---

## 职责链模式

职责链模式（Chain of Responsibility Pattern）是一种行为设计模式，它通过将多个处理对象连接成一条链，并沿着这条链传递请求，直到有一个对象能够处理它为止。这种模式实现了请求的发送者和接收者之间的解耦，使得多个对象都有机会处理请求，从而增强了系统的灵活性和可扩展性。

### 抽象处理者（Handler）：
- 定义一个处理请求的接口，通常包含一个设置后继处理者的方法（如setSuccessor）和一个处理请求的方法（如handleRequest）。
- 处理请求的方法中，通常会判断自身是否能处理该请求，如果不能，则将请求转发给后继处理者。
### 具体处理者（Concrete Handler）：
- 实现抽象处理者的接口，具体处理请求或将其转发给后继处理者。
- 每个具体处理者都负责处理特定的请求类型，或者将请求传递给链中的下一个处理者。
### 客户端（Client）：
- 发起请求，并将请求发送给职责链中的第一个处理者。

### 工作原理
- 构建职责链：客户端或某个配置对象负责构建职责链，将各个处理者按照一定的顺序连接起来。
- 发送请求：客户端将请求发送给职责链中的第一个处理者。
- 处理请求：第一个处理者接收请求后，判断自身是否能处理该请求。如果能处理，则处理请求；如果不能处理，则将请求转发给后继处理者。这个过程沿着职责链一直传递下去，直到有一个处理者能够处理该请求或者请求到达链的末尾。
- 返回结果：处理请求的处理者可以将处理结果返回给客户端，或者由职责链中的某个特定处理者负责收集并返回结果。

### 优点
- 解耦：职责链模式将请求的发送者和接收者解耦，使得发送者不需要知道接收者的具体信息，只需要将请求发送给职责链中的第一个处理者即可。
- 灵活性：可以动态地添加、删除或修改职责链中的处理者，而不需要修改客户端代码或其他处理者的代码。
- 可扩展性：可以方便地扩展新的处理者类型，只需实现抽象处理者的接口并将其添加到职责链中即可。
- 单一职责：每个处理者只负责处理自己能处理的请求，不符合的请求则转发给后继处理者，这符合单一职责原则。

### 缺点
- 性能问题：如果职责链过长，或者请求在链中传递多次才找到处理者，可能会影响性能。
- 请求不一定能被处理：如果职责链中的所有处理者都无法处理请求，那么请求可能会得不到处理，需要额外的机制来处理这种情况。

### 示例
``` typescript
namespace chainOfResponsibilityByArray {
    interface IHandler {
        handle(content: number): Boolean;
    }

    class HandlerA implements IHandler {
        handle(content: number): Boolean {
            let handled: Boolean = false;
            if (content < 9) {
                console.log("小于9，处理器 A 处理对象");
                // 数据处理代码
                handled = true;
            }
            return handled;
        }
    }

    class HandlerB implements IHandler {
        handle(content: number): Boolean {
            let handled: Boolean = false;
            if (content < 100 && content >= 9) {
                console.log("大于等于9，小于100，处理器 B 处理对象");
                // 数据处理代码
                handled = true;
            }
            return handled;
        }
    }

    // 处理器链
    class HandlerChain {
        private handlers: IHandler[] = [];
        addHandler(handler: IHandler) {
            this.handlers.push(handler);
        }
        handle(content: number): void {
            for (let i = 0; i < this.handlers.length; i++) {
                const handled = this.handlers[i].handle(content);
                if (handled) return;
            }
        }
    }
    
    // 使用举例
    class Application {
        constructor() {
            const handlerChain: HandlerChain = new HandlerChain();
            handlerChain.addHandler(new HandlerA());
            handlerChain.addHandler(new HandlerB());
            handlerChain.handle(18);
        }
    }

    new Application();
}

```

## 推荐

- [软件设计模式](https://baike.baidu.com/item/%E8%BD%AF%E4%BB%B6%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/2117635)
- [23种设计模式](https://zhuanlan.zhihu.com/p/651451595)