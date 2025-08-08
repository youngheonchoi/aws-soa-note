## CloudFormation 스택 정책(Stack Policy)

### ✅ 정의

CloudFormation 스택 정책은 **스택 업데이트 중 특정 리소스에 대한 의도치 않은 변경을 방지**하기 위한 **JSON 문서**입니다.

* 주로 **운영 환경의 중요한 리소스**(예: 프로덕션 DB) 보호에 사용됩니다.
* 정책은 `Update:*` 작업에 대해 **Allow** 또는 **Deny**로 구성됩니다.

---

### ✅ 기본 동작

* 기본적으로 CloudFormation은 **스택 내 모든 리소스를 자유롭게 업데이트**할 수 있음
* 스택 정책을 적용하면 **특정 리소스를 업데이트 불가** 상태로 설정할 수 있음

---

### ✅ 스택 정책 구성 예시

```json
{
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "Update:*",
      "Principal": "*",
      "Resource": "*"
    },
    {
      "Effect": "Deny",
      "Action": "Update:*",
      "Principal": "*",
      "Resource": "LogicalResourceId/ProductionDatabase"
    }
  ]
}
```

#### 해석:

* **모든 리소스**에 대해 업데이트를 허용함 (`Allow`)
* 단, **ProductionDatabase** 리소스에 대해서는 모든 업데이트를 **거부**함 (`Deny`)

---

### ✅ 구성 요소 설명

| 필드          | 설명                                               |
| ----------- | ------------------------------------------------ |
| `Effect`    | `Allow` 또는 `Deny` 지정                             |
| `Action`    | `Update:*` – 모든 업데이트 작업을 의미                      |
| `Principal` | `"*"` – 모든 사용자 대상                                |
| `Resource`  | `"*"` 또는 `LogicalResourceId/리소스이름` 으로 대상 리소스를 지정 |

---

### ✅ 실무 팁

* **스택 정책을 설정하면 기본적으로 모든 리소스가 보호됨**

  * 따라서 *업데이트를 허용할 리소스*는 **명시적으로 `Allow` 문이 있어야 함**
* 주로 **프로덕션 DB, 보안 설정, IAM 리소스 등 민감 리소스**에 사용
* 콘솔, CLI, SDK를 통해 **스택 생성 시 또는 생성 이후에 정책 적용 가능**

---

### ✅ CLI 예시

```bash
aws cloudformation set-stack-policy \
  --stack-name MyStack \
  --stack-policy-body file://policy.json
```

---

### ✅ 시험 대비 핵심 요점

* 스택 정책은 **스택 업데이트 중 보호 기능을 제공**한다
* **리소스를 의도치 않게 수정하거나 삭제하지 않도록 방지**하는 용도로 사용됨
* **스택 생성 후 적용 가능**하며, JSON 문서로 구성됨
* 스택 정책이 설정된 경우, 특정 리소스를 변경하려면 먼저 정책을 **수정해야 함**