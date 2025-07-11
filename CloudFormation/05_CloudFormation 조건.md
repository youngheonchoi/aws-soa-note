## CloudFormation: 조건(Conditions) 사용법 정리

---

### 1. 조건이란?

* CloudFormation에서 특정 상황에 따라 **리소스 또는 출력물의 생성을 제어**하는 기능
* 예: 개발 환경에서는 리소스를 생성하고, 운영 환경에서는 생략하는 등

---

### 2. 언제 사용할까?

* **환경 구분**: dev, test, prod 환경마다 리소스 구성이 다를 경우
* **리전 구분**: 리전에 따라 생성 여부를 다르게 하고 싶을 때
* **파라미터 기반 조건**: 사용자 입력 값에 따라 조건을 다르게 설정할 때
* **출력 제한**: 특정 상황일 때만 출력(Outputs) 노출

---

### 3. 조건 정의 예시

```yaml
Conditions:
  CreateProdResources: !Equals [ !Ref EnvType, "prod" ]
```

* `EnvType` 파라미터가 `"prod"`일 경우 `CreateProdResources` 조건은 **true**
* 그 외에는 **false**

---

### 4. 사용할 수 있는 조건 함수

| 함수           | 설명                               |
| ------------ | -------------------------------- |
| `Fn::Equals` | 두 값을 비교해 같으면 true                |
| `Fn::Not`    | 조건의 반대값 반환                       |
| `Fn::And`    | 최대 10개 조건이 모두 true일 경우 true      |
| `Fn::Or`     | 최대 10개 조건 중 하나라도 true이면 true     |
| `Fn::If`     | 조건이 true일 경우/false일 경우 각각의 값을 반환 |

---

### 5. 조건 적용 예시 (리소스에 적용)

```yaml
Resources:
  MountPoint:
    Type: AWS::EC2::VolumeAttachment
    Condition: CreateProdResources
    Properties:
      InstanceId: !Ref MyEC2Instance
      VolumeId: !Ref MyVolume
      Device: /dev/sdh
```

* `CreateProdResources`가 **true일 때만** MountPoint 생성

---

### 6. 조건 적용 예시 (출력에 적용)

```yaml
Outputs:
  VolumeIdOutput:
    Description: "ID of the EBS Volume"
    Value: !Ref MyVolume
    Condition: CreateProdResources
```

* 출력 항목도 조건에 따라 포함 여부를 제어 가능

---

### 7. 조건 활용 요령

* 조건문은 **파라미터** 또는 **매핑 값**을 참조 가능
* 여러 조건을 조합해서 **복잡한 제어 흐름 구성 가능**
* **직접 조건을 쓰는 복잡한 함수 사용법은 시험에서 잘 나오지 않음**
* 하지만 **조건을 기반으로 리소스를 제어할 수 있다는 개념은 반드시 숙지 필요**

---

### ✅ 요약

* 조건을 사용하면 **CloudFormation 템플릿을 동적으로 구성**할 수 있음
* 리소스나 출력물에 **생성 여부 제어 기능**을 적용
* 일반적인 사용 패턴은 `Equals`, `If`, `And`, `Or`, `Not` 함수
* 파라미터 기반 또는 환경/리전 기반의 조건이 가장 흔하게 사용됨
