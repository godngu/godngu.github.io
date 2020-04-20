---
title: "AWS CodeDeploy에서 환경변수 적용하는 방법"
permalink: /aws/s3-deploy-bitbucket-pipeline
excerpt: "Bitbucket pipeline을 통한 S3 Deploy"
last_modified_at: 2020-04-20
categories:
  - aws
  - bitbucket
  - s3
tags:
  - aws
  - bitbucket
  - pipeline
  - s3 deploy
#author_profile: true
---
## S3
### 버킷 생성
![](/assets/images/blog/0004/image01.png)

### 퍼블릭 액세스 설정
![](/assets/images/blog/0004/image02.png)

### S3 접근을 위한 사용자 생성 및 key 다운로드
> bitbucket에서 S3로 액세스 하기 위해서는 `accessKey`와 `secretKey`가 필요합니다.
> 사용자는 보안상 S3 액세스 권한만 가지며, 사용자의 `.csv`를 다운로드 받아서 사용합니다.

#### 사용자 생성
![](/assets/images/blog/0004/image03.png)

#### 정책 연결
> `AmazonS3FullAccess` 정책 직접 연결

![](/assets/images/blog/0004/image04.png)

#### .csv 사운로드
![](/assets/images/blog/0004/image05.png)


---
## Bitbucket repository
### 레파지토리 생성
![](/assets/images/blog/0004/image06.png)

### 파이프라인 설정파일 등록
> `other`를 선택하고 `Commit file`을 클릭하면 master 브랜치에 `bitbucket-pipeline.yml` 파일이 push 됩니다.
> (만약 브랙치 권한이 없을 경우 등록되지 않습니다.)

![](/assets/images/blog/0004/image07.png)

__bitbucket-pipeline.yml이 잘 등록되고 배포된 결과__
![](/assets/images/blog/0004/image08.png)

### 파이프 라인에서 사용할 변수 등록
> S3에 접근하기 위한 key를 등록 합니다.
> key는 bitbucket-pipeline.yml에 직접 넣어도 되지만 `보안`상 외부에서 주입하는게 좋습니다.
> key 등록시에는 `Secured`를 선택하여 암호화 합니다.

![](/assets/images/blog/0004/image09.png)

### 비트버킷 파이프라인에 S3 Deploy 설정 등록
> `branches`에 develop/master 브랜치 별로 설정합니다.
> `LOCAL_PATH`는 배포가 될 대상 디렉토리이며, contents 디렉토리는 배포되지 않고 그 내부 파일과 디렉토리가 배포됩니다.
```yaml
image: atlassian/default-image:2

pipelines:
  default:
    - step:
        script:
          - echo "Everything is awesome!"

  branches:
    master:
      - step:
          script:
            - pipe: atlassian/aws-s3-deploy:0.4.1
              variables:
                AWS_ACCESS_KEY_ID: $AWS_ACCESS_KEY
                AWS_SECRET_ACCESS_KEY: $AWS_SECRET_KEY
                AWS_DEFAULT_REGION: $REGION
                S3_BUCKET: 'dev-godngu'
                LOCAL_PATH: 'contents'
                ACL: 'public-read'
```
