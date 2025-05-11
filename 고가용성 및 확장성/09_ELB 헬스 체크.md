## 상태 확인 (Health Check)

**상태 확인**은 로드 밸런서가 **백엔드 인스턴스(대상)** 가 정상인지 여부를 판단하는 핵심 기능입니다.
애플리케이션 로드 밸런서(ALB) 또는 대상 그룹(Target Group) 수준에서 설정할 수 있으며,
정상 상태의 인스턴스에만 요청을 라우팅함으로써 **서비스 안정성**을 확보할 수 있습니다.

<br>

## 상태 확인 구성 요소

| 설정 항목                       | 설명                                      |
| --------------------------- | --------------------------------------- |
| **HealthCheckProtocol**     | 상태 확인에 사용할 프로토콜 (HTTP 또는 HTTPS)         |
| **HealthCheckPort**         | 인스턴스의 어떤 포트로 상태 확인을 수행할지 설정 (기본: 80)    |
| **HealthCheckPath**         | 상태 확인용 HTTP 경로 (예: `/health`)           |
| **HealthCheckTimeout**      | 상태 확인 요청이 실패로 간주되기 전까지 대기하는 시간 (기본: 5초) |
| **HealthCheckInterval**     | 상태 확인 요청 간격 (기본 추천: 30초)                |
| **HealthyThresholdCount**   | 정상으로 간주되기 위한 연속 성공 횟수                   |
| **UnhealthyThresholdCount** | 비정상으로 간주되기 위한 연속 실패 횟수                  |
| **Success Codes**           | 상태 확인 시 유효한 HTTP 응답 코드 (예: 200, 302 등)  |

<br>

## 상태 확인 예시 시나리오

* `/health` 경로로 80번 포트에 30초 간격으로 HTTP 요청 전송
* 응답 코드가 200이면 성공, 아니면 실패
* **3번 연속 성공 시 "정상(Healthy)"**, **2번 연속 실패 시 "비정상(Unhealthy)"**

<br>

## 대상 상태 종류

| 상태              | 설명                   |
| --------------- | -------------------- |
| **Initial**     | 초기 등록 상태, 상태 확인 진행 전 |
| **Healthy**     | 정상 대상, 요청 수신 가능      |
| **Unhealthy**   | 비정상 대상, 요청 전달되지 않음   |
| **Draining**    | 등록 취소 중, 기존 연결만 유지   |
| **Unused**      | 등록되지 않음              |
| **Unavailable** | 상태 확인 비활성화 또는 실패 시   |

> 대상 그룹 내 **모든 대상이 비정상**인 경우, ELB는 어쩔 수 없이 비정상 대상에게도 요청을 보낼 수 있습니다.
> → **정확한 상태 확인 구성**이 중요합니다.

<br>

## 고급 설정 팁

* **HTTP vs HTTPS**
  백엔드가 HTTPS만 수신하는 경우, 상태 확인 프로토콜도 반드시 HTTPS로 설정

* **타겟 포트 재정의**
  상태 확인은 트래픽과 다른 포트에서 수행할 수 있음 (예: 앱은 8080, 상태 확인은 8000)

* **Timeout & Interval**

  * **짧은 요청**: timeout 2초, interval 10초
  * **긴 작업**: timeout 10초 이상

* **Success Code 커스터마이징**

  * 기본은 `200`
  * `200-299`, `302` 등 범위 지정 가능

<br>

## 설정 위치 (ALB 기준)

1. **Target Group > Health Checks 탭 이동**
2. **편집(EDIT)** 클릭
3. 아래 항목들 수정 가능:

   * Protocol / Port / Path
   * Timeout / Interval
   * Healthy & Unhealthy Threshold
   * Success Code

> 모든 설정은 UI 뿐 아니라 **Terraform, CloudFormation, CLI**로도 자동화 가능

<br>

## 실습 예시 요약

* 대상 그룹에 연결된 인스턴스 3개
* 상태 확인 설정: `/health`, HTTP, 80포트
* 성공 코드: `200`
* 상태 확인 간격: 30초
* 결과: 세 인스턴스 모두 **Healthy** 상태

<br>

## 정리

| 항목               | 설명                                                 |
| ---------------- | -------------------------------------------------- |
| **목적**           | 비정상 인스턴스에 요청 전달 방지                                 |
| **위치**           | ALB, NLB, 대상 그룹 수준에서 설정                            |
| **핵심 설정**        | Path, Port, Protocol, Interval, Timeout, Threshold |
| **상태 유형**        | Initial, Healthy, Unhealthy, Draining, etc.        |
| **성공 코드 커스터마이징** | 다양한 HTTP 응답 코드 지원 가능                               |