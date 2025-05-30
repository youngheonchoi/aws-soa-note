## Network Load Balancer (NLB)

**Network Load Balancer (NLB)** 는 **OSI 4계층(TCP/UDP)** 수준에서 작동하는 고성능 로드 밸런서입니다. **초고속 처리**, **짧은 지연 시간**, 그리고 **고정 IP 지원**이 필요한 트래픽 분산 환경에 적합합니다.

<br>

## 주요 특징
- **4계층 로드 밸런서**: TCP, UDP, TLS 지원
- **초당 수백만 건의 요청** 처리 가능
- **지연 시간이 매우 짧음** (low latency)
- **가용 영역별 고정 IP** 지원 (탄력적 IP 할당 가능)
- **정적 IP 필요 시 필수 선택지**
- **AWS 프리 티어에는 포함되지 않음**

> 시험 문제에서 "UDP", "고정 IP", "초고성능" 키워드가 나오면 NLB를 연상하세요.

<br>

## 구조 및 작동 방식
- 기본 구조는 ALB와 유사하게 **대상 그룹(Target Group)** 으로 트래픽을 전달합니다.
- **프론트엔드**: TCP/UDP로 요청을 수신
- **백엔드**: EC2 인스턴스, IP 주소 대상 그룹으로 트래픽 전송

### 대상 그룹 유형
| 대상 그룹 | 설명 |
|-----------|------|
| **EC2 인스턴스** | TCP/UDP를 수신할 백엔드 서버 |
| **IP 주소** | 반드시 **사설 IP**이어야 하며, 온프레미스 서버도 가능 |

> IP 대상 그룹을 이용하면 **하이브리드 아키텍처**도 구축 가능

<br>

## 고정 IP 설정
- **각 가용 영역(AZ)당 하나의 고정 IP**를 NLB에 할당할 수 있음
- **탄력적 IP(Elastic IP)** 사용 가능
- IP 주소 제한이 있는 외부 시스템과 연동할 때 유용

### 예시
> “어떤 애플리케이션이 특정 IP들(예: 1.2.3.4, 5.6.7.8)로만 접근을 허용한다”  
→ NLB로 고정 IP 제공 필요

<br>

## 상태 확인 (Health Check)
NLB는 다음 세 가지 프로토콜로 **상태 확인(Health Check)** 을 수행할 수 있습니다:

| 프로토콜 | 설명 |
|----------|------|
| **TCP** | 기본 설정, 연결 가능 여부 확인 |
| **HTTP** | 지정된 포트/경로에 대해 상태 코드로 확인 |
| **HTTPS** | 보안 연결을 통한 상태 코드 확인 가능 |

> 백엔드 애플리케이션이 HTTP/HTTPS를 사용할 경우, 해당 프로토콜 기반 상태 검사 설정 가능

<br>

## NLB + ALB 조합 가능
- NLB를 **ALB 앞단에 배치** 가능
- 이유
  - **고정 IP가 필요**하지만
  - **HTTP 기반 라우팅 기능도 필요**한 경우

### 구성 예시
```plaintext
Client
  ↓
NLB (고정 IP 제공)
  ↓
ALB (경로 기반 라우팅)
  ↓
다수의 마이크로서비스로 분산
```

> 이 조합은 고정 IP 및 HTTP 레벨 트래픽 세분화 모두 필요한 환경에서 유효한 구성입니다.

<br>

## 정리
Network Load Balancer는 **고성능**, **저지연**, **정적 IP 제공**이 핵심인 AWS의 4계층 로드 밸런서입니다. TCP/UDP 기반의 대규모 트래픽 분산이 필요한 상황이나, 정적 IP 제한이 있는 보안 네트워크 환경에서 매우 유용합니다.  
**온프레미스 연동, 하이브리드 구성, 고정 IP 필요** 시 NLB를 고려하세요.