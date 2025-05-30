## 로드 밸런서 오류 코드

로드 밸런서를 사용할 때 발생할 수 있는 오류 코드는 주로 **HTTP 응답 코드** 로 표현되며,
**클라이언트 오류(4XX)** 와 **서버 오류(5XX)** 로 구분됩니다.

### 정상 응답

* **200**: OK (정상 요청 처리)

### 클라이언트 측 오류 (4XX)

| 코드  | 설명                      |
| --- | ----------------------- |
| 400 | Bad Request: 잘못된 요청 형식  |
| 401 | Unauthorized: 인증되지 않음   |
| 403 | Forbidden: 접근 금지        |
| 460 | 클라이언트가 연결을 강제로 종료       |
| 463 | `X-Forwarded-For` 헤더 오류 |

> 클라이언트 쪽 문제로 간주됩니다.

### 서버 측 오류 (5XX)

| 코드  | 설명                              |
| --- | ------------------------------- |
| 500 | Internal Server Error: 서버 내부 문제 |
| 502 | Bad Gateway: 게이트웨이 잘못 연결        |
| 503 | Service Unavailable: 정상 인스턴스 없음 |
| 504 | Gateway Timeout: 응답 지연으로 타임아웃   |
| 561 | Unauthorized (서버 인증 실패)         |

> 서버, 로드 밸런서, 또는 백엔드 인스턴스 문제를 의미합니다.

<br>

## 주요 CloudWatch 모니터링 지표

AWS에서는 로드 밸런서 관련 주요 지표를 **Amazon CloudWatch**를 통해 모니터링할 수 있습니다.

### 상태 관련

| 지표                   | 설명            |
| -------------------- | ------------- |
| `HealthyHostCount`   | 정상 대상 인스턴스 수  |
| `UnHealthyHostCount` | 비정상 대상 인스턴스 수 |

### 요청 수 및 응답

| 지표                                   | 설명                     |
| ------------------------------------ | ---------------------- |
| `RequestCount`                       | 총 요청 수                 |
| `RequestCountPerTarget`              | 대상당 평균 요청 수            |
| `TargetResponseTime`                 | 백엔드 응답 지연 시간 (Latency) |
| `HTTPCode_ELB_4XX/5XX/2XX/3XX_Count` | 오류 유형별 응답 수            |

### 트래픽 대기열

| 지표                 | 설명                               |
| ------------------ | -------------------------------- |
| `SurgeQueueLength` | 연결 대기 중인 요청 수 (최대 1024)          |
| `SpilloverCount`   | 대기열 초과로 거절된 요청 수 (**0 이상이면 문제**) |

### ⚙️ 용량 및 성능

| 지표                      | 설명                                       |
| ----------------------- | ---------------------------------------- |
| `ActiveConnectionCount` | 현재 활성 연결 수                               |
| `ConsumedLCUs`          | 소비된 Load Balancer Capacity Unit (비용과 직결) |

> 이 지표들은 Auto Scaling, 장애 감지, 트래픽 최적화에 모두 유용하게 사용됩니다.

<br>

## 문제 해결 (Troubleshooting) 예시

| 증상          | 오류 코드            | 대응 방법                          |
| ----------- | ---------------- | ------------------------------ |
| 클라이언트 요청 실패 | 400              | 요청 포맷, 헤더 점검                   |
| 인증 안 됨      | 401              | IAM 설정, 인증 토큰 확인               |
| 백엔드 응답 없음   | 503              | `HealthyHostCount` 확인          |
| 백엔드 응답 지연   | 504              | EC2의 `keep-alive` 설정 및 타임아웃 확인 |
| 요청 거절       | SpilloverCount ↑ | Auto Scaling 확장 필요             |

<br>

## 액세스 로그

로드 밸런서의 **액세스 로그**는 S3에 저장되며,
**요청별 상세 메타데이터**를 기록하여 **디버깅, 규정 준수, 감사** 목적으로 활용됩니다.

### 포함 정보

* 요청 시간
* 클라이언트 IP
* 경로 및 응답 코드
* 지연 시간
* 추적 ID (`X-Amzn-Trace-Id`)

> 로그는 **암호화** 가능하며, **S3 스토리지 비용**만 지불

### 활성화 방법

1. 로드 밸런서의 **Attributes** 탭 이동
2. **Access Logs** 활성화
3. 로그 저장할 **S3 버킷 및 경로(Prefix)** 지정

### 분석 도구

* **Amazon Athena**: S3에 저장된 로그를 SQL로 분석
* **CloudWatch Logs** 또는 **SIEM 도구** 연동 가능

<br>

## 요청 추적 (Request Tracing)

ALB는 **`X-Amzn-Trace-Id`** 헤더를 통해 요청을 추적할 수 있도록 지원합니다.

* **각 요청에 고유 추적 ID 포함**
* 로그나 분산 추적 시스템에서 사용 가능
* 현재 **AWS X-Ray와는 직접 연동되지 않음** (추후 지원 가능)

> 동일한 Trace ID로 전체 요청 흐름을 추적해 문제 파악 가능

<br>

## 정리

| 항목                     | 설명                                 |
| ---------------------- | ---------------------------------- |
| **4XX 오류**             | 클라이언트 측 문제 (요청 형식, 인증 등)           |
| **5XX 오류**             | 서버/백엔드 문제 (연결 실패, 타임아웃 등)          |
| **CloudWatch 지표**      | 상태, 응답, 대기열, 연결, 소비량 등 다양하게 제공     |
| **SpilloverCount > 0** | 요청 손실 → 스케일링 필요                    |
| **Access Logs**        | S3에 저장, 분석 도구와 연계 가능               |
| **Trace ID**           | 요청 식별 및 흐름 추적 가능 (X-Amzn-Trace-Id) |
