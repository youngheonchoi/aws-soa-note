## ALB 규칙 및 가중치 기반 라우팅 정리

### 규칙(Rules)이란?

ALB(Application Load Balancer)에서 **들어오는 요청을 어떻게 처리할지 정의하는 조건 및 작업의 집합**입니다.
각 규칙은 **대상 그룹, 리다이렉트, 고정 응답** 등 다양한 작업을 포함할 수 있으며,
**조건 기반 라우팅**을 구현할 수 있게 해줍니다.

> ALB는 규칙을 **순차적으로 평가**하며, 마지막 규칙은 항상 **기본(default)** 규칙입니다.

---

### 규칙의 구성 요소

| 구성 요소              | 설명                        |
| ------------------ | ------------------------- |
| **조건(Conditions)** | 요청이 어떤 상황일 때 규칙이 적용되는지 정의 |
| **작업(Actions)**    | 요청이 조건에 맞을 경우 수행할 작업 정의   |

#### 조건의 예시

* **Host 헤더** (예: `example.com`)
* **HTTP 메서드** (GET, POST 등)
* **경로 패턴(Path)** (`/myapp1`, `/api/v2`)
* **소스 IP** (예: 사내망 접근 제한)
* **HTTP 헤더** (예: `User-Agent`, `Authorization`)
* **쿼리 문자열 매개변수** (`?env=dev` 등)

#### ✔️ 작업의 예시

* **Forward**: 대상 그룹으로 트래픽 전달
* **Redirect**: 다른 URL로 리다이렉트 (예: HTTP → HTTPS)
* **Fixed Response**: 고정 응답 반환 (예: `503 Maintenance`)

---

### 규칙에 따른 라우팅 흐름 예시

```plaintext
1. 요청: GET example.com/myapp1
2. 조건: Host = example.com AND Path = /myapp1
3. 작업: 대상 그룹 A로 전달
```

---

## 가중치 기반 라우팅 (Weighted Target Group Routing)

ALB는 **하나의 규칙 안에 여러 대상 그룹(Target Group)** 을 설정하고,
**가중치(Weight)를 통해 트래픽 비율을 제어**할 수 있습니다.

### 사용 사례: 블루/그린 배포

| 대상 그룹        | 버전 | 가중치 | 트래픽 비율 |
| ------------ | -- | --- | ------ |
| 그룹 1 (Blue)  | v1 | 8   | 80%    |
| 그룹 2 (Green) | v2 | 2   | 20%    |

* ALB는 **단일 규칙** 내에서 이 설정을 통해 **점진적 배포, 테스트 롤아웃** 가능
* **CloudWatch 지표**로 새 버전 상태를 모니터링하여 문제가 없으면 가중치 증가 가능

> 완전 배포 전 **A/B 테스트 또는 카나리 배포**에 유용

---

## 정리

| 항목           | 설명                                             |
| ------------ | ---------------------------------------------- |
| **규칙 처리 방식** | 위에서 아래로 순차 평가, 마지막은 기본 규칙                      |
| **조건 예시**    | Host, Path, Method, Query, Header, Source IP 등 |
| **작업 예시**    | Forward, Redirect, Fixed Response              |
| **가중치 라우팅**  | 하나의 규칙에서 여러 대상 그룹에 비율로 트래픽 분산                  |
| **활용 사례**    | 블루/그린 배포, 점진적 릴리즈, A/B 테스트                     |
