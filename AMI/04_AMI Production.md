## **IAM 정책과 AWS Config를 활용한 승인된 AMI 사용**  

### **1. 승인된 AMI(Approved AMI)란?**  
- **특정 태그가 지정된 AMI**  
- 예: `Environment: "Prod"` 태그가 있는 AMI만 사용 가능  
- **IAM 정책과 결합하여** 특정 사용자가 승인된 AMI에서만 EC2 인스턴스를 실행하도록 제한 가능  

<br>

### **2. IAM 정책을 활용한 AMI 사용 제한**  
- **사전 승인된 AMI에서만 EC2 인스턴스를 실행하도록 강제**  
- IAM 정책에서 특정 조건을 설정하여 **태그가 없는 AMI 실행 차단**  
- 예제 조건 :  
  ```json
  {
    "Effect": "Deny",
    "Action": "ec2:RunInstances",
    "Resource": "*",
    "Condition": {
      "StringNotEquals": {
        "ec2:ImageTag/Environment": "Prod"
      }
    }
  }
  ```
  - `Environment: "Prod"` 태그가 없는 AMI 실행을 **거부**  

<br>

### **3. AWS Config를 활용한 비준수 인스턴스 탐지**  
- **AWS Config를 이용해 승인되지 않은 AMI에서 실행된 EC2 인스턴스를 감지**  
- 사용자가 승인되지 않은 AMI에서 EC2 인스턴스를 실행하면 이를 **규정 위반(Non-compliant)으로 플래그**  
- **모니터링 프로세스:**  
  1. AWS Config 규정 정의  
  2. 모든 EC2 인스턴스를 지속적으로 스캔  
  3. 규정을 준수하는 인스턴스는 **초록색(Compliant)** 표시  
  4. 승인되지 않은 AMI에서 실행된 인스턴스는 **위반(Non-compliant)으로 플래그**  

<br>

### **결론**  
✅ **IAM 정책과 승인된 AMI 태그를 활용하여 보안 강화**  
✅ **AWS Config를 이용해 비준수 EC2 인스턴스 탐지 및 관리 가능**  
✅ **운영 환경(Prod)에서 승인되지 않은 AMI 사용을 방지하여 보안 및 규정 준수 보장**