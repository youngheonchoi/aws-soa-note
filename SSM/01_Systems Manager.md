## 1. **AWS Systems Manager(SMM)란?**  
   - **EC2 및 온프레미스 서버를 대규모로 관리**할 수 있는 서비스.  
   - 인프라 상태에 대한 **통찰력 제공**, 문제 감지, **패치 자동화**, **규정 준수 강화** 기능 포함.  
   - **Windows 및 Linux OS 지원**, **CloudWatch, AWS Config와 통합**.  
   - **무료 서비스**, 사용한 리소스에 대해서만 비용 청구.  

<br>

## 2. **시험에 출제될 핵심 기능**  
   - **운영 관리**: Explorer, OpsCenter, CloudWatch Dashboard, PHD, Incident Manager  
   - **변경 관리**: Change Manager, 자동화, Change Calendar, Maintenance Windows  
   - **애플리케이션 관리**: Application Manager, AppConfig, Parameter Store  
   - **노드 관리**: Fleet Manager, Compliance, Inventory, 하이브리드 정품 인증,  
     Session Manager, Run Command, State Manager, Patch Manager, Distributor  

<br>

## 3. **SSM 작동 방식**  
   - **SSM Agent 설치 필요**:  
     - Amazon Linux 2 AMI 기본 포함, 일부 Ubuntu AMI도 지원.  
     - EC2 인스턴스 또는 온프레미스 서버/VM에 설치하여 SSM과 통신.  
   - **IAM 역할 확인**:  
     - 올바른 IAM 권한이 없으면 Systems Manager에서 제어 불가.  
   - **문제 발생 시 체크 항목**:  
     - **SSM Agent 실행 여부** 확인.  
     - **IAM 역할의 적절한 권한 부여** 여부 확인.