**CloudWatch와 EC2의 연결**

AWS는 EC2 인스턴스에 여러 지표를 제공하며, 이러한 지표는 AWS에서 자동으로 전송됩니다. EC2와 CloudWatch의 연결은 시험에서도 매우 중요한 개념입니다.

<br>

## **1. 기본 및 상세 모니터링**
- **기본 모니터링**: 5분 간격으로 지표를 수집합니다.
- **상세 모니터링**: 1분 간격으로 지표를 수집하며, 이를 활성화하면 추가 비용이 발생합니다.

<br>

## **2. EC2에서 제공하는 기본 지표**
CloudWatch는 다음 네 가지 기본 지표를 제공합니다.
1. **CPU 사용률**
2. **네트워크 트래픽** (유입 및 유출량)
3. **디스크 I/O** (읽기 및 쓰기)
4. **상태 검사 (Status Check)**
   - 인스턴스 상태 확인 (Instance Status)
   - 시스템 상태 확인 (System Status)
   - 연결된 EBS 볼륨의 상태 확인

이 네 가지 항목은 반드시 기억해야 합니다.

<br>

## **3. 사용자 지정 지표**
- AWS 기본 제공 지표 외에도, 사용자가 직접 CloudWatch에 지표를 전송할 수 있습니다.
- 기본 해상도는 **1분 간격**이며, 고해상도 모니터링을 사용하면 **최대 1초 단위**까지 가능합니다.
- 대표적인 사용자 지정 지표: **RAM 사용량, 애플리케이션 레벨 지표**
- 사용자 지정 지표를 CloudWatch에 전송하려면 **IAM 역할을 설정**해야 합니다.

<br>

## **4. EC2 주요 모니터링 지표**
- **CPU 사용률**: EC2 인스턴스의 CPU 사용량을 나타냅니다.
  - T2, T3 버스트 가능 인스턴스의 경우 **CPU 크레딧 사용량 및 잔액**을 확인할 수 있습니다.
- **네트워크 트래픽**: 인스턴스의 네트워크 입출력 데이터 확인 가능.
- **상태 검사 (Status Check)**: 인스턴스의 하드웨어 및 소프트웨어 상태를 확인.
  - Amazon이 수행하는 상태 검사로, 사용자가 직접 제어할 수 없습니다.
- **디스크 관련 지표**:
  - EBS 볼륨이 연결된 경우 디스크 읽기/쓰기 지표는 EBS에서 확인해야 합니다.
  - 인스턴스 스토어 기반 EC2 인스턴스는 디스크 I/O 데이터를 확인할 수 있습니다.
- **RAM 사용량**:
  - CloudWatch에서는 기본적으로 RAM 사용량을 제공하지 않습니다.
  - 사용자 지정 지표로 직접 CloudWatch에 전송해야 합니다.

<br>

## **5. CloudWatch 대시보드 설정**
1. **EC2 인스턴스의 모니터링 탭**으로 이동합니다.
2. 원하는 지표를 **CloudWatch 대시보드에 추가**합니다.
3. 새 대시보드를 생성하고, 예를 들어 "EC2 지표"라고 이름을 지정합니다.
4. "Add to Dashboard"를 클릭하여 EC2 인스턴스의 모니터링 정보를 확인할 수 있습니다.

<br>

## **6. CPU 크레딧 (T2, T3 인스턴스)**
- Burstable 인스턴스의 경우, **CPU 크레딧 사용량 및 잔액**을 확인할 수 있습니다.
- 인스턴스를 사용하지 않을 경우, **CPU 크레딧 잔액이 증가**합니다.

<br>

## **7. 상세 모니터링 활성화 방법**
1. EC2 인스턴스의 **모니터링 탭**으로 이동합니다.
2. **Manage Detailed Monitoring**을 클릭합니다.
3. 활성화하면 **1분 간격의 그래프**를 볼 수 있습니다.
   - 기본적으로 5분 간격으로 제공되지만, 상세 모니터링을 활성화하면 더 세밀한 모니터링이 가능합니다.

<br>

## **8. CloudWatch 대시보드 삭제**
모든 작업이 끝나면 **CloudWatch 대시보드를 삭제**하여 정리할 수 있습니다.