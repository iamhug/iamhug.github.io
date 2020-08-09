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

Trigger 는 모두 `QRTZ_TRIGGERS`에 등록되고 CRON, SIMPLE 타입에 따라 `QRTZ_CRON_TRIGGERS`, `QRTZ_SIMPLE_TRIGGERS`에 저장된다.


### QRTZ_CRON_TRIGGERS

|SCHED_NAME|TRIGGER_NAME|TRIGGER_GROUP|CRON_EXPRESSION|TIME_ZONE_ID|
|----------|------------|-------------|---------------|------------|
|scheduler-name|trigger-1|trigger-group|0/20 * * ? * * |Asia/seoul|
|scheduler-name|trigger-2|trigger-group|0/30 * * ? * * |Asia/seoul|

*****CRON_EXPRESSION**

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



