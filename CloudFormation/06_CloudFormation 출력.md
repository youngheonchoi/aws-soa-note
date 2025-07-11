## CloudFormation: 출력(Outputs)과 스택 간 연결

---

### 1. 출력(Outputs) 섹션이란?

* **선택 사항**으로 정의되는 템플릿의 일부
* CloudFormation 스택에서 생성된 **값들을 외부에 노출**
* 콘솔, CLI에서 출력값 확인 가능
* **다른 스택에서 Import하여 재사용** 가능 (스택 간 참조)

---

### 2. 출력의 사용 예

* 예를 들어 VPC, 보안 그룹, 서브넷 등 공통 리소스를 가진 네트워크 스택이 있을 때:

  * VPC ID, Subnet ID 등을 `Outputs`로 선언
  * 다른 스택에서 **ImportValue**로 참조 가능

---

### 3. 출력 구문 예시

```yaml
Outputs:
  SSHSecurityGroup:
    Description: "SSH 보안 그룹 ID"
    Value: !Ref SSHSecurityGroup
    Export:
      Name: SSHSecurityGroup
```

* **Description**: 출력 항목에 대한 설명
* **Value**: 출력할 실제 값 (`!Ref`, `!GetAtt` 등 사용 가능)
* **Export > Name**: 출력값을 외부로 내보내는 이름 (해당 리전에서 **고유**해야 함)

---

### 4. 출력 사용 예 – 스택 간 참조

#### 예시: 다른 스택에서 가져오기 (Import)

```yaml
Resources:
  MyInstance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-12345678
      InstanceType: t2.micro
      SecurityGroupIds:
        - !ImportValue SSHSecurityGroup
```

* `!ImportValue SSHSecurityGroup`
  → 다른 스택에서 `Export`한 값을 현재 템플릿에서 사용함

---

### 5. 출력의 장점

| 장점      | 설명                                                          |
| ------- | ----------------------------------------------------------- |
| 스택 간 연결 | VPC, 보안 그룹, 서브넷 등 공유 리소스를 다른 스택에서 참조                        |
| 모듈화 가능  | 역할을 분리한 템플릿(예: VPC 템플릿, 애플리케이션 템플릿 등) 간 협업 용이               |
| 유지관리 효율 | 스택 재사용성 증가, 분리된 관리 가능                                       |
| 가시성 향상  | 콘솔이나 CLI에서 출력값 확인 가능 (`aws cloudformation describe-stacks`) |

---

### 6. 주의 사항

* **`Export` 이름은 리전 내에서 고유해야 함**
* **다른 스택에서 해당 값을 참조 중일 경우, Export한 스택은 삭제 불가**
* 스택 간 순환 참조 불가 (A → B → A 형태)

---

### 7. 요약

* `Outputs`는 템플릿에서 생성된 값을 외부로 노출시키는 섹션
* `Export`를 사용해 출력값을 **다른 스택에서 ImportValue로 재사용** 가능
* 잘 설계된 스택 간 연결 구조를 통해 **모듈화된 인프라 구성** 가능
* **ImportValue 사용 시, 스택 삭제 제한**에 유의해야 함