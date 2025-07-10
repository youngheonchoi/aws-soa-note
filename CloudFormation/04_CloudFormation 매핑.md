## CloudFormation 매핑(Mappings) 정리

---

### 1. 매핑(Mappings)이란?

* **템플릿 내의 고정된 변수 테이블**
* 환경, 리전, 아키텍처 등에 따라 **사전에 정의된 값**을 제공
* 파라미터와 달리 **런타임 입력이 아닌, 정적인 설정값**

---

### 2. 사용 목적

* 환경 간(Dev, Prod 등) 또는 리전 간(us-east-1, ap-northeast-2 등) 설정값을 구분
* **AMI ID, 인스턴스 타입, AZ 등 리전 또는 환경별 값 매핑**에 유용

---

### 3. 매핑 형식 예시

```yaml
Mappings:
  RegionMap:
    us-east-1:
      HVM64: ami-0ff8a91507f77f867
      HVMG2: ami-0a584ac55a7631c0c
    us-west-1:
      HVM64: ami-0bdb828fd58c52235
      HVMG2: ami-066ee5fd4a9ef77f1
    eu-west-1:
      HVM64: ami-047bb4163c506cd98
      HVMG2: ami-0a7c483d527806435
```

* `RegionMap`: 매핑 이름
* `us-east-1`, `us-west-1`, `eu-west-1`: 최상위 키 (리전 등 구분자)
* `HVM64`, `HVMG2`: 세부 키 (아키텍처 등)
* 각 키에 대한 값은 **정적인 문자열** (예: AMI ID)

---

### 4. 매핑 값 참조 방법 – `FindInMap` 함수

```yaml
ImageId: !FindInMap 
  - RegionMap 
  - !Ref "AWS::Region" 
  - HVM64
```

* **첫 번째 인자**: 매핑 이름 (`RegionMap`)
* **두 번째 인자**: 최상위 키 (`AWS::Region`은 가상 파라미터)
* **세 번째 인자**: 하위 키 (`HVM64`)

**실행 결과**:

* 리전이 `us-east-1`이면 → `ami-0ff8a91507f77f867` 반환

---

### 5. 매핑 vs 파라미터

| 구분 | 매핑            | 파라미터                |
| -- | ------------- | ------------------- |
| 입력 | 정적 (템플릿에 정의됨) | 동적 (사용자 입력)         |
| 용도 | 리전, 환경, 계정별 값 | 사용자 지정 가능한 값        |
| 수정 | 템플릿 수정 필요     | 입력값으로 교체 가능         |
| 예시 | 리전별 AMI ID    | EC2 인스턴스 타입, 비밀번호 등 |

**요약**:

* **매핑**: 사전에 결정된 값이 필요할 때
* **파라미터**: 사용자 선택이 가능해야 할 때

---

### 6. 주의 사항

* 매핑은 하드코딩되어 있기 때문에 **템플릿에 직접 수정 필요**
* 리전이 늘어나거나 AMI ID가 바뀌면 템플릿 자체 수정 필요
* 변수화할 수 없는 고정 값 설정에 매우 적합

---

### 7. 가상 파라미터 활용

* `!Ref AWS::Region`을 사용하면 현재 템플릿이 실행 중인 리전을 자동 참조
* 리전별 값을 자동으로 불러올 수 있어 **유지보수성이 향상됨**

---

### 8. 사용 예시 전체

```yaml
Mappings:
  RegionMap:
    ap-northeast-2:
      HVM64: ami-1234567890abcdef0
      HVMG2: ami-1234567890abcdef1

Resources:
  MyInstance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t3.micro
      ImageId: !FindInMap [RegionMap, !Ref "AWS::Region", HVM64]
```

---

### 9. 요약 정리

* 매핑은 CloudFormation 템플릿 내 **정적 변수 테이블**
* `FindInMap` 함수를 통해 매핑된 값 참조 가능
* 리전, 아키텍처, 환경 구분 등 **예측 가능한 값에 유용**
* **파라미터와의 차이**를 정확히 이해하고 적절히 선택할 것