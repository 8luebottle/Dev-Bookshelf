# System Design Interview 
The objective of this book is to provide a reliable strategy to approach the system design questions.
The right strategy and knowledge are vital to the success of an interview. This book provides solid knowledge in building a scalable system.

### GIT X AWS Mentoring Program 
IT 기술 독서 with Amazon Web Services  
- System Design Interview 가상 면접 사례로 배우는 대규모 시스템 설계 기초

## Table of Contents

Chapter|         Title       | Page  |   Date
-------|:-------------------:|:-----:|:--------:
[01](#1)|사용자 수에 따른 규모 확장성|001-032|
02     |개략적인 규모 측정|033-038|
03     |시스템 설계 면접 공략법|039-050|
04     |처리율 제한 장치의 설계|051-076|
05     |안정 해시 설계|077-090|
06     |키-값 저장소 설계 |091-116|
07     |분산 시스템을 위한 유일 ID 생성기 설계|117-126|
08     |URL 단축기 설계|127-140|
09     |웹 크롤러 설계|141-164|
10     |알림 시스템 설계|165-182|
11     |뉴스 피드 시스템 설계|183-196|
12     |채팅 시스템 설계|197-222|
13     |검색어 자동완성 시스템|223-246|
14     |유튜브 설계|247-276|
15     |구글 드라이브 설계|277-300|
16     |배움은 계속된다|301-307|

---
# 1
## 사용자 수에 따른 규모 확장성
> Scale From Zero To Millions Of Users

시스템의 규모를 확장하는 것은 지속적이고 반복적인 과정이다.

- **Stateless**  
  웹 계층은 무상태 계층으로
- **Redundancy**  
  모든 계층에 다중화 도입
- **Cache**  
  가능한 한 많은 데이터를 캐시할 것
- **Data centers**  
  여러 데이터 센터를 지원할 것
- **CDN**  
  정적 콘텐츠는 CDN을 통해 서비스할 것
- **Sharding**  
  데이터 계층은 샤딩을 통해 그 규모를  확장할 것
- **Split tiers**  
  각 계층은 독립적 서비스로 분할할 것
- **Monitor & Automation tool**  
  시스템을 지속적으로 모니터링하고, 자동화 도구들을 활용할 것

<details><summary>NOTE</summary><br>

  사용자가 증가하게 되면 단일  서버로는 충분치 않아 여러 서버를 두게 된다.

<img width="546" alt="multiple servers" src="https://user-images.githubusercontent.com/48475824/130340899-9c14368b-57ca-49e0-a055-9cb133004ceb.png">

  - [서버 1: Web server] 웹/모바일 트래픽 처리용
  - [서버 2: Database] 데이터베이스 용

  보통의 경우 RDBMS를 사용하지만 비-관계형 DB가 필요시 되는 상황이 있다.

  NoSQL DB를 선택해야하는 경우는 아래와 같다.
  - **Super-low latency**  
    아주 낮은 응답 지연시간이 요구되는 경우
  - **Unstructured data**  
    비정형 데이터를 다뤄야하는 경우
  - **Only need to serialize & deserialize data**  
    데이터를 단순히 직렬화하거나 역직렬화만 하면 되는 경우
      - Data: JSON, YAML, XML, etc
  - **Massive amount of data**  
    상당히 대규모의 데이터를 저장해야 하는 경우

  ### Vertical scaling vs horizontal scaling
  DB의 규모를 확장 시 `수직적 확장`, `수평적 확장` 두 가지 방법이 있다
  - 서버로 유입되는 트래픽 양이 적을 경우 → `수직적 확장`
  - 대규모 앱을 지원할 경우 → `수평적 확장`

  ### Scale up 수직적 확장
  서버에 고사양 자원(CPU, RAM, etc)을 추가시키는 확장
  - **장점**
    - 단순함
  - **단점**
    - 비싼 비용
    - 규모 확장의 한계성
    - 장애에 대한 처리 미흡  
      예) failover, redundancy

  ### Scale out 수평적 확장
  더 많은 서버를 추가시키는 확장. 샤딩(sharding)이라고도 부름.
  - 대규모 DB를 작은 단위(shard)로 분할한다. 샤드에 보관되는 데이터 사이에는 중복 X.

  **[Sharding 도입 시 고려할 사항]**
  1. **Data resharding**   
    shard exhaustion 문제
  2. **Celebrity**   
    hotspot key 문제
  3. **Join and de-normalization**   
    여러 샤드에 걸친 데이터는 조인하기가 힘듬 ⇒ DB 비정규화를 통해 하나의 테이블에서 질의가 수행되도록 하기

  ## Load Balancer
  웹 서버들에게 트래픽을 고르게 분산시킴으로써 부하를 분산시켜준다.  
  - 웹 서버는 클라이언트의 접속을 직접 처리하지 X ⇒ 사용자들이 Load balancer의 Public IP 로 접속하기에
  - 보안성 향상 ⇒ 서버 간 통신에는 Private IP를 사용  
    Load balancer는 웹 서버와 통신시 private IP를 사용함.

  ## DB 다중화
  - 서버간에 first-second 관계를 설정
    - first: 데이터 원본, write operation
    - second: 데이터 사본, read operation
  - **장점**
    - 더 나은 성능
    - 안정성
    - 가용성

  ## Cache
  앱의 성능은 `DB를 얼마나 자주 호출하느냐`에 따라 좌우된다. 캐시를 통해 응답시간(latency)을 개선한다.
  - Cache tier
    - 응답 시간 개선
    - DB 부하 ↓

  **[Cache 사용시 고려 할 요소]**
  1. 어떤 상황에 캐시 사용?
  1. 어떤 데이터를 캐시에?
  1. 캐시에 보관된 데이터는 어떻게 만료?
  1. 일관성은 어떻게 유지?
  1. 장애는 어떻게 대처?
    예) SPOF 피하기
  1. 캐시 메모리의 크기는?
  1. 데이터 방출(eviction) 정책은?
    예) LRU, LFU, FIFO, etc

  ## CDN
  정적 콘텐츠를 전송시 CDN(콘텐츠 전송 네트워크)을 이용한다.
  **[CDN 사용시 고려할 요소]**
  1. 비용
  1. 적절한 만료 시한 설정
    - 너무 짧을 경우 ⇒ 원본 서버에 빈번히 접속하게 됨
    - 너무 길 경우 ⇒ 콘텐츠의 신선도가 떨어지게 됨
  1. 장애에 대한 대처 방안
  1. 콘텐츠 무효화(invalidation) 방법

  ## Stateless
  웹 계층을 수평적으로 확장하기 위해서는 상태정보를 웹 계층에서 제거해 주어야 한다. (무상태 웹 계층)
  - 상태 정보의 예) user's session data
  상태 정보가 웹 서버로부터 물리적으로 분리됨으로써 구조는 단순해지고, 안정적이며, 규모 확장이 쉬워지게 된다.

  ## Data centers
  여러 데이터 센터를 사용함으로써 전 세계 유저가 원하는 콘텐츠를 빠르게 이용 가능하도록 한다.
  - geo-routing: 사용자에게 가장 가까운 데이터 센터로 안내

  **[다중 데이터 센터 아키텍처의 기술적 난제]**
  1. 트래픽 우회
  1. 데이터 동기화
  1. 테스트와 배포

  ## Message Queue
  시스템의 component를 분리하기 위해 메시지 큐를 사용한다. 느슨한 결합으로 인해 독립적인 확장이 가능해진다.
  - 메시지의 버퍼 역할을 한다.
  - 비동기적으로 전송한다.

  ## Log, Metric, and Automation
  - **Log**   
    시스템의 오류와 문제들을 쉽게 찾아낼 수 있게 해주기 위해 에러 로그를 모니터링 할 것.
  - **Metric**  
    수집한 매트릭을 통해 사업 현황에 관한 주요 정보를 얻을 수 있다. 또한 시스템의 현재 상태를 파악할 수 있다.
    - Host level metrics  
      CPU, Memory, Disk I/O, etc
    - Aggregated level metrics  
      database tier, cache tier, etc
    - Key business metrics  
      daily active users, retention, revenue, etc
    - Automation

</details>
