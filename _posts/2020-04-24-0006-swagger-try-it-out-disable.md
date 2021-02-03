---
title: "Swagger에서 'Try it out'버튼 비활성화 처리"
layout: single
permalink: /dev/swagger-try-it-out-disable
excerpt: "Swagger에서 Try it out 버튼을 특정 환경에서 사용하지 못하도록 설정 할 수 있습니다."
last_modified_at: 2020-04-21
categories:
  - aws
tags:
  - AWS
  - s3
  - route 53
author_profile: true
header:
  teaser: /assets/images/blog/0006/teaser.png

# table of contents
toc: true # 오른쪽 부분에 목차를 자동 생성해준다.
toc_label: "table of content" # toc 이름 설정
toc_icon: "bars" # 아이콘 설정
toc_sticky: true # 마우스 스크롤과 함께 내려갈 것인지 설정
---
+ swagger-ui.html은 RESTful API의 Document를 예쁘고, 쉽게 테스트 할 수 있는 화면을 제공합니다.
+ 개발 환경이라면 이러한 테스트 환경이 매우 유용할 수 있겠지만, `운영 환경` 에서는 테스트 데이터가 인입되지 않도록 해야 합니다.

### 규칙
> 1. `운영 환경`에서는 `조회 API(GET method)`만 테스트 가능 하도록 합니다.
> 2. 그 외 환경에서는 모든 테스트가 가능해야 합니다.


#### AS-IS
![](/assets/images/blog/0006/image01.png)

#### TO-BE
##### Code
![](/assets/images/blog/0006/image03.png)
```java
@Configuration
@EnableSwagger2
public class SwaggerConfig {
    @Bean
    @Profile("prod")
    public UiConfiguration uiConfig() {
        return UiConfigurationBuilder.builder()
            .supportedSubmitMethods(new String[]{"get"})
            .build();
    }
}
```

##### 적용 결과
![](/assets/images/blog/0006/image02.png)
> `POST` 에서는 `Try it out` 버튼이 비활성화 된다.
