## **EC2 AMI 생성 시 No-Reboot 옵션**  

EC2 인스턴스에서 AMI(Amazon Machine Image)를 생성할 때 **No-Reboot 옵션**을 설정할 수 있습니다.  

<br>

### **1. 기본 동작 방식**  
- 기본적으로 AMI를 생성할 때 **파일 시스템의 무결성을 유지하기 위해** EC2 인스턴스가 먼저 종료됩니다.  
- 이후, 인스턴스에 연결된 **EBS 볼륨이 스냅샷으로 저장되고, 이 스냅샷이 AMI로 변환**됩니다.  

<br>

### **2. No-Reboot 옵션 활성화 시 동작**  
- **EC2 인스턴스를 중지하지 않고** AMI를 생성할 수 있습니다.  
- 실행 중인 인스턴스의 EBS 볼륨에서 바로 스냅샷을 생성하여 AMI를 만들게 됩니다.  
- 그러나 **운영 체제(OS) 버퍼가 디스크에 완전히 기록되지 않을 가능성이 있어 파일 시스템의 무결성이 보장되지 않습니다.**  

<br>

### **3. AWS 백업 서비스와의 관계**  
- AWS 백업 서비스를 사용하여 EC2 AMI를 생성할 경우, **기본적으로 No-Reboot 옵션이 활성화**됩니다.  
- 즉, **인스턴스를 재부팅하지 않고** 스냅샷을 생성하므로, 파일 시스템 무결성이 보장되지 않습니다.  
- 이를 해결하려면 **EventBridge 규칙을 사용하여 Lambda 함수로 정기적인 백업 프로세스를 실행**하고, AMI를 생성하기 전에 인스턴스를 재부팅하는 방법을 고려해야 합니다.  

<br>

### **4. AMI 생성 방법**  
- AWS 콘솔에서 **EC2 인스턴스를 우클릭 → "Image and Templates" → "Create Image"** 선택  
- "No-Reboot" 옵션을 활성화하면 **인스턴스를 중지하지 않고** AMI를 생성 가능  
- 하지만 **파일 시스템 무결성 문제를 고려하여 신중하게 선택해야 함**  

<br>

### **5. 결론**  
- **파일 시스템 무결성이 중요한 경우**: 인스턴스를 먼저 중지한 후 AMI 생성  
- **다운타임을 최소화해야 하는 경우**: No-Reboot 옵션 사용, 그러나 데이터 무결성 보장 필요  
- **AWS 백업 서비스 사용 시 주의**: 기본적으로 No-Reboot 옵션이 활성화됨  