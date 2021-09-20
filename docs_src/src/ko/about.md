# About

[세마포(Semaphore)](https://github.com/appliedzkp/semaphore) 는 이더리움 사용자들이 자신들의 신원을 드러내지 않고 이전에 참여했던 집합에 대한 멤버십을 허용하는 영지식 도구이다.
동시에, 세마포는 사용자들이 그들의 지지를 임의의 문자열로 신호하도록 허용한다. 
세마포는 이더리움 dApp에서 간단하고 일반적인 프라이버시 계층으로 설계되었다.
사용 사례는 익명 투표, 익명 신고(whistleblowing), 믹서(mixers), 그리고 익명 인증 등이 있다.
마지막으로, 세마포는 이중 신호나 이중 소비를 예방하는 간단한 내부 메커니즘을 지원한다.

이 도구는 스마트 컨트랙트와 [영지식(zero-knowledge)](https://z.cash/technology/zksnarks/) 컴포넌트가 서로 도와가며 구성을 이룬다.
세마포 스마트 컨트랙트는 상태, 허가, 그리고 체인 내 증명 확인을 다룬다. 
영지식 컴포넌트는 사용자가 증명을 생성하여 유효한 증명일 때 스마트 컨트랙트가 스스로 상태를 업데이트 할 수 있게 하기 위해 체인 없이 동작한다.

세마포의 공식적인 설명과 세마포의 근본적인 암호학 메커니즘을 알고 싶다면, [여기](https://github.com/appliedzkp/semaphore/tree/master/spec) 에서 문서를 확인하라.

세마포는 스마트 컨트랙트와 dApp 개발자들을 위해 설계되었으며, 최종 사용자를 위해 만들어진 것은 아니다.
개발자는 사용자 친화적인 익명성을 제공하기 위해서는 이러한 특성들을 추상화하는 것을 권장한다.

간단한 [데모](https://weijiekoh.github.io/semaphore-ui/) 를 시도해 보거나 세마포에 대한 높은 수준의 세마포 [설명](https://medium.com/coinmonks/to-mixers-and-beyond-presenting-semaphore-a-privacy-gadget-built-on-ethereum-4c8b00857c9b) 을 읽어보자.

## 기본적인 특징들

요약하면, 세마포는 다음과 같은 능력을 제공한다.

1. 스마트 컨트랙트에 신원을 등록한다. 그리고 나서

2. 신호를 알린다. :

    - 그들의 신원이 등록된 신원의 집합에 있다는 것을 익명으로 증명한다.
    - 만약 문자열이 사용자마다 고유의 값이고 이 값이 컨트랙트의 현재 외부 널리파이어(nullifier, 주제와 유사한 고유값)라면, 공개적으로 임의의 문자열을 컨트랙트에 저장한다. 이는 같은 외부 널리파이어 하에서 같은 메시지를 이중 신호 보내는 것이 불가능하다는 것을 의미한다.

### 외부 널리파이어에 대하여

외부 널리파이어를 사용자가 하나의 표를 행사하는 투표 부스에서 생각해보자. 만약 그들이 같은 부스에서 두 번째 표를 행사하려고 시도한다면, 그 표는 무효가 될 것이다.

외부 널리파이어는 29 바이트의 어떠한 값이다. 세마포는 항상 하나의 외부 널리파이어에서 시작하는데, 이 널리파이어는 컨트랙트 배포에 의해 없어질 수 있다. 세마포 컨트랙트의 주인은 추가적인 널리파이어를 더하고, 비활성화 시키고, 존재하는 것들을 재활성화 시켜야 한다.

특정 유저가 처음 신호를 송출하여 활성상태의 외부 널리파이어 n이 되고, 그 유저가 등록된 유저 그룹에 속한다는 멤버십 증명이 유효하면, 트랜잭션은 성공한다. 두 번째 유저가 동일하게 같은 n으로 시도하면, 그 유저의 트랜잭션은 실패한다.

추가적으로, 비활성화된 외부 널리파이어로 송출하는 모든 신호들은 실패한다.

각각의 클라이언트 어플리케이션은 반드시 위의 세마포 특징을 독창적인 방법으로 사용하여야 하며 이로 인해 프라이버시 목표를 이룰 수 있다. 예를 들어 믹서(mixer)는 하나의 외부 널리파이어를 다음과 같이 사용한다.

| 신호 | 외부 널리파이어 |
|-|-|
| 수신자 주소, 스위처(relayer) 주소, 그리고 스위처 요금에 대한 해시 | 믹서 컨트랙트의 주소 |

이는 (요금을 보상받는 트랜잭션 스위처를 통한) 펀드의 익명 철회를 허용하며, 오직 하나의 외부 널리파이어가 있기에 이중 소비를 예방한다.

익명 투표 앱은 다르게 구성된다.

| 신호 | 외부 널리파이어 |
|-|-|
| 응답자 대답의 해시 | 질문의 해시 |

이는 어떠한 사용자가 어떠한 질문에 대해 임의의 대답으로 투표를 할 수 있게 허용한다. 그러나 사용자는 하나의 질문당 한 번만 투표할 수 있다.

## 코드에 관하여

이 레포지토리는 세마포 컨트랙트에 대한 Solidity 언어로 쓰여진 코드를 포함하고 있다. 그리고 [circom](https://github.com/iden3/circom)으로 쓰여진 zk-SNARK 회로 코드도 포함하고 있다. 또한 테스트 실행을 위한 타입스크립트 코드도 포함하고 있다.

이 코드는 ABDK 컨설팅에 의해 검수를 받았다. 그들이 제안한 보안 및 효율성 수정 사항은 반영되었다.

zk-SNARK 증명과 세마포 검증 키들을 생산하기 위한 여러 파티의 연산은 곧 시작이 될 것이다.