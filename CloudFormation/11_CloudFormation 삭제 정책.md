## CloudFormation 삭제 정책 (Deletion Policy)

CloudFormation의 삭제 정책은 **스택 삭제 또는 리소스 제거 시 리소스에 취할 동작**을 정의합니다.

기본적으로 CloudFormation은 스택 삭제 시 **모든 리소스를 함께 삭제**합니다. 하지만 다음의 정책을 사용하면 **보존하거나 스냅샷을 생성**하는 등의 제어가 가능합니다.

---

### ✅ 삭제 정책 유형

| 정책 이름          | 설명                                    | 사용 예                                         |
| -------------- | ------------------------------------- | -------------------------------------------- |
| `Delete` (기본값) | 리소스를 스택 삭제 시 함께 삭제                    | EC2, S3 등 대부분의 리소스                           |
| `Retain`       | 리소스를 **보존**하고 CloudFormation의 제어에서 분리 | 보안 그룹, DynamoDB 등 데이터 보존 필요 시                |
| `Snapshot`     | 삭제 전 스냅샷을 자동 생성                       | EBS, RDS, Redshift, ElastiCache 등 스냅샷 지원 리소스 |

---

### ✅ 예시 1: 기본 삭제 정책

```yaml
Resources:
  MyInstance:
    Type: AWS::EC2::Instance
    DeletionPolicy: Delete   # 명시하지 않아도 기본값
```

스택 삭제 시 EC2 인스턴스도 함께 삭제됨

---

### ✅ 예시 2: Retain (보존)

```yaml
Resources:
  MyDynamoDB:
    Type: AWS::DynamoDB::Table
    DeletionPolicy: Retain
```

* 스택 삭제 시 테이블은 유지
* 데이터를 보호하고자 할 때 사용
* **수동 삭제 필요**

---

### ✅ 예시 3: Snapshot

```yaml
Resources:
  MyVolume:
    Type: AWS::EC2::Volume
    DeletionPolicy: Snapshot
```

* 스택 삭제 시 볼륨 삭제 전 스냅샷 생성
* **스냅샷은 수동으로 삭제해야 함**

---

### ✅ 예외 사항: S3 버킷

* S3 버킷은 **비어 있어야만 삭제 가능**
* `DeletionPolicy: Delete` 설정 시에도 **버킷이 비어 있지 않으면 실패**
* 해결책:

  * 수동으로 객체 삭제
  * 사용자 지정 리소스로 자동 삭제 구현

---

### ✅ 실습 예시

```yaml
Resources:
  MySecurityGroup:
    Type: AWS::EC2::SecurityGroup
    DeletionPolicy: Retain

  MyVolume:
    Type: AWS::EC2::Volume
    DeletionPolicy: Snapshot
```

* 스택 삭제 시:

  * SecurityGroup → **삭제되지 않음 (delete\_skipped)**
  * EBS Volume → **삭제 전 스냅샷 생성**

---

### ✅ CloudFormation 콘솔 이벤트 상태

| 이벤트 상태            | 의미                             |
| ----------------- | ------------------------------ |
| `DELETE_COMPLETE` | 삭제 완료됨                         |
| `DELETE_SKIPPED`  | `Retain` 설정으로 삭제 생략            |
| `DELETE_FAILED`   | 삭제 중 오류 발생 (예: S3 버킷 비어 있지 않음) |

---

### ✅ 정리

| 삭제 정책         | 스택 삭제 시 결과        | 사용 예시            |
| ------------- | ----------------- | ---------------- |
| `Delete` (기본) | 리소스 삭제            | 일반 리소스           |
| `Retain`      | 리소스 유지 (직접 삭제 필요) | 데이터 보존 필수 리소스    |
| `Snapshot`    | 리소스 삭제 전 스냅샷 생성   | 백업이 필요한 스토리지 리소스 |