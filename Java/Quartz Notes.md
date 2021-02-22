# Quartz Notes

## Quartz 体系结构（三个核心概念）

1. 调度器

2. 任务（JobDetails包含任务实现类）

3. 触发器（trigger）（SimpleTrigger CronTrigger）触发时间

---

## Job&JobDetail

```java
interface job {
    execute(JobExecutionContent x);
}  
```

- **Job生命周期**
  
  每次调度器执行job时，调用execute方法前会创建一个新的job实例。当调用完成后，关联的job对象实例会被释放，释放的实例会被垃圾回收机制回收。

- **JobDetail**：
  
  绑定job，携带job需要的信息，关联监听器。

- **重要属性**
  
  name（任务名称）、jobClass（任务实现类）、group（任务所在组）、jobDataMap（传参作用）

- **JobBuilder**
  
  定义创建job的实例

- **JobStore**
  
  保存job数据

- **JobExecutionContext是什么？**
  
  - 当Scheduler调用job，就会将JobExecutionContext传递给job的execute方法
  
  - job能通过jobExecutionContext对象访问到Quartz运行时候的环境以及job本身的数据。

- **JobDataMap是什么（自定义参数）**
  
  - 进行任务调度时存储在jobExecutionContext中，方便获取。
  
  - 可以用来装载任何可序列化的数据对象，当job实例对象被执行时这些参数会传递给它。
  
  - 实现map接口，并添加了一些方便的方法用来存取基本数据类型。

- **如何获取：**
  
  - 从map中直接获取
  
  - 定义同名key的成员变量，通过setter方法获取值Trigger

#### 触发器

Quartz中的触发器用来告诉调度程序作业什么时候触发。  
即Trigger对象是用来触发执行job的。

- **Calendar**
  
  一个Trigger可以和多个Calendar关联，以排除或包含某些时间点。

- **监听器**
  
  JobListener，TriggerListener，SchedulerListener。

- **触发器通用属性**
  
  - JobKey：job实例的标识，触发器被触发时，该指定的job实例会执行。
  
  - StartTime：触发器首次被触发的时间。
  
  - EndTime：不再被触发的时间。

- **SinpleTrigger的作用**
  
  在一个指定时间段内执行一次作业任务，或是在指定的时间间隔内多次执行作业任务。

- **CronTrigger的作用**
  
  基于日历的作业调度器，而不是像simpleTrigger那样精确指定间隔时间，比SimpleTrigger更常用。

- **Cron表达式：**
  
  用于配置CronTrigger实例。由7个子表达式组成的字符串，描述了时间表的详情信息。
  格式：[秒] [分] [小时] [日] [月] [周] [年]
  
  ![20210117_1.png](..\Images\20210117_1.png)
  
  ![20210117_2.png](..\Images\20210117_2.png)
  
