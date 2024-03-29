# Software Engineering at Google

## Table of Contents

|Chapter|       Title      | Page  |   Date   |
|-------|:----------------:|:-----:|:--------:|
|01|소프웨어 엔지니어링이란?|||
|02|팀워크 이끌어내기|||
|03|지식 공유|||
|04|공정 사회를 위한 엔지니어링|||
|05|팀 이끌기|||
|06|성장하는 조직 이끌기|||
|07|엔지니어링 생산성 측정하기|||
|08|스타일 가이드와 규칙|||
|09|코드 리뷰|||
|10|문서자료|||
|11|테스트 개요|||
|[12](#12)|단위 테스트|312-342|01.15.2023|
|13|테스트 대역|||
|14|더 큰 테스트|||
|15|폐기|||
|16|버전 관리와 브랜치 관리|||
|17|Code Search|||
|18|빌드 시스템과 빌드 철학|||
|19|Critique: 구글의 코드 리뷰 도구|||
|20|정적 분석|||
|21|의존성 관리|||
|22|대규모 변경|||
|23|지속적 통합|||
|24|지속적 배포|||
|25|서비스형 컴퓨트|||

# 12
## 단위 테스트
> 구글: 단위 테스트 80%, 범위가 더 넓은 테스트 20%

<details><summary>테스트 목적</summary>

- 버그 예방
- 엔지니어의 생산성 개선  
  생산성을 끌어 올리는 특성:
  - 단위 테스트 == 작은 테스트  
    빠르고  결정적이어서 개발자들이 수시로 수행 → 빠른 피드백

  - 테스트 커버리지를 높이기 좋음  
    빠르게 작성 가능하기에

  - 실패 시 원인을 파악하기 쉬움  
    개념적으로 간단하고 시스템의 특정 부분에 집중하므로

  - 문서자료 / 예제 코드의 역할  
    시스템의 사용법과 의도한 동작 방식을 알려줌
  
</details>

<details><summary>유지보수하기 쉬워야 한다</summary>

- 깨지기 쉬운 코드 X  
  자세한 내용은 아래 항목(`깨지기 쉬운 테스트 예방하기`) 참고

- 명확해야 한다.
  - 무엇이 잘못되어 실패했는지, 어떻게 고쳐야 하는지를 파악하기 어려운 불명확한 테스트는 유지보수하기 어려움.

</details>

<details><summary>깨지기 쉬운 테스트 예방하기</summary>

> 깨지기 쉬운 테스트: 실제로는 버그가 없어도, 검증 대상 코드와는 관련조차 없는 변경 때문에 실패하는 테스트

깨지기 쉬운 테스트는 유지보수를 힘들게 한다.
- 코드가 변경될 때마다 엔지니어가 수동으로 테스트를 손봐줘야하기에.

### [이상적인 테스트]

- 한 번 작성한 후로는 대상 시스템의 요구사항이 바뀌지 않는 한 절대 수정할 일이 없어야 한다.

### [코드 변경의 기본적인 유형]

1. 순수 리팩토링
    > 기존 테스트 변경 (X)

    > 테스트의 역할: 리팩토링 후에도 시스템의 행위가 달라지지 않았음을 보장하는 것.

    - 성능 최적화
    - 코드 가독성 개선  

    <br />

    만약 테스트를 변경해야한다면:
    - 시스템의 행위가 달라졌다는 반증.
    - 테스트의 추상화 수준이 적절하지 않았음.
      - 테스트가 대상의 세부 구현 방식에 지나치게 의존하고 있다는 것.

1. 새로운 기능 추가
    > 기존 테스트 변경 (X)

    새 기능이나 행위를 추가할 때는 기존 행위들 및 테스트에 영향을 주지 않아야 한다.

1. 버그 수정
    > 기존 테스트 변경 (X)

    버그 수정은 기능 추가와 비슷. 기존 테스트들은 변경되지 않아야 함.

    버그가 존재한다는 것은 기존 테스트 스위트에 빠진 게 있다는 신호.
    - 버그 수정과 동시에 누락됐던 테스트를 추가해야 함.

1. 행위 변경
    > 기존 테스트 변경 (O)

    시스템의 기존 행위를 변경하는 경우 → 기존 테스트 역시 변경되어야 함.

    비용이 더 드는 변경.


### [공개 API 를 이용해 테스트하자]
> 이유: 다른 사용자 코드와 똑같은 방식으로 테스트 호출하기 → Open API (내부 구현을 위한 코드가 아닌)

- 테스트가 실패하면 사용자도 똑같은 문제를 겪는다.
- 테스트가 사용자에게 유용한 예제 코드와 문서자료가 되어준다.

Open API:
- 코드 소유자가 3rd Party 에 노출한 API
  - 소유자의 통제 없이 누구든 접근할 수 있게 설계된 패키지나 클래스라면 직접 테스트해야 한다.
  - 소유자만이 접근할 수 있지만 다방면으로 유용한 기능을 제공하도록 설계된 패키지나 클래스(e.g. 지원 라이브러리) 라면 직접 테스트해야 한다.

### [상호작용이 아니라 상태를 테스트하자]
시스템이 기대한 대로 동작하는지 검증하는 방법:
1. State Test (상태 테스트)
    메서드 호출 후 시스템 자체를 관찰.

1. Interaction Test (상호작용 테스트)
    호출을 처리하는 과정에서 시스템이 다른 모듈(시스템)들과 협력하여 기대한 일련의 동작을 수행하는 지를 확인.

    깨지기 쉬운 테스트가 만들어 지기 쉬움:
    - 모의 객체 프레임워크 Mocking Framework 에 지나치게 의존하기 때문.
      - 진짜 객체가 빠르고 결정적이라면 테스트 대역을 지양하고 진짜 객체를 사용해야 한다.

### [명확한 테스트 작성하기]
> 명확한 테스트: 존재 이유와 실패 원인을 엔지니어 바로 알 수 있는 테스트

테스트가 실패하는 이유:
- 테스트가 실패하면 두 이유중 어느 것인지 파악해야 함.
  1. 대상 시스템에 문제가 있거나 불완전하다.
      - 테스트는 이 문제를 잡아낼 목적으로 설계된 것.
      - 실패이유가 이런 이유라면 버그를 고치라는 경고라고 보자.
  2. 테스트 자체에 결함이 있다.
      - 깨지기 쉬운 코드라는 뜻 (이를 완벽 제거하기란 불가능)

### [완전하고 간결하게 만들자]
> Complete Test (완전한 테스트): 결과에 도달하기까지의 논리를 읽는 이가 이해하는 데 필요한 모든 정보를 본문에 담고 있는 테스트

> Concise Test (간결한 테스트): 코드가 산만하지 않고, 관련 없는 정보는 포함하지 않은 테스트

테스트를 더 명확하게 만들 수 있다면 DRY(Don’t Repeat Yourself) 원칙을 거스르는게 낫다.
- 테스트 본문에는 테스트를 이해하는 데 필요한 정보를 모두 담아야 한다.
- 반면, 눈을 어지럽히거나 관련 없는 정보는 담지 않아야 한다.

### [메서드가 아니라 행위를 테스트하자]
테스트 구조 ≠ 대상 코드의 구조

- 테스트 구조를 대상 코드와 일치하게 구성하려 하면 안된다.
  - 대상 메서드가 복잡해질 수록 테스트도 함께 복잡해짐 → 실패 원인 파악 어려움.

테스트를 행위별로 작성하자:
> Behavior (행위): 특정한 일련의 입력을 받았을 때 시스테이 보장하는 ‘반응’

- 행위 주도 테스트가 메서드 중심 테스트보다 명확한 이유:
    1. 자연어에 더 가깝다.  
      이해하기 더 쉬움.

    1. 더 좁은 범위를 검사한다.  
        원인과 결과가 분명히 드러남.

    1. 테스트가 짧고 서술적이다.  
        검사한 기능이 무엇인지 쉽게 확인 가능.

테스트의 구조는 행위가 부각되도록 구성하자:
- 행위는 세가지 구조로 구성:
  - `Given`: 시스템 설정
  - `When`: 시스템이 수행할 작업
  - `Then`: 결과를 검증

복잡한 테스트라면 명확성을 위해 주석을 달아주는 것도 좋다.

테스트를 작성할 때는 한 행위만 다뤄야 한다:
- 한 테스트에 여러 행위를 검사하지 않도록 해야함.

테스트 이름은 검사하는 행위에 어울리게 짓자:
- 테스트 명칭
  - 메서드 중심 테스트 명칭:
    - 대상 메서드의 이름을 따서 지음
        - 대상 메서드: `updateBalance`
        - 테스트: `testUpdateBalance`
  - 행위 중심 테스트 명칭:
    - 대상 클래스: `BankAccount`
    - 테스트: `shouldNotAllowWithdrawalsWhenBalanceIsEmpty`
- 테스트의 이름은 검사하려는 행위를 요약해 보여주어야 한다.
    - 보통 실패 보고서에 테스트 이름만 표시되는 경우도 많기 때문
      - 실패 단서는 이름 뿐…

### [테스트에 논리를 넣지 말자]
테스트가 명확하면 검토하기도 쉽다. 테스트를 검증하는 테스트를 작성해야 할 것 같다면 이는 잘못된 것.
- 복잡한 테스트:
  - 복잡성은 대체로 ‘논리 logic’ 라는 형태로 나타난다.
    - 논리: 명령형 요소(연산자, 반복문, 조건문 etc) 를 이용해 표현됨.

테스트 코드에서는 스마트한 로직보다 직설적인 코드를 고집해야 한다.

중복은 서술적이고 의미 있는 테스트를 작성하기 위해 허용된다.

### [실패 메시지를 명확하게 작성하자]
잘 작성된 실패 메시지는 테스트의 이름과 거의 동일한 정보를 담고 있어야 한다.
- 좋은 라이브러리를 사용하면 실패 메시지를 쓸모 있게 작성하기가 더 쉬워 진다.

</details>

<details><summary>DRY가 아니라 DAMP!</summary>

> DRY: Don’t Repeat Yourself

> DAMP: Descriptive And Meaningful Phrase

테스트 코드에서는 DRY가 주는 혜택이 크지 않다. 테스트는 DAMP 화 해야한다. 서술적이고 의미있게 작성하도록 해야 함.
- DRY
  - 장점: 유지보수 하기 쉬움.
  - 단점: 참조에 참조를 따라가야 함 → 명확성 떨어짐.

테스트의 리팩토링:
- 반복을 줄이는 것이 리팩토링이 아님.
- 서술적이고 의미 있게 개선해야함.

</details>
