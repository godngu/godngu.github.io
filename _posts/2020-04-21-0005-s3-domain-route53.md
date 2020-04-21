---
title: "Route 53을 이용하여 S3 버킷에 도메인 연결시 "사용 가능한 대상 없음" 문제"
permalink: /aws/s3-domain-route53
excerpt: "AWS의 Route 53을 이용하여 S3에 도메인 연결시 '사용 가능한 대상 없음'으로 나오는 문제 해결 방법입니다."
last_modified_at: 2020-04-21
categories:
  - aws
tags:
  - AWS
  - s3
  - route 53
author_profile: true
header:
  teaser: /assets/images/blog/0005/0005_teaser.png
---

### 문제의 증상
* S3에 도메인을 연결하여 정적 컨텐츠를 서비스 하고자 했습니다.
* 각종 블로그에서 가이드 한 대로 S3 버킷을 설정 했습니다.
    * 퍼블릭 액세스
    * 버킷 정책 추가
    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Sid": "PublicReadForGetBucketObjects",
                "Effect": "Allow",
                "Principal": "*",
                "Action": "s3:GetObject",
                "Resource": "arn:aws:s3:::{your resource name}/*"
            }
        ]
    }
    ```
    * 속성 > 정적 웹 사이트 호스팅


> Route 53에서 S3 버킷에 도메인 연결 시도시 **`선택 가능한 대상 없음`**

![](/assets/images/blog/0005/image01.png)


### 원인 및 해결
> ##### bucket 명칭을 도메인 이름으로 해라!
> 즉 www.domain.com의 도메인을 S3 버킷에 연결하기 위해서는 S3 버킷명 또한 동일하게 해줘야 합니다.
[https://aws.amazon.com/ko/premiumsupport/knowledge-center/route-53-no-targets/](https://aws.amazon.com/ko/premiumsupport/knowledge-center/route-53-no-targets/)
