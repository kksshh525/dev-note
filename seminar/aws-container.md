-   [1일차 실습 교육 사이트](http://container-workshop.s3-website.ap-northeast-2.amazonaws.com/public/)
-   [2일차 실습 교육 사이트](https://eksworkshop.com/)



# 1일차 (2/4)

1.  이상향

-   경쟁우위에 점하기 위해서는 비즈니스 혁신이 필요함. 거기서 부터 시작함 (인프라 setup) → innovation flywheel

    -   해외 많은 기업들의 실제 사례

-   변화를 위한 필요 요소

    -   아키텍쳐 패턴 : 모놀리스 - 마이크로 서비스 ( 변화의 영향이 적으면 릴리즈 속도가 증가 할 수 있다 ?)

        -   마이크로서비스: 독립성을 유지함, 다른 의존도가 없기 때문에 배포가 빠르다.

    -   운영 모델: 조직 관점에서 MSA(개발팀, 인프라팀) 이면, 경계가 모호해진다.

        ※  여러개 컴포넌트, 조직으로 분리되면 관리, 운영하기 어렵지 않냐?  AWS의 자랑, AWS 운영책임 모델을 제공한다 (서비스 카테고리를 제공함)

    -   소프트웨어 딜리버리: 배포 사이클이 여러개

        -   developers - service - delivery pipeline (build - test - release -...)
        -   best practice
            -   민첩성을 위해 팀 분해
            -   모든 것을 자동화
            -   툴 표준화
            -   belts and suspenders ? (governance, templates)
            -   infrastructure as code

-   컨테이너: 위의 문제들의 솔루션이다

    -   소프트웨어 개발 가속화

1.  현실

-   provisioning 걸리는 시간
-   로컬에서는 잘됨, 운영단에서 이슈 발생
-   다양한 기술 스택
-   다양한 하드웨어 배포 환경
-   마이그레이션 하는 환경
-   위의 문제를 해결 → Docker

1.  패러다임

-   Immutable Infrastructure
    -   공통된 영역은 불변하고, 바뀌는 부분만 변할 수 있다.
    -   처음 100대 setting은 똑같았다. 나중에 시간이 지나면 서버가 각각 Unique 해짐  ( SnowFlak → 피닉스 로 변경하는 과정이다. )
    -   장점
        -   lightweight 가벼움
        -   run anywehere어디든 배포함
        -   consistent env

1.  컨테이너란?

-   코드, 런타임 환경, 라이브러리, 설정파일등 애플리케이션 구동에 필요한 모든 것들을 실행 가능하도록 묶은 일종의 소프트웨어 패키지
-   CPU, memory , 네트워크 리소스를 OS수준에서 가상화 하여 다른 애플리케이션으로 부터 논리적으로 격리된 샌드 박스 환경을 제공

Docker 구조

-   client , docker host, registry (이미지 저장소)  - googlling
-   ![](https://docs.docker.com/engine/images/architecture.svg)



Docker Image

-   **Union F/S (file system)**  (셀로판지, 겹쳐도 하나로 보인다) - 용량이 커질 수 있기 때문에, 각각의 이미지를 reference (참조) 해서 이런 부분을 해결했다.
-   

![](https://hub.packtpub.com/wp-content/uploads/2018/04/image3-1.jpg)

Docker Workflow

-   pull an image from Docker Hub

-   Build an image form Dockerfile

-   push an image

-   Run a container

-   Insepect a contatiner

-   Stop a container

-   Remove a container

-   Remove an image

    docker ps docker stop docker start



## AWS ECS

AWS ECS 구성 요소

-   task definition : json 형식으로 정의함, ECR(repository 같은걸로 사용가능 하다)
-   task
    -   컨테이너를 실행하는 최소 단위
    -   task definition에 정의된 스펙(network, docker image, CPU/MEM) 을 바탕으로 생성
    -   task definition 은 revision 저장됨
-   service
    -   task를 실행하고 유지 관리
    -   ELB를 통해 트래픽을 서비스내 컨테이너로 분산
    -   Auto scaling옵션으로 task 유동 조정 가능

배치 전략 (task replacement constraint)

-   binpacking : 고루게 배포?
-   spread: 균등하게
-   affinity: 친화적으로 labeling 해서 배포
-   distinct instance: 유니크하게 배포

ECS 장점중 하나 logging and monitoring

-   cloud watch logs → S3
-   cloud watch logs → kinesis (stream)
-   cloud watch logs → Lambda

Task별로  Role 도 부여가능 하다.

CloudWatch ECS matrix

-   2 Dimensions
    -   cluster name
    -   service name

스케일링 정책 2가지

-   step scahling policy
-   target tracking scaling policy  ( 목표값을 설정하고)

## AWS Fargate

-   컨테이너 OS upgrade, 등등 이런 관리를 해주는 서비스임
-   : run serverless containers
-   **AWS ECS  + Fargate 조합으로 완전 관리형 컨테이너 서비스 구성**

## Kubernetes

-   ochestration tool 중에 하나임
-   오프소스 컨테이너 관리 플랫폼  (CNCF cloud native computing foundation 에서 만듬)
-   AWS EKS 나옴.
    -   언제 사용하나?
    -   MSA, PAAS, Migration , machine learning







# 2/5 수요일 2일차 교육

------

orchestration tools

-   https://www.linux.com/news/8-open-source-container-orchestration-tools-know/
-   Q&A
-   Keyword
-   sticy session
-   headless
-   deplyoment - rolling update,
-   graceful shut down
-   kuberneets기억할 것 3가지 → service, deployment, pods
-   ingress
-   control plane
-   ECS  or  EKS  + Fargate
-   helm
    -   kubernetes의 packageManager
    -   chart를 만들어서 repository에 만들어서, 사용한다.
    -   helm → tiler → APIserver 가 다시 → tiler
    -   
-   provisioning - chef, ansible, puppet vs  container 의 차이
    -   멱등성으로 바라보는 차이가 있다. chef, ansible 같은 경우에는 계속해서 요청을 날리면 쌓이는 느낌이다.

![https://miro.medium.com/max/2658/1*WRlTslNkaEPgFI1EPC67MQ.png](https://miro.medium.com/max/2658/1*WRlTslNkaEPgFI1EPC67MQ.png)

-   health check
    -   liveness - pod 재생산 ( A → B 의존도가 있는데, B의 상태live를 체크 한다.)
    -   readiness - pod는 running 중인데, 뭔가 안뜬 상황인 경우