
Quartz 2.3.0
---


```java
package org.quartz;

public interface Job {
	public void execute(JobExecutionContext context) throws JobExecutionException;
}
``` 

**Durability**
> if a job is non-durable, it is automatically deleted from the scheduler one there are no longer any active triggers associated with it. In other words, non-durable jobs have a life span bounded by the existence of its triggers.

**RequestsRecovery**
> if a job "requests recovery", and it is executing during the time of a 'hard shutdown' of the scheduler (i.e. the process it is running within crashes, or the machine is shut off), then it is re-executed when the scheduler is started again. In this case, the JobExecutionContext.isRecovering() method will return true.

**JobExecutionException**
> Finally, we need to inform you of a few details of the `Job.execute(..)` method. The only type of exception (including RuntimeExceptions) that you are allowed to throw from the execute method is the JobExecutionException. Because of this, you should generally wrap the entire contents of the execute method with a `try-catch` block. You should also spend some time looking at the documentation for the JobExecutionException, as your job can use it to provide the scheduler various directives as to how you want the exception to be handled.

### Trigger

**Trigger Properties**

* jobKey: trigger 가 실행할 job의 식별자
* startTime: job이 처음으로 실행될 시간을 나타내며 `java.util.Date` 형태
* endTime: job이 더이상 실행되지 않을 시간

**Priority**

많은 trigger 가 존재한다고 가정할 때, Quartz가 모든 trigger를 동시에 실행할 리소스가 없는 경우가 발생할 수 있다. 이런 경우에 각 tigger에 `priority`를 설정하면 우선적으로 실행시킬 수 있다. 만약 `priority`를 설정하지 않을 경우 기본값은 `5`로 셋팅된다.
주의할점은 `priority` 값은 동시에 실행되는 경우에만 적용할 수 있다. 그리고 trigger의 job이 `require recovery`를 실행할 경우 이전에 설정해 놓은 `priority`값을 사용한다.

**Misfire Instructions**

`misfire`는 스케줄러가 셧다운 되었거나 사용가능한 `thread`가 없을 때 발생한다. 기본적으로 `smart policy instruction`을 사용하는데 이는 trigger 타입과 설정에 기반으로 동적으로 사용된다. 어플리케이션에는 각 trigger type 에 알맞는 `misfire instruction`을 설정해야 한다. 

**Simple Trigger**

`simple trigger`는 한번만 실행하거나 특정 시점 기준으로 N번 반복하여 실행할 때 사용할 수 있다. 이런 기능을 실행시키기 위해서 `simple trigger`는 `start-time`, `end-time`, `repeat-count`, `repeat-interval` 을 설정해야 한다.

_특정 시간에 실행하고 반복하지 않을 트리거_

```java
SimpleTrigger trigger = (SimpleTrigger)newTrigger()
			.withIndentity("trigger", "group")
			.startAt(someStartTime)
			.forJob("job", "group")
			.build();
```

_특정 시간에 실행하고 10초간격으로 10회 반복하는 트리거_

```java
SimpleTrigger trigger = (SimpleTrigger)newTrigger()
		.withIdentity("trigger", "group")
		.startAt(someStartTime)
		.withSchdule(
			simpleSchedule()
				.withIntervalInSecond(10)
				.withRepeatCount(10)
		)
		.forJob(job)
		.build();
```

_한번 실행되고 5분 후에 다시 실행될 트리거_

```java
SimpleTrigger trigger = (SimpleTrigger) newTrigger()
		.withIdentity("trigger", "group")
		.startAt(futureDate(5, IntervalUnit.MINUTE))
		.forJob(job)
		.build();
```

_지금 실행되고 22:00시 까지 5분 간격으로 실행될 트리거_

```java
SimpleTrigger trigger = (SimpleTrigger) newTrigger()
		.withIdentity("trigger", "group")
		.withSchedule(
			simpleSchedule()
				.withIntervalInMinutes(5)
				.repeatForever()
		)
		.endAt(dateOf(22, 0, 0))
		.build();
```

_다음 시간에 실행되고 2시간 간격으로 계속 실행될 트리거_

```java
SimpleTrigger trigger = (SimpleTrigger) newTrigger()
		.withIdentity("trigger")
		.startAt(evenHourDate(null))
		.withSchedule(
			simpleSchedule()
				.withIntervalInHours(2)
				.repeatForever()
		)
		.build();
	
scheduler.scheduleJob(trigger, job);
```


   
 


