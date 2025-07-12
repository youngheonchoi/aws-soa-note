## CloudFormation 내장 함수 정리

CloudFormation에서는 다양한 \*\*내장 함수(Built-in Functions)\*\*를 제공하여 템플릿 내에서 동적 설정, 참조, 변환 등을 수행할 수 있습니다. 이 중 **시험이나 실무에서 자주 사용되는 주요 함수**들을 중심으로 설명합니다.

---

### 꼭 알아야 할 핵심 함수

| 함수                                 | 설명                                           |
| ---------------------------------- | -------------------------------------------- |
| `Ref`                              | 리소스 또는 파라미터의 값을 참조 (리소스의 기본 ID 또는 파라미터 값 반환) |
| `GetAtt`                           | 리소스의 속성(Attributes) 값을 참조                    |
| `FindInMap`                        | Mapping에서 값 찾기                               |
| `ImportValue`                      | 다른 스택의 Export 값을 가져오기                        |
| `Join`                             | 문자열을 구분자로 결합                                 |
| `Sub`                              | 문자열 내 변수 치환                                  |
| `If`, `Equals`, `Not`, `And`, `Or` | 조건 함수                                        |
| `Base64`                           | 문자열을 Base64 인코딩                              |
| `Select`, `Split`                  | 배열에서 항목 선택 또는 문자열 분리                         |
| `Cidr`, `GetAZs`, `Length`         | 고급 네트워크 및 배열 연산 함수                           |
| `Transform`                        | 템플릿 매크로 또는 예: SAM 변환                         |

---

### 1. `Ref`

**용도**: 리소스의 고유 식별자(ID)나 파라미터 값을 참조
**형식**:

```yaml
!Ref MyParameter
```

**예시**: `VPC`를 참조하여 `Subnet`에 설정

```yaml
VpcId: !Ref MyVPC
```

---

### 2. `GetAtt` (Get Attribute)

**용도**: 리소스의 속성 값 참조 (예: IP, DNS, AZ 등)
**형식**:

```yaml
!GetAtt EC2Instance.AvailabilityZone
```

**예시**: EC2 인스턴스의 가용 영역을 참조해 EBS 볼륨에 설정

---

### 3. `FindInMap`

**용도**: `Mappings` 내에서 값 찾기
**형식**:

```yaml
!FindInMap [ RegionMap, !Ref "AWS::Region", HVM64 ]
```

**예시**: 리전과 아키텍처에 따라 AMI ID 선택

---

### 4. `ImportValue`

**용도**: 다른 CloudFormation 스택에서 `Export`된 값을 가져오기
**형식**:

```yaml
!ImportValue SSHSecurityGroup
```

**예시**: 다른 스택에서 Export한 보안 그룹 ID를 참조

---

### 5. `Base64`

**용도**: 문자열을 Base64 인코딩 (주로 UserData 전달 시 사용)
**예시**:

```yaml
UserData:
  Fn::Base64: !Sub |
    #!/bin/bash
    echo "Hello, World" > /var/tmp/hello.txt
```

---

### 6. 문자열 조작 함수

| 함수       | 설명          | 예시                                         |
| -------- | ----------- | ------------------------------------------ |
| `Join`   | 문자열 결합      | `!Join [ "-", [ "a", "b", "c" ] ] → a-b-c` |
| `Sub`    | 변수 치환       | `!Sub "Value is ${MyVar}"`                 |
| `Split`  | 문자열 분리      | `!Split [ ",", "a,b,c" ]`                  |
| `Select` | 리스트에서 항목 선택 | `!Select [ 1, [ "a", "b", "c" ] ] → b`     |

---

### 7. 조건 함수

| 함수       | 설명                    |
| -------- | --------------------- |
| `If`     | 조건에 따라 반환값 선택         |
| `Equals` | 두 값 비교                |
| `Not`    | 부정 조건                 |
| `And`    | 여러 조건 모두 만족 시 true    |
| `Or`     | 조건 중 하나라도 true 시 true |

**예시**:

```yaml
!If [ CreateProdResources, "t3.large", "t3.micro" ]
```

---

### 8. 기타 함수

* `GetAZs`: 리전의 가용 영역 목록 반환
* `Cidr`: 주어진 IP 블록에서 CIDR 블록 계산
* `Length`: 배열의 길이 반환
* `Transform`: 템플릿 매크로 (예: AWS::Serverless-2016-10-31)

---

### 📝 요약

* `Ref`, `GetAtt`, `FindInMap`, `ImportValue`는 시험과 실무에서 자주 쓰이는 핵심 함수
* 문자열 처리에는 `Join`, `Sub`, 조건 제어에는 `If`, `Equals`, `Not` 등이 자주 사용
* 공식 문서에서 각 함수의 사용법과 예시를 참고하여 숙지하는 것이 중요