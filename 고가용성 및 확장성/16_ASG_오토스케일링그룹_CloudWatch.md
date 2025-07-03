## ASG 관련 CloudWatch 지표 정리

Auto Scaling Group(ASG)을 운영하면서 모니터링을 위한 중요한 지표들이 있습니다. 이들은 EC2 인스턴스 수준과 ASG 그룹 수준으로 나눠집니다.

---

### 1. **ASG 그룹 수준(Group-level) 지표**

이 지표들은 **ASG 전체의 상태**를 보여줍니다.
기본적으로는 비활성화되어 있으며, **명시적으로 활성화**해야 볼 수 있습니다.

#### 주요 지표

| 지표 이름                         | 설명                              |
| ----------------------------- | ------------------------------- |
| **GroupMinSize**              | 설정된 최소 인스턴스 수                   |
| **GroupMaxSize**              | 설정된 최대 인스턴스 수                   |
| **GroupDesiredCapacity**      | 현재 희망 인스턴스 수 (Desired Capacity) |
| **GroupInServiceInstances**   | 서비스 중인 인스턴스 수                   |
| **GroupPendingInstances**     | 시작 대기 중인 인스턴스 수                 |
| **GroupStandbyInstances**     | 스탠바이 상태의 인스턴스 수                 |
| **GroupTerminatingInstances** | 종료 중인 인스턴스 수                    |
| **GroupTotalInstances**       | ASG에 포함된 전체 인스턴스 수              |

> **주의**: 위 지표들은 ASG에서 **그룹 수준의 메트릭 수집(Metric Collection)** 을 활성화해야 볼 수 있습니다.

활성화 방법:

* 콘솔에서 ASG 선택 → **Monitoring** 탭 → **Enable Group Metrics Collection**

---

### 2. **EC2 인스턴스 수준 지표**

ASG에서 생성된 EC2 인스턴스 각각에 대해 **기본적으로 활성화**되어 있습니다.

#### 주요 지표

| 지표 이름                      | 설명                      |
| -------------------------- | ----------------------- |
| **CPUUtilization**         | CPU 사용률                 |
| **NetworkIn**              | 수신된 네트워크 트래픽 (Bytes)    |
| **NetworkOut**             | 송신된 네트워크 트래픽 (Bytes)    |
| **DiskReads / DiskWrites** | 디스크 읽기/쓰기 I/O           |
| **StatusCheckFailed**      | 시스템 또는 인스턴스 상태 확인 실패 여부 |

#### 모니터링 주기

| 모니터링 수준 | 주기 | 설명                          |
| ------- | -- | --------------------------- |
| 기본 모니터링 | 5분 | 기본적으로 활성화됨                  |
| 상세 모니터링 | 1분 | **명시적으로 활성화해야 함**, 추가 비용 발생 |

---

### 3. **활용 팁**

* ASG의 **Scaling 정책**, **알람 설정**, **운영 트러블슈팅**을 위해 이 지표들을 반드시 확인해야 합니다.
* 특히 `GroupDesiredCapacity`, `GroupInServiceInstances`, `CPUUtilization`은 **스케일링 정책 설계**에 핵심적입니다.
* `NetworkIn/Out`은 **트래픽 급증 감지**, `StatusCheckFailed`는 **장애 대응**에 유용합니다.

---

### 4. 실습/모니터링 위치

* 콘솔: EC2 → Auto Scaling Groups → 대상 그룹 선택 → **Monitoring 탭**
* EC2 탭: 개별 인스턴스 수준 지표 확인 가능

---

## 요약

* **ASG-level 지표**는 직접 활성화 필요, 인스턴스 상태 변화 파악에 유리
* **EC2-level 지표**는 기본 제공, 성능 및 리소스 사용량 감시용
* **CloudWatch Alarms**과 함께 사용 시 **자동 확장/축소의 트리거로 활용 가능**