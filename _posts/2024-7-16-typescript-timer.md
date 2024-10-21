---
title: TypeScript Timer 工具类
author: zeo
date: 2024-7-16 19:46:00 +0800
categories: [提效工具, 定时器Timer类]
tags: [提效, Typescript, 定时器, Timer]
render_with_liquid: false
seo:
  title: TypeScript Timer 工具类教程
  description: 了解如何使用TypeScript实现一个计时器工具类
---

# 如何实现一个强大的 TypeScript 计时器工具类

在现代Web开发中，时间管理是一个常见的需求。无论是需要计时的游戏开发，还是需要记录时间的应用程序，一个强大的计时器工具类都是必不可少的。在本文中，我们将介绍如何实现一个功能丰富的 TypeScript 计时器工具类，并详细解析其代码和使用方法。

## 计时器工具类简介

我们的 `Timer` 类具备以下功能：
- 启动计时器
- 停止计时器
- 暂停和恢复计时器
- 重置计时器
- 记录计次（lap）时间
- 获取计时器的持续时间和已过去的时间

### Timer 类源码

首先，让我们来看一下 `Timer` 类的完整源码：

```typescript
/**
 * 时间计时器
 */
export class Timer {
  private startTime: number | null = null; // 开始时间
  private endTime: number | null = null;  // 结束时间
  private pausedTime: number = 0; // 暂停时长
  private pauseStartTime: number | null = null; // 暂停开始时间
  private lapTimes: number[] = []; // 存储计次时间点
  private lapStartTime: number | null = null; // 每次开始时间

  // 获取当前时间戳
  private getCurrentTime(): number {
    return Date.now();
  }

  // 启动计时器
  start(): void {
    const now = this.getCurrentTime();
    this.startTime = now;
    this.lapStartTime = now;
    this.endTime = null;
    this.pauseStartTime = null;
    this.pausedTime = 0
    this.lapTimes = []
  }

  // 停止计时器
  stop(): void {
    if (this.isPaused() && this.pauseStartTime !== null) {
      this.pausedTime += this.getCurrentTime() - this.pauseStartTime;
      this.pauseStartTime = null;
    }
    this.endTime = this.getCurrentTime();
  }

  // 暂停计时器
  pause(): void {
    if (!this.isPaused()) {
      this.pauseStartTime = this.getCurrentTime();
    }
  }

  // 恢复计时器
  resume(): void {
    if (this.isPaused() && this.pauseStartTime !== null) {
      this.pausedTime += this.getCurrentTime() - this.pauseStartTime;
      this.pauseStartTime = null;
    }
  }

  // 检查是否处于暂停状态
  isPaused(): boolean {
    return this.pauseStartTime !== null;
  }

  // 重置计时器
  reset(): void {
    this.startTime = null;
    this.endTime = null;
    this.pausedTime = 0;
    this.pauseStartTime = null;
    this.lapTimes = []
    this.lapStartTime = null
  }

  // 记录计次时间点
  lap(): void {
    if (this.lapStartTime !== null) {
      const now = this.getCurrentTime();
      const currentTime = now - this.lapStartTime - this.pausedTime;
      this.lapTimes.push(currentTime / 1000);
      this.lapStartTime = now;
      this.pausedTime = 0; // 每次记录完一圈时间后重置暂停时间
    }
  }

  // 获取所有的计次时间
  getLapTimes(): number[] {
    return this.lapTimes;
  }

  // 获取当前时间，从start->此刻，不会终止时间
  getDuration(): number | null {
    if (this.startTime === null) {
      return null; // 计时器尚未启动
    }

    if (this.endTime === null) {
      const now = this.getCurrentTime();
      return (now - this.startTime - this.pausedTime) / 1000; // 计时器仍在运行
    }

    return this.endTime - this.startTime - this.pausedTime; // 计时器已停止
  }

  // 获取已过去的时间（s），如果已经stop则返回总时长（不包含暂停时长），如果暂停中，返回暂停时长
  getElapsedDurationInSeconds(): number | null {
    const elapsedDurationInMillis = this.getElapsedDuration();

    return elapsedDurationInMillis !== null ? elapsedDurationInMillis / 1000 : null;
  }

  // 获取已过去的时间（ms）
  getElapsedDuration() {
    if (this.startTime === null) {
      return null; // 计时器尚未启动
    }

    const now = this.getCurrentTime();

    if (this.isPaused() && this.pauseStartTime !== null) {
      return now - this.pauseStartTime - this.pausedTime; // 计时器已暂停
    }

    return now - this.startTime - this.pausedTime; // 计时器正在运行或已停止
  }
}
```

### 方法详解

- **`start()`**: 启动计时器，初始化开始时间和计次时间，并重置结束时间和暂停时间。
  
- **`stop()`**: 停止计时器，记录结束时间。如果在停止前处于暂停状态，则将暂停时间累加到总暂停时长中。
  
- **`pause()`**: 暂停计时器，记录暂停开始的时间戳。
  
- **`resume()`**: 恢复计时器，将暂停的时长累加到总暂停时长中。
  
- **`isPaused()`**: 检查计时器是否处于暂停状态。
  
- **`reset()`**: 重置计时器，清空所有记录。
  
- **`lap()`**: 记录当前计次时间点，并存储到 `lapTimes` 数组中。
  
- **`getLapTimes()`**: 返回所有的计次时间点。
  
- **`getDuration()`**: 获取当前计时器运行的总时间，不包括暂停的时长。如果计时器尚未启动或已停止，返回相应的时间或 `null`。
  
- **`getElapsedDurationInSeconds()`**: 获取已过去的时间（秒），如果计时器尚未启动或已停止，返回相应的时间或 `null`。
  
- **`getElapsedDuration()`**: 获取已过去的时间（毫秒），包括暂停时长。如果计时器尚未启动或已停止，返回相应的时间或 `null`。

### 使用示例

下面是如何在项目中使用 `Timer` 类的示例：

```typescript
import { Timer } from './path/to/timer';

const timer = new Timer();

// 启动计时器
timer.start();
console.log('Timer started.');

// 暂停计时器
setTimeout(() => {
  timer.pause();
  console.log('Timer paused.');
}, 5000);

// 恢复计时器
setTimeout(() => {
  timer.resume();
  console.log('Timer resumed.');
}, 10000);

// 记录计次
setTimeout(() => {
  timer.lap();
  console.log('Lap recorded.');
}, 15000);

// 停止计时器
setTimeout(() => {
  timer.stop();
  console.log('Timer stopped.');
  console.log('Elapsed duration in seconds:', timer.getElapsedDurationInSeconds());
  console.log('Lap times:', timer.getLapTimes());
}, 20000);

// 重置计时器
setTimeout(() => {
  timer.reset();
  console.log('Timer reset.');
}, 25000);
```

在这个示例中，计时器将依次启动、暂停、恢复、记录计次、停止和重置，并在每个操作后输出相关信息。这个例子展示了如何在实际开发中使用 `Timer` 类，帮助你更好地管理时间和记录各个事件的持续时间。

### 总结

通过这个计时器工具类，你可以轻松实现各种时间管理和记录功能，无论是用于简单的时间记录，还是复杂的多任务时间管理。希望本文能够帮助你理解并实现一个强大的计时器工具类。如果你有任何问题或建议，欢迎在评论区留言讨论！