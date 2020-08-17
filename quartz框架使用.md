## quartz框架使用

### Quartz 三要素
Scheduler：任务调度器，所有的任务都是从这里开始。
Trigger：触发器，定义任务执行的方式、间隔。
JobDetail & Job ： 定义任务具体执行的逻辑。

### Scheduler：

scheduler 是quartz的核心所在，所有的任务都是通过scheduler开始。
scheduler是一个接口类，所有的具体实现类都是通过SchedulerFactory工厂类实现，但是SchedulerFactory有两个具体的实现类，如图：
![这里写图片描述](https://img-blog.csdn.net/20180718152333841?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JpY2hlbmc0NzY5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

1. StdSchedulerFactory：默认值加载是当前工作目录下的”quartz.properties”属性文件。如果加载失败，会去加载org/quartz包下的”quartz.properties”属性文件。一般使用这个实现类就能满足我们的要求。

2. DirectSchedulerFactory：这个我也没用过QAQ，听说是为那些想绝对控制 Scheduler 实例是如何生产出的人所
   设计的。

### Trigger

   - **withIdentity**() 给触发器一些属性 比如名字，组名。

   - **startNow**() 立刻启动

   - **withSchedule**(ScheduleBuilder schedBuilder) 以某种触发器触发。

   - **usingJobData**(String dataKey, Boolean value) 给具体job传递参数。

     举个创建Trigger的例子：

   ```java
   Trigger trigger = TriggerBuilder.newTrigger()
                   .withIdentity("trigger1", "group1")
                   .startNow()
                   .withSchedule(SimpleScheduleBuilder.simpleSchedule().withIntervalInSeconds(1)
                   .repeatForever()).build();12345
   ```

   Trigger的重点内容就是在withSchedule这个方法，从参数开始：查看`SchedulerBuilder`，这个是个`抽象类`，一共有4种具体实现方法，如图：
   ![这里写图片描述](https://img-blog.csdn.net/20180718162248553?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JpY2hlbmc0NzY5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

   - SimpleScheduleBuilder

     最简单的触发器，表示从某一时刻开始，以一定的时间间隔执行任务。

     属性：

     - repeatInterval 重复间隔。
     - repeatCount 重复次数。

   比如：现在开始，以后每一个小时执行一次。

   ```
   Trigger trigger = TriggerBuilder.newTrigger()
                   .withIdentity("trigger1", "group1")
                   .startNow()
                   .withSchedule(SimpleScheduleBuilder.simpleSchedule().withIntervalInHours(1)
                   .repeatForever()).build();12345
   ```

   - DailyTimeIntervalScheduleBuilder

     每一天的某一个时间段内，以一定的时间间隔执行任务，可以指定具体的某一天（星期一、星期二、星期三。。）

     属性：

     - intervalUnit 重复间隔（秒、分钟、小时。。。）。
     - daysOfWeek 具体的星期。 默认 周一到周日
     - startTimeOfDay 每天开始时间 默认 0.0
     - endTimeOfDay 每天结束时间，默认 23.59.59
     - repeatCount 重复次数。 默认是-1 不限次数
     - interval 每次执行间隔

   比如每周一到周四早上9点开始，晚上16点结束，每次执行间隔1 小时。

   需要 导入静态方法：**import static org.quartz.DailyTimeIntervalScheduleBuilder.dailyTimeIntervalSchedule;**

   ```
   Trigger trigger = TriggerBuilder.newTrigger().withIdentity("trigger1", "group1")
                   //加入 scheduler之后立刻执行
                   .startNow()
                   //定时 ，每个1秒钟执行一次
                   .withSchedule(dailyTimeIntervalSchedule()
.startingDailyAt(TimeOfDay.hourAndMinuteOfDay(9, 0)) //第天9：00开始
.endingDailyAt(TimeOfDay.hourAndMinuteOfDay(16, 0)) //16：00 结束
.onDaysOfTheWeek(MONDAY,TUESDAY,WEDNESDAY,THURSDAY) //周一至周五执行
                           .withIntervalInHours(1) //每间隔1小时执行一次
                           ).build();12345678910
   ```
   - CalendarIntervalScheduleBuilder
     和
     ```
     SimpleScheduleBuilder
     ```
     类似，都是表示从某一时刻开始，以一定时间间隔执行任务。但是
     ```
     SimpleScheduleBuilder
     ```
     无法指定一些特殊情况，比如每个月执行一次，每周执行一次、每一年执行一次
     属性：
     - interval 执行间隔
     - intervalUnit 执行间隔的单位（秒，分钟，小时，天，月，年，星期）

   ```
     Trigger trigger = TriggerBuilder.newTrigger().withIdentity("trigger1", "group1")
                   //加入 scheduler之后立刻执行
                   .startNow()
                   //定时 ，每个1秒钟执行一次
                   .withSchedule(calendarIntervalSchedule()
                           .withIntervalInWeeks(1) //每周执行一次
                           ).build();1234567
   ```
   最自由的`CronScheduleBuilder`

   - **CronScheduleBuilder**

   - 以上几个例子都可以使用cron表达式来表示。
     属性：
     - cron表达式。

   ```
   Trigger trigger = TriggerBuilder.newTrigger().withIdentity("trigger1", "group1")
                   //加入 scheduler之后立刻执行
                   .startNow()
                   //定时 ，每个1秒钟执行一次
                   .withSchedule(cronSchedule("0 0/2 8-17 * * ?") // 每天8:00-17:00，每隔2分钟执行一次
                           ).build();123456
   ```
### JobDetail & Job

   jobdetail 就是对job的定义，而job是具体执行的逻辑内容。
   具体的执行的逻辑需要实现 job类，并实现`execute`方法。
   **这里为什么需要有个JobDetai来作为job的定义，为什么不直接使用job？**
   **解释：如果使用jobdetail来定义，那么每次调度都会创建一个new job实例，这样带来的好处就是任务并发执行的时候，互不干扰，不会对临界资源造成影响**。



### 使用：
```java
//首先定义job内容
//定义一个JobDetail
JobDetail jobDetail = JobBuilder.newJob(HelloQuartz.class)
                //定义name和group
                .withIdentity("job1", "group1")
                //job需要传递的内容
                .usingJobData("name", "sdas")
                .build();
                
//定义触发器Trigger
//定义一个Trigger
Trigger trigger = TriggerBuilder.newTrigger().withIdentity("trigger1", "group1")
                //加入 scheduler之后立刻执行
                .startNow()
                //定时 ，每个1秒钟执行一次
				.withSchedule(SimpleScheduleBuilder.simpleSchedule().withIntervalInSeconds(1)
                //重复执行
                .repeatForever()).build();

//定义scheduler来以定义的触发器trigger为条件触发标准来执行任务
//创建scheduler
Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
scheduler.scheduleJob(jobDetail, trigger);
scheduler.start(); 
try {
	Thread.sleep(6000);
} catch (InterruptedException e) {
	e.printStackTrace();
}
//运行一段时间后关闭
scheduler.shutdown();

```

tips：

触发器有多种类型，可以建立cornTrigger触发器来触发。

代码：

```java
// 表达式调度构建器
CronScheduleBuilder scheduleBuilder = 
    CronScheduleBuilder.cronSchedule(scheduleJob.getCronExpression())//放入corn表达式
      .withMisfireHandlingInstructionDoNothing();

// 按新的cronExpression表达式构建一个新的trigger
CronTrigger trigger = TriggerBuilder.newTrigger().withIdentity(getTriggerKey(scheduleJob.getJobId()))
      .withSchedule(scheduleBuilder).build();
```



