### 1. 파라미터란?

* **CloudFormation 템플릿에 사용자 입력 값을 전달하는 방법**
* 리소스 생성 시 동적인 입력값을 반영 가능
* 템플릿 재사용성과 유연성 증가

---

### 2. 언제 사용해야 할까?

* **템플릿에서 나중에 바뀔 가능성이 있는 값**이라면 파라미터로 분리
* **사전에 고정할 수 없는 값**이라면 파라미터로 정의
* 예: EC2 인스턴스 타입, 보안 그룹 설명, 비밀번호 등

---

### 3. 파라미터 속성

| 속성                        | 설명                                                                                   |
| ------------------------- | ------------------------------------------------------------------------------------ |
| **Type**                  | 문자열(`String`), 숫자(`Number`), 쉼표 구분 목록(`CommaDelimitedList`), 숫자 목록(`List<Number>`) 등 |
| **Default**               | 기본값 지정                                                                               |
| **AllowedValues**         | 허용된 값 리스트로 제한 (예: `t2.micro`, `t2.small`)                                            |
| **AllowedPattern**        | 정규식으로 입력값 제한                                                                         |
| **MinLength / MaxLength** | 문자열 길이 제약                                                                            |
| **MinValue / MaxValue**   | 숫자 값 범위 제한                                                                           |
| **Description**           | 파라미터 설명                                                                              |
| **ConstraintDescription** | 제약 조건 실패 시 출력되는 메시지                                                                  |
| **NoEcho**                | `true`로 설정 시 값이 출력되지 않음 (예: 비밀번호 입력 시 보안 목적)                                         |

---

### 4. 파라미터 예시

```yaml
Parameters:
  InstanceType:
    Description: EC2 인스턴스 유형 선택
    Type: String
    Default: t2.micro
    AllowedValues:
      - t2.micro
      - t2.small
      - t2.medium
    ConstraintDescription: 반드시 지원되는 인스턴스 유형 중 하나여야 합니다.
```

```yaml
  DBPassword:
    Description: 데이터베이스 비밀번호
    Type: String
    NoEcho: true
```

---

### 5. Ref 함수로 파라미터 참조하기

```yaml
Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceType
```

* `!Ref`는 파라미터나 리소스를 참조할 때 사용
* 동일한 형식으로 리소스나 다른 파라미터도 참조 가능

---

### 6. 리소스 참조와의 차이

```yaml
  SSHSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: SSH access
```

```yaml
Resources:
  MyInstance:
    Type: AWS::EC2::Instance
    Properties:
      SecurityGroups:
        - !Ref SSHSecurityGroup
```

* 위 예시는 리소스를 참조한 것이며, 파라미터와 방식은 동일하지만 참조 대상이 다름

---

### 7. CloudFormation 제공 가상 파라미터

| 이름                 | 설명                                  |
| ------------------ | ----------------------------------- |
| **AWS::AccountId** | 현재 계정의 ID                           |
| **AWS::Region**    | 현재 스택이 배포된 리전                       |
| **AWS::StackId**   | 스택의 전체 ARN                          |
| **AWS::StackName** | 스택 이름                               |
| **AWS::Partition** | `aws`, `aws-cn`, `aws-us-gov` 중 하나  |
| **AWS::NoValue**   | 리소스 속성을 제거할 때 사용 (조건에 따라 속성을 제거 가능) |

**예시:**

```yaml
Outputs:
  RegionOutput:
    Value: !Ref AWS::Region
```

---

### 8. 요약 정리

* 파라미터는 **동적 입력값**을 위한 기능
* `!Ref`로 참조하여 템플릿 내부의 리소스 설정에 사용
* 다양한 제약 조건을 통해 **유효성 검증 및 사용자 제한** 가능
* `NoEcho`는 민감 정보 보호에 유용
* CloudFormation 내 **가상 파라미터**를 통해 현재 계정, 리전 등 자동 확인 가능
