Spring Quartz & Spring Batch Integration
---

## Clustering

```yaml
spring:
  quartz:
    properties:
      org.quartz:
        scheduler:
          instanceId: AUTO      # 각 인스턴스가 등록될 때 instance-id 생성방식
        threadPool:
          threadCount: 20       # 하나의 인스터스에서 실행할 thread count
        jobStore:
          tablePrefix: QRTZ_
          isClustered: true     # 클러스터 사용 여부
          class: org.quartz.impl.jdbcjobstore.JobStoreTX  # 클러스터를 지원하는 store 타입
```

## Quartz Trigger & Job

인스턴스가 시작되면 자동으로 스케줄러가 등록된다.

|SCHED_NAME|INSTANCE_NAME|LAST_CHECKIN_TIME|CHECKIN_INTERVAL|
|----------|-------------|-----------------|----------------|
|scheduler-name|hugui-iMac.local59813123|159681332098|7500|
|scheduler-name|hugui-iMac.local59178911|159681339912|7500|

**INSTANCE_NAME**

어플리케이션이 실행되면서 프로퍼티에 설정한 `spring.quartz.properties.org.quartz.scheduler.instanceId` 정책에 따라 등록된다.

Trigger 는 모두 `QRTZ_TRIGGERS`에 등록되고 CRON, SIMPLE 타입에 따라 `QRTZ_CRON_TRIGGERS`, `QRTZ_SIMPLE_TRIGGERS`에 저장된다.


### QRTZ_CRON_TRIGGERS

|SCHED_NAME|TRIGGER_NAME|TRIGGER_GROUP|CRON_EXPRESSION|TIME_ZONE_ID|
|----------|------------|-------------|---------------|------------|
|scheduler-name|trigger-1|trigger-group|0/20 * * ? * * |Asia/seoul|
|scheduler-name|trigger-2|trigger-group|0/30 * * ? * * |Asia/seoul|

**CRON_EXPRESSION**

trigger가 실행될 cron-expression

### QRTZ_SIMPLE_TRIGGERS

|SCHED_NAME|TRIGGER_NAME|TRIGGER_GROUP|REPEAT_COUNT|REPEAT_INTERVAL|TIMES_TRIGGERED|
|----------|------------|-------------|------------|---------------|---------------|
|scheduler-name|triger-3|trigger-group|100|5000|30|

**REPEAT_COUNT**

trigger가 반복될 횟수

**REPEAT_INTERVAL**

trigger가 반복될 간격 (단위:ms)

**TIMES_TRIGGERED**

trigger가 반복된 총 횟수

### QRTZ_JOB_DETAILS

|SCHED_NAME|JOB_NAME|JOB_GROUP|JOB_CLASS_NAME|...|JOB_DATA|
|----------|--------|---------|--------------|---|--------|
|scheduler-name|job-1|job-group|com.demo.JobClazz|...|parameter=1\nvalue=2|

**JOB_CLASS_NAME**

Job을 실행할 구현체의 package경로

**JOB_DATA**

Job을 실행하면서 `JobDataMap`으로 활용할 수 있는 정보. key=value로 바인딩 시킬 수 있고 key간에는 `new line`으로 구분할 수 있다.

`JOB_CLASS_NAME`에 명시된 클래스를 기준으로 Quartz에서 지정된 시간에 스케줄러를 실행하게 된다. `JOB_CLASS_NAME`에는 `org.quartz.Job` 인터페이스를 구현한 클래스여야 한다.

```java

package org.quartz;

public interface Job {
	void execute(JobExecutionContext context) throws JobExecutionException;
}

```

`spring-quartz`를 사용한다면 `org.springframework.scheduling.quartz.QuartzJobBean` 클래스를 상속받으면 된다.

```java
public abstract class QuartzJobBean implements Job {

	...
	
	protected abstract void executeInternal(JobExecutionContext context) throws JobExecutionException;
}

```



