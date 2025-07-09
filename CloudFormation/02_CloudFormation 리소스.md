### 1. 리소스란?

* **CloudFormation 템플릿의 유일한 필수 섹션**
* 생성될 AWS 리소스들을 정의
* 리소스 간 참조 가능
* 생성, 업데이트, 삭제 순서를 AWS가 자동 관리
* **현재 700개 이상의 리소스 유형**이 존재

---

### 2. 리소스 유형 식별 규칙

형식

```
AWS::서비스이름::리소스타입
```

예시

* `AWS::EC2::Instance`
* `AWS::S3::Bucket`

---

### 3. 공식 문서 접근 방법

**AWS CloudFormation Resource Reference 페이지**:

* 리소스 유형별 문서 존재
* 각 리소스

  * 구문 (YAML/JSON)
  * 필수 및 선택 속성
  * 속성의 데이터 타입
  * 업데이트 동작 (예: 교체 여부)
  * 예제 코드 (YAML/JSON)

**예시 - EC2 인스턴스**:

* 유형 : `AWS::EC2::Instance`
* 속성

  * `ImageId` (필수)
  * `InstanceType` (필수)
  * `IamInstanceProfile` (선택)
  * `SecurityGroups` (string 배열)
* `ImageId` 변경 시 인스턴스 교체 필요

---

### 4. 문서 탐색 팁

* [공식 문서](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html)를 통해 각 리소스 탐색
* 특정 리소스를 찾을 땐, 검색창에 다음과 같이 입력

  ```
  [서비스명] cloudformation site:docs.aws.amazon.com
  ```

  예 : `elastic ip cloudformation site:docs.aws.amazon.com`

---

### 5. 템플릿 예시

```yaml
Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-12345678
      InstanceType: t2.micro
      SecurityGroups:
        - !Ref SSHSecurityGroup

  SSHSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: SSH Access
```

---

### 6. 자주 묻는 질문

#### Q1. 동적인 수의 리소스를 생성할 수 있나요?

* 기본적으로는 불가능
* **CloudFormation Macro** 또는 **Transform**을 사용해야 함

  * (해당 기능은 고급 주제로 별도 학습 필요)

#### Q2. 모든 AWS 서비스가 지원되나요?

* **거의 대부분 지원**
* 일부 신규 서비스 또는 베타 기능은 미지원일 수 있음
* 이 경우 **Custom Resource**를 사용하여 우회 가능

---

### 7. 핵심 요약

| 항목     | 설명                                        |
| ------ | ----------------------------------------- |
| 리소스 정의 | 템플릿 내에서 생성될 실제 AWS 리소스 지정                 |
| 문서 중요성 | 각 리소스의 요구 속성 및 구조를 정확히 이해해야 오류 없이 배포 가능   |
| 자동 처리  | 리소스 간 종속성, 순서, 업데이트 등을 AWS가 처리            |
| 실전 팁   | 공식 문서 예제를 자주 참고하고, 작은 템플릿부터 시작해서 점차 확장할 것 |