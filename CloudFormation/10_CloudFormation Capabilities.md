## CloudFormation Capabilities 요약

AWS CloudFormation 템플릿을 통해 **IAM 리소스 또는 매크로 기반 구성**을 생성할 때는 \*\*추가 승인(capabilities)\*\*이 필요합니다. 그렇지 않으면 스택 생성을 거부당하고 `InsufficientCapabilitiesException` 오류가 발생할 수 있습니다.

---

### ✅ 주요 Capabilities

| Capability 이름            | 설명                                                      | 사용 시점                                                   |
| ------------------------ | ------------------------------------------------------- | ------------------------------------------------------- |
| `CAPABILITY_IAM`         | CloudFormation이 **익명(자동 생성 이름) IAM 리소스**를 생성할 수 있도록 허용  | 이름 없는 IAM 리소스를 템플릿에 포함한 경우                              |
| `CAPABILITY_NAMED_IAM`   | **사용자 지정 이름의 IAM 리소스**를 생성할 수 있도록 허용                    | 템플릿에 `RoleName`, `UserName`, `GroupName` 등의 명시적 이름 포함 시 |
| `CAPABILITY_AUTO_EXPAND` | **매크로(Macros)** 또는 **동적 변환(Transform)** 기능을 사용하는 템플릿 허용 | 템플릿에 `Transform` 섹션 포함 시                                |

---

### ✅ 왜 Capabilities가 필요한가?

IAM 리소스를 생성하는 작업은 **보안상 민감한 작업**이기 때문에, **CloudFormation에 명시적 허용**을 요구합니다. 이를 통해 실수나 악성 코드로부터 **의도하지 않은 권한 부여**를 방지할 수 있습니다.

---

### ✅ 콘솔 및 CLI에서 사용 방법

* **AWS 콘솔**
  스택 생성 화면에서 아래 메시지가 표시됨:

  > “CloudFormation이 사용자 지정 이름으로 IAM 리소스를 생성할 수 있다는 사실을 확인합니다.”
  > 체크박스를 활성화해야만 스택 생성을 진행할 수 있습니다.

* **AWS CLI 예시**

  ```bash
  aws cloudformation create-stack \
    --stack-name MyIAMStack \
    --template-body file://3-capabilities.yaml \
    --capabilities CAPABILITY_NAMED_IAM
  ```

* **여러 Capability를 동시에 지정 가능**

  ```bash
  --capabilities CAPABILITY_NAMED_IAM CAPABILITY_AUTO_EXPAND
  ```

---

### ✅ 예시 설명

```yaml
Resources:
  MyIAMRole:
    Type: AWS::IAM::Role
    Properties:
      RoleName: MyCustomRoleName    # 사용자 정의 이름 사용 → CAPABILITY_NAMED_IAM 필요
      AssumeRolePolicyDocument:
        ...
      ManagedPolicyArns:
        - arn:aws:iam::aws:policy/AmazonEC2FullAccess
```

* 이 템플릿을 실행하려면 **CAPABILITY\_NAMED\_IAM**을 제공해야 함
* 그렇지 않으면 **InsufficientCapabilitiesException** 오류 발생

---

### ✅ 오류 메시지 예시 및 대응

* **오류 메시지**:

  ```
  An error occurred (InsufficientCapabilitiesException) when calling the CreateStack operation:
  Requires capabilities: [CAPABILITY_NAMED_IAM]
  ```

* **대응 방법**:

  * 콘솔이라면 확인 박스에 체크
  * CLI/SDK라면 `--capabilities` 인자 추가 후 재실행

---

### ✅ 시험 대비 핵심 요약

| 항목                       | 설명                                                           |
| ------------------------ | ------------------------------------------------------------ |
| `CAPABILITY_IAM`         | 이름 없는 IAM 리소스 생성 허용                                          |
| `CAPABILITY_NAMED_IAM`   | **이름이 지정된 IAM 리소스 생성 시 필수**                                  |
| `CAPABILITY_AUTO_EXPAND` | `Transform`, `Macros`, 중첩 스택 등 동적 구조 사용 시 필요                 |
| 오류 메시지                   | `InsufficientCapabilitiesException` 발생 시 capabilities 누락 가능성 |
| 보안 목적                    | **IAM 생성 작업을 명시적으로 승인**받기 위한 장치                              |
