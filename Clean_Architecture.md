# Clean Architecture
Clean Architecture: A Craftsman's Guide to Software Structure and Design

### DirectCloudLAB Study Group
사내 스터디 그룹 (매월 둘째주 넷째주 목요일)

## Table of Contents

| Chapter  |      Title       |  Page   |    Date    |
|:--------:|:----------------:|:-------:|:----------:|
| [00](#0) |   Introduction   |     000-004      | 04.01.2023 |
| [01](#1) |    설계와 아키텍처란?    | 005-014 | 04.01.2023 |
| [02](#2) | 두 가지 가치에 대한 이야기  | 015-022 | 04.02.2023 |
|    03    |     패러다임 개요      | 023-028 ||
|    04    |    구조적 프로그래밍     | 029-036 ||
|    05    |   객체 지향 프로그래밍    | 037-050 ||
|    06    |    함수형 프로그래밍     | 053-060 ||
|    07    |  SRP: 단일 책임 원칙   | 061-072 ||
|    08    |  OCP: 개방-폐쇄 원칙   | 073-080 ||
|    09    | LSP: 리스코프 치환 원칙  | 081-086 ||
|    10    | ISP: 인터페이스 분리 원칙 | 087-090 ||
|    11    |    의존성 역전 원칙     | 091-096 ||
|    12    |       컴포넌트       | 097-106 ||
|    13    |     컴포넌트 응집도     | 107-114 ||
|    14    |     컴포넌트 결합      | 115-138 ||
|    15    |      아키텍처란?      | 139-154 ||
|    16    |       독립성        | 155-168 ||
|    17    |     경계: 선 긋기     | 169-184 ||
|    18    |      경계 해부학      | 185-192 ||
|    19    |      정책과 수준      | 193-198 ||
|    20    |      업무 규칙       | 199-206 ||
|    21    |    소리치는 아키텍처     | 207-212 ||
|    22    |     클린 아키텍처      | 213-222 ||
|    23    |   프레젠터와 험블 객체    | 223-228 ||
|    24    |      부분적 경계      | 229-234 ||
|    25    |      계층과 경계      | 235-244 ||
|    26    |  메인(Main) 컴포넌트   | 245-250 ||
|    27    |  '크고 작은 모든'서비스들  | 251-260 ||
|    28    |      테스트 경계      | 261-266 ||
|    29    |   클린 임베디드 아키텍처   | 267-286 ||
|    30    |  데이터베이스는 세부사항이다  | 287-296 ||
|    31    |    웹은 세부사항이다     | 297-302 ||
|    32    |  프레밍워크는 세부사항이다   | 303-308 ||
|    33    |  사례 연구: 비디오 판매   | 309-314 ||
|    34    |     빠져 있는 장      | 315-333 ||
----
# 0
## Introduction
> 옮긴이의 글, 추천사, 서문, 감사의 글

<details><summary>Dead Code</summary></summary>

> 실행되지 않는 코드 
> 
> Dead code refers to sections of a computer program that are no longer executed or accessed by other parts of the program.

#### Dead Code
- 프로그램의 크기 ↑
- 유지보수성 ↓
- 가독성 ↓

#### Types of dead code
- **주석 처리된 코드** 
  - 주석처리 된 코드로, 더 이상 사용되지 않거나 삭제되지 않아 불필요 하게 남아 있는 코드.
- **호출되지 않는 함수/메서드** 
  - 사용처 없이 방치되어 있는 함수/메서드.
- **조건문에서 실행되지 않는 코드** 
  - 조건문(`if/switch`)에서 조건이 항상 `false` 가 되어 실행되지 않는 코드.
- **접근 불가한 코드** 
  - 분기 명령문(`return, break`) 뒤에 위치해 있지만 앞선 잘못된 로직으로 인해 절대 실행될 수 없는 코드.
- **중복 코드** 
  - 여러번 반복하여 존재하는 동일한 코드.
- **폐기된 코드** 
  - 더 이상 사용되지 않는 코드.

#### Dead code in the real world  
Dead code 로 인해 문제가 발생한 실 사례는 MS사의 Windows98 OS가 있음.
- Windows98 은 기존의 Windows95와 유사한 구조를 지님.
- 수많은 코드가 재사용 됨.
  - 가져온 코드 중에서 사용되지 않는 불필요한 코드(`폐기된 코드`)가 존재. 
  - 하지만 이는 방치된 채로 남아 있었음.
- 불필요한 코드는 메모리와 디스크 공간 낭비를 초래.
  - 느려진 속도, 안정성 문제 발생.
    - 느려진 속도: 부팅, 응용 프로그램 etc.
    - 안정성: OS 충돌, 오동작 etc.
- 새 OS Windows ME 를 통해 문제 해결.
  - Refactoring 을 통한 Dead Code 제거. 

</details>

<details><summary>Speculative generality</summary>

> 추측성 일반화

추후에 사용할 것이 예상하여 불필요하게 확장성(`overy extensible`) 있게 만들어 둔 코드.
- 실제로 사용되지 않을 수 있음.
  - 초기에 사용 가능성을 염두에 두고 만들어 두었지만 사용되지 않음. 
- 유지 보수를 어렵게 만듬.

Unused configuration options, over-engineered abstractions, unused code paths, and unnecessary extensibility.

</details>

<details><summary>Accidental complexity</summary>

> 부수적인 복잡성

부수적인 복잡성은 '맨먼스 미신(The Mythical Man-Month)' 서적에서 등장하는 단어.

`"어떤 것이 복잡하다고 느껴지는 이유는 그것이 복잡하기 때문이 아니라, 우리가 그것을 이해하지 못하기 때문이다. 따라서 복잡성을 제거하는 방법은 어떻게든 이해할 수 있도록 만드는 것이다. 이해할 수 없는 것을 단순화하면, 부수적인 복잡성(accidental complexity)이 줄어든다.`

즉, 복잡성의 원인은 사람들이 어떤 것을 이해하기 어렵기 때문.
- 이를 해결하기 위해 → 복잡성 줄이기

#### 복잡도의 이유
- **적절하게 추상화 되지 않음**
- **충분한 문서화의 부재**
  - 부족한 설명과 충분하지 못한 설명으로 인해 복잡성 증가. 
- **복잡한 시스템 구조**
  - 전체적인 시스템 구조 파악이 어려워지면 이해하기 어려운 코드가 작성됨. 
- **불분명한 요구사항**
  - 요구사항이 명확치 않아 개발자들이 이해가 부족한 채로 작성한 코드는 복잡함을 초래. 
- **Legacy Code**
  - 주기적인 refactoring 또는 새 코드 작성이 야기됨.

</details>

<details><summary>구현과 측정</summary>

`"아키텍처는 구현과 측정을 통해 증명해야 하는 가설이다."`

*소프트웨어 아키텍처*는 시스템의 전반적인 설계와 구조를 결정하므로 시스템의 성능, 안정성, 확장성 등을 결정짓는 매우 중요한 역할을 한다.

#### 잘못 설계된 아키텍처
잘못 설계된 아키텍처는 시스템의 문제점과 결함을 발생시킨다.
- 시스템 성능 ↓  
- 유지보수의 어려움 ↑ 
- 개발비용 ↑

#### 구현과 측정
아키텍처가 올바르게 설계되었는지 알기 위해서는 구현과 측정을 해야 함.
- 시스템의 요구사항을 충족시키는지?
- 성능과 안정성이 적절한지?

**구현(Implementation):**
- 아키텍처 설계를 바탕으로, 소프트웨어 시스템의 실제 코드를 작성하는 과정. 
- 설계의 유효성과 적절성을 확인 가능. 

**측정(Measurement):** 
- 아키텍처 설계의 유효성을 검증하는 과정입니다. 
- 성능, 안정성, 확장성 등을 측정.
- 측정법:
  - 성능 측정:
    - 처리 속도, 응답 시간, 처리량 etc.
      - 측정 도구 예) `Apache Bench`, `JMeter` 
  - 코드 품질 검사
  - 테스트:
    - 기능 검증
    - type of tests: unit, integration, system, acceptance, regression, performance, and security.
    - 테스트 도구 예) `pytest`, `JUnit`, `Selenium`
  - 로그 분석
    - 시스템 동작을 이해
    - 문제 파악
    - 로그 분석 도구 예) `Datadog`, `Splunk`, `ELK Stack`, `Graylog`, `Loggly`.
  - 코드 복잡도 분석
    - 복잡도 분석 도구 예) `CodeClimate`, `SonarQube`

</details>

다양한 시스템이 비슷한 아키텍처 규칙을 공유하는 이유 → 소프트웨어 아키텍처의 규칙은 다른 모든 변수에 독립적
- `다른 모든 변수에 독립적`: 아키텍처 규칙은 시스템을 구성하는 기술, 인력, 일정, 예산 등과는 관계없이 동일하게 적용될 수 있다는 것.

소프트웨어 아키텍처 규칙이란 프로그램의 구성요소를 정렬하고 조립하는 방법에 관한 규칙이다.

----
# 1
## 설계와 아키텍처란?

<details><summary>설계와 아키텍처</summary>

> 둘 사이에는 아무런 차이가 없다.

설계(design):
- 저수준의 구조, 결정사항 등.

아키텍처(architecture):
- 저수준의 세부사항과 고수준의 무언가를 가리킬 때 흔히 사용.
  - 저수준(low-level): 
    - 메서드의 구현, 데이터 베이스 스키마 etc.
    - 구체적이고 구현적인 세부 사항.
  - 고수준(high-level):
    - 아키텍처, 구조, 비즈니스 로직 etc.
    - 저수준의 구성요소들을 조합하여 만들어지는 추상화된 개념.

저수준의 세부사항과 고수준의 구조는 모두 소프트웨어 전체 설계의 구성 요소.

</details>

<details><summary>좋은 설계</summary>

> 비용은 최소화, 생산성은 최대화.

좋은 설계는 비용을 절감시켜 준다. 
- 기능 만들기 또는 유지보수하는 데 투입되는 인력이 최소화 됨.
- 고객의 요구사항을 탑재한 기능을 만들어 내는 비용이 절감됨.

좋은 설계는 단순 명료함.

`빨리 가는 유일한 방법은 제대로 가는 것.`
- TDD 를 적용한 날이 그렇지 않은 날 보다 되려 작업속도가 향상.
- 속도만을 생각하여 빠르게 작성한 지저분한 코드는 생산성을 갈수록 저하 시킴.
  - 이전에 작성한 코드를 정리하는 일은 거의 발생 X
    - 다음 업무를 진행해야 하기에... 

</details>

----
# 2
## 두가지 가치에 대한 이야기 
모든 소프트웨어 시스템은 두 가지 가치를 제공한다, 행위와 구조. 이 두가지를 함께 잘 유지시켜야 한다.
- 어느 하나도 배제되어서는 안됨.

<details><summary>행위</summary>

> Behavior

**행위**: 시스템이 수행하는 작업이나 기능을 의미.

- 시스템이 어떤 input 을 받아서 그에 따라 어떤 output 을 생성하는지
- 그 과정에서 어떤 상호작용을 하는지를 나타낸다.

`아키텍처에서 행위는 구성 요소들 간의 상호작용과 함께 고려된다.`
- 구성요소(Component): 시스템을 구성하는 개별적인 module or block.
  - 구성요소는 실현 가능한 코드로 구현된다. 

</details>

<details><summary>구조</summary>

> Structure

구조는 변경하기 쉬워야 한다. Software 라는 단어가 내포한 의미처럼.

변경하기 쉬운 구조를 위해서는 Architecture 의 형태가 독립적이어야 한다.
- `아키텍처가 특정 현태를 다른 형태보다 선호하면하수록, 새로운 기능을 이 구조에 맞추는게 더 힘들어진다.`

수정의 용이성은 비용과 직접적인 연관성을 갖는다.
- **수정이 용이 O:** 비용 ↓  
- **수정이 용이 X:** 비용 ↑ 
  - 수정이 현실적으로 불가능한 시스템: 변경에 드는 비용 > 변경으로 인해 창출되는 수익 

</details>