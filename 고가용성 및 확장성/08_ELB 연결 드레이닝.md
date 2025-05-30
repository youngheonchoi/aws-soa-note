## 연결 드레이닝 (Connection Draining) / 등록 취소 지연 (Deregistration Delay)

**연결 드레이닝** 또는 **등록 취소 지연**은 로드 밸런서에서 **대상 인스턴스를 제거하거나 비정상 상태로 전환할 때**,
해당 인스턴스에 **기존 연결된 요청들을 정상적으로 완료**할 수 있도록 일정 시간을 부여하는 기능입니다.

> 로드 밸런서 유형에 따라 명칭이 다릅니다.

| 로드 밸런서                                | 명칭                              |
| ------------------------------------- | ------------------------------- |
| **CLB (Classic Load Balancer)**       | 연결 드레이닝 (Connection Draining)   |
| **ALB / NLB (Application / Network)** | 등록 취소 지연 (Deregistration Delay) |

<br>

## 작동 방식

1. EC2 인스턴스가 **비정상 상태**로 감지되거나 **등록 해제**됨
2. 로드 밸런서는 해당 인스턴스를 **드레이닝 상태**로 전환
3. **기존 연결(활성 요청)은 유지**, 새 요청은 전달하지 않음
4. 설정된 시간이 지나거나 모든 요청이 완료되면 인스턴스 종료 처리

> 새 연결은 자동으로 다른 정상 인스턴스로 분산됩니다.

<br>

## 예시

* EC2 인스턴스 3개 중 1개가 드레이닝 모드로 전환됨
* 해당 인스턴스에 연결된 사용자의 요청은 **지속**
* 새 사용자 요청은 나머지 두 인스턴스로 전달됨

<br>

## 설정 가능 값

| 항목        | 설명                        |
| --------- | ------------------------- |
| **시간 범위** | 1초 \~ 3,600초 (최대 1시간)     |
| **기본값**   | 300초 (5분)                 |
| **0초 설정** | 드레이닝 **비활성화** (즉시 등록 취소됨) |

> 설정은 로드 밸런서 또는 대상 그룹 수준에서 수행됩니다.

<br>

## 상황별 설정 예시

| 사용 시나리오                  | 추천 시간 설정 |
| ------------------------ | -------- |
| **짧은 요청 (API 등)**        | 30초 이하   |
| **파일 업로드, 영상 처리 등 긴 요청** | 300초 이상  |
| **즉시 종료 필요 (테스트 환경 등)**  | 0초       |

<br>

## 정리

| 항목            | 설명                                       |
| ------------- | ---------------------------------------- |
| **기능 목적**     | 인스턴스를 제거할 때 **기존 요청이 중단되지 않도록** 유예 시간 부여 |
| **지원 로드 밸런서** | ALB, NLB, CLB                            |
| **비활성화 방법**   | 값 0으로 설정                                 |
| **성능 최적화 팁**  | 요청 시간에 따라 유동적으로 설정 필요                    |
