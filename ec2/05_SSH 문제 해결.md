## SSH 연결 문제 및 해결 방법

### 1. **Unprotected Private Key File 오류**
   - **원인 :** PEM 키 파일의 권한이 적절하게 설정되지 않음
   - **오류 메시지 :** `Unprotected private key file!`
   - **해결 방법 :**
     1. PEM 파일의 권한을 `0400` 또는 `0600`으로 설정
     2. 명령어 실행: `chmod 400 DemoKeyPair.pem`

<br>

### 2. **잘못된 사용자 이름 오류**
   - **원인:** SSH 접속 시 사용자가 올바르지 않음
   - **오류 메시지:**
     - `Permission denied`
     - `Too many authentication failures`
   - **해결 방법:**
     1. 사용 중인 AMI에 따라 올바른 사용자 이름 확인
        - Amazon Linux 2: `ec2-user`
        - Ubuntu: `ubuntu`
     2. SSH 명령어 예시:
        ```sh
        ssh -i DemoKeyPair.pem ec2-user@<EC2_IP>
        ```

<br>

### 3. **Connection Timed Out 오류**
   - **원인:** 네트워크 및 보안 그룹 문제
   - **오류 메시지:** `Connection timed out`
   - **해결 방법:**
     1. **보안 그룹 설정 확인**
        - 인바운드 규칙에서 **포트 22 (SSH)**가 열려 있는지 확인
     2. **서브넷 라우트 테이블 확인**
        - 올바르게 인터넷 게이트웨이(`igw-xxxxxx`)가 연결되어 있어야 함
     3. **NACL 설정 확인**
        - 인바운드 및 아웃바운드 규칙이 SSH(포트 22) 트래픽을 허용하는지 확인
     4. **공용 IPv4 주소 확인**
        - 인스턴스가 퍼블릭 서브넷에 있고, 공용 IPv4 주소가 할당되어 있어야 함
     5. **CPU 사용량 확인**
        - 인스턴스의 CPU가 100%로 과부하 상태이면 SSH 접속이 불가능할 수 있음

<br>

### 4. **EC2 Instance Connect 사용 시 문제**
   - **EC2 Instance Connect와 SSH 차이점:**
     - SSH: 사용자의 공용 IP가 인바운드 규칙에 등록되어 있어야 접속 가능
     - EC2 Instance Connect: AWS에서 관리하는 특정 IP 범위를 허용해야 사용 가능
   - **EC2 Instance Connect 설정 방법:**
     1. **AWS에서 제공하는 JSON 파일 확인**
        - 특정 리전에 대한 `EC2_INSTANCE_CONNECT` 서비스의 IP 범위 제공
     2. **보안 그룹 설정**
        - 해당 IP 범위를 인바운드 규칙에 추가해야 함
     3. **JSON 파일 다운로드 후 CIDR 블록 추가**
        - `eu-central-1` (프랑크푸르트 리전) 예시:
          ```sh
          # JSON 파일 다운로드 후 필터링
          curl -s https://ip-ranges.amazonaws.com/ip-ranges.json | jq '.prefixes[] | select(.service=="EC2_INSTANCE_CONNECT" and .region=="eu-central-1")'
          ```
        - 보안 그룹에서 해당 CIDR 블록을 인바운드 규칙에 추가
     4. **EC2 Instance Connect로 접속 테스트**
        - AWS 콘솔에서 **EC2 Instance Connect** 클릭하여 접속

<br>

### 5. **보안 그룹 변경 후 SSH 접속 실패**
   - **원인:** 인바운드 규칙에서 SSH(포트 22) 허용이 제거됨
   - **해결 방법:**
     1. 보안 그룹 인바운드 규칙에서 `My IP`를 추가 후 SSH 재시도
     2. 규칙 적용 후 변경사항이 즉시 반영되는지 확인

<br>

### 6. **시험에서 나올 가능성이 있는 문제 유형**
1. **SSH 키 파일 문제**
   - `Unprotected private key file!` 오류가 발생하는 원인은?
     - A) PEM 파일의 권한이 잘못됨 ✅
     - B) SSH 포트가 닫혀 있음
     - C) EC2 인스턴스가 종료됨
     - D) 퍼블릭 IP가 없음

2. **잘못된 사용자 이름 문제**
   - `Permission denied` 오류가 발생하는 가장 가능성 높은 원인은?
     - A) SSH 포트가 닫혀 있음
     - B) 잘못된 사용자 이름 입력 ✅
     - C) PEM 파일이 없거나 잘못됨
     - D) 보안 그룹 문제

3. **EC2 Instance Connect 문제**
   - EC2 Instance Connect가 실패하는 이유 중 올바른 것은?
     - A) SSH 키 파일이 필요하다
     - B) 인스턴스의 CPU가 100% 사용 중이다
     - C) AWS에서 제공하는 IP 범위를 허용해야 한다 ✅
     - D) EC2가 퍼블릭 IP가 없기 때문이다