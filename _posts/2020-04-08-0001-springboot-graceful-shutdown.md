---
title: "spring boot 우아하게 종료하기(graceful shutdown)"
permalink: /server/springboot-graceful-shutdown
excerpt: "spring boot에서 내장 tomcat 종료시 서비스를 바로 죽이지 않고 대기 했다가 종료하는 방법에 대해서 설명합니다."
last_modified_at: 2020-04-08
categories:
  - spring
tags:
  - tomcat
  - spring boot
  - gracefully shutdown
author_profile: true
header:
  teaser: /assets/images/blog/0001/img01.png
---

### 일반적인 재기동 프로세스
> kill -15 {PID}

서버에서 배포 후 재기동을 할 경우 kill 명령어를 사용하게 됩니다.
kill -9를 사용하면 곧바로 죽지만, `kill -15(SIGTERM)`을 사용하면 "죽어라~" 하고 요청을 보내게 됩니다.
하지만 이 경우라도 **`진행중인 프로세스가 있으면 곧바로 종료됩니다.`**


### 실제 업무 사례
제 경우 엑셀 업로드 후 업로드 내용을 DB insert 하는 업무라서 시간이 오래 걸리는 업무가 있었습니다.
아무리 서버를 이중화 하고, 무중단 배포를 하더라도 기존에 동작 중인 프로세스가 곧바로 죽어서 문제가 됐습니다.


### 설정
이를 극복하기 위해 아래와 같은 코드를 통해서 일정 시간 대기를 했다가 서버를 재기동 할 수 있습니다.
**`최대 20분을 대기하고, 10초마다 상태를 체크하도록 설정`** 했습니다.


#### Code
![img01](/assets/images/blog/0001/img01.png)
```java
import java.util.concurrent.Executor;
import java.util.concurrent.ThreadPoolExecutor;
import java.util.concurrent.TimeUnit;
import lombok.extern.slf4j.Slf4j;
import org.apache.catalina.connector.Connector;
import org.springframework.boot.web.embedded.tomcat.TomcatConnectorCustomizer;
import org.springframework.context.ApplicationListener;
import org.springframework.context.event.ContextClosedEvent;

/**
 * 우아하게 종료하기
 * kill -15(SIGTERM) 종료 요청이 실행된 경우 {INTERVAL_TIMEOUT_SECONDS} 단위로 스레드풀 진행 여부를 검사합니다.
 * 최대 {LIMIT_TIMEOUT_MINUTES} 까지 대기하며, {LIMIT_TIMEOUT_MINUTES} 이 초과된 경우 강제 종료합니다.
 */
@Slf4j
public class GracefulShutdown implements TomcatConnectorCustomizer, ApplicationListener<ContextClosedEvent> {

    private static final int INTERVAL_TIMEOUT_SECONDS = 10;
    private static final int LIMIT_TIMEOUT_MINUTES = 20;

    private volatile Connector connector;

    @Override
    public void customize(Connector connector) {
        this.connector = connector;
    }

    @Override
    public void onApplicationEvent(ContextClosedEvent event) {
        this.connector.pause();
        Executor executor = this.connector.getProtocolHandler().getExecutor();
        try {
            if (executor instanceof ThreadPoolExecutor) {
                final long begin = nanoTime();
                ThreadPoolExecutor threadPoolExecutor = (ThreadPoolExecutor) executor;
                // 이미 시작된 Task는 실행 하고, 새로운 작업은 받지 않음
                threadPoolExecutor.shutdown();
                do {
                    log.warn("Tomcat 스레드풀이 아직 종료되지 않았습니다. [경과시간: {}초]", toSeconds(elapsedNanoTime(begin)));
                    if (limitTimeout(begin)) {
                        log.warn("제한시간 {}분 동안 종료되지 않아 강제 종료합니다.", LIMIT_TIMEOUT_MINUTES);
                        threadPoolExecutor.shutdown();
                    }
                } while (!threadPoolExecutor.awaitTermination(INTERVAL_TIMEOUT_SECONDS, TimeUnit.SECONDS));

                if (threadPoolExecutor.isTerminated()) {
                    log.info("진행중인 작업이 완료 되었습니다. 즉시 정지 합니다.");
                    threadPoolExecutor.shutdownNow();
                }
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
            Thread.currentThread().interrupt();
        }
    }

    private boolean limitTimeout(long beginNanoTime) {
        return elapsedNanoTime(beginNanoTime) > TimeUnit.MINUTES.toNanos(LIMIT_TIMEOUT_MINUTES);
    }

    private long elapsedNanoTime(long beginNanoTime) {
        return nanoTime() - beginNanoTime;
    }
}
```
