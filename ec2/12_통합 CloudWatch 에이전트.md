## Unified CloudWatch Agent 사용법

### 1. 목적
Unified CloudWatch Agent는 EC2 인스턴스 및 온프레미스 서버에서 시스템 수준의 지표(메모리, 프로세스, 디스크 사용 등)와 로그를 수집하여 CloudWatch로 전송합니다.
   
### 2. 설치 및 설정
EC2 인스턴스를 시작하면 기본적으로 CloudWatch Logs로 파일이나 로그가 전송되지 않으므로, Unified CloudWatch Agent를 설치하고 구성해야 합니다. 

### 3. 구성 방법
   - **SSM 파라미터 스토어**를 사용하여 중앙에서 구성을 저장할 수 있습니다.
   - **구성 파일**을 통해 설정을 지정할 수도 있습니다.
   - EC2 인스턴스의 **IAM 역할**에 적절한 권한을 부여하거나 온프레미스 서버에서 액세스 키를 설정해야 합니다.

### 4. CloudWatch Logs 및 지표
   - EC2 인스턴스에서 메모리 지표를 얻는 유일한 방법은 Unified CloudWatch Agent를 사용하는 것입니다.
   - 모든 지표는 기본적으로 **CWAgent**라는 접두어를 가집니다.

### 5. procstat Plugin
   - **procstat Plugin**을 사용하면 Linux 또는 Windows 서버에서 실행 중인 개별 프로세스의 지표를 수집하고 시스템 자원 사용을 모니터링할 수 있습니다.
   - 예: CPU 사용량, 메모리 사용량, 프로세스별 실행 시간 등.
   - **PID**(프로세스 ID) 파일을 통해 모니터링할 프로세스를 선택할 수 있습니다.
   - 프로세스 관련 지표는 **procstat_** 접두어로 시작합니다. 예: **procstat_cpu_time**, **procstat_cpu_usage** 등.

### 6. 온프레미스 서버에서의 사용
온프레미스 서버에도 Unified CloudWatch Agent를 설치하고, IAM 권한을 부여하여 CloudWatch Logs 및 지표를 푸시할 수 있습니다.

<br>

시험에서 중요하게 알아두어야 할 점은 **procstat Plugin**을 활용하여 서버에서 개별 프로세스의 지표를 모니터링하는 방법입니다.