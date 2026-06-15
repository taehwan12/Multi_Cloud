# ☁️ AWS x Naver Cloud Platform 멀티 클라우드(Multi-Cloud) 글로벌 인프라 구축 프로젝트

> **단일 클라우드 장애(Single Point of Failure)에 대응하기 위해 AWS와 NCP(네이버 클라우드 플랫폼)의 가상 네트워크 인프라를 유기적으로 연동하고, 상호 백업 체계 및 고가용성 로드 밸런싱을 구현한 엔터프라이즈급 멀티 클라우드 아키텍처 프로젝트입니다.**

---

## 👥 Team Information (4rsenal)
- **팀원:** 전병욱, 신재섭, 정태환, 이은석, 권승빈

<br>

## 🛠️ Tech Stack & Environment
- **AWS Infrastructure:** `VPC`, `EC2 (Ubuntu / Rocky Linux)`, `Application Load Balancer`, `Route 53`
- **NCP Infrastructure:** `VPC`, `Server`, `Network Load Balancer`, `Global DNS`
- **Inter-Cloud Connectivity:** `IPsec VPN`, `DNS Failover Policy`
- **Web/WAS Software:** `Nginx`, `Apache Tomcat`, `Docker / Containerization`

---

## 📌 주요 수행 내용 (Key Features)

### 1️⃣ 멀티 클라우드 가상 네트워크(VPC) 및 서브넷 아키텍처 설계
* **AWS 인프라 동기화:** `4RSENAL-AWS-VPC` 대역을 기반으로 퍼블릭 서브넷(Web 계층)과 프라이빗 서브넷(WAS/DB 계층)을 다중 가용 영역(AZ 2A, 2C)에 격리 배치
* **NCP 인프라 동기화:** `4RSENAL-NCP-VPC` 대역을 네이버 클라우드 플랫폼 상에 수립하고, AWS와 대칭되는 퍼블릭/프라이빗 서버 서브넷 세트를 독립적으로 전개
* **IP CIDR 블록 최적화:** 두 클라우드 간의 VPN 터널링 통신 시 라우팅 충돌이 발생하지 않도록 IP 대역 분할 및 서브넷 마스크 수동 설계 수립

### 2️⃣ 하이브리드 클라우드 연동을 위한 IPsec VPN 터널링
* **클라우드 간 암호화 채널 구축:** AWS의 가상 사설 게이트웨이(VGW)와 NCP의 VPN 게이트웨이를 연동하여 전용 IPsec VPN 파이프라인 형성
* **라우팅 테이블(Routing Table) 수동 제어:**
  * AWS VPC 라우팅 테이블에 NCP 대역으로 향하는 커스텀 타겟 경로 추가
  * NCP VPC 라우팅 테이블에 AWS 대역으로 향하는 가용 경로를 매핑하여 이기종 클라우드 간 내부 사설 IP(Private IP) 간 직통 통신 활성화

### 3️⃣ 이기종 클라우드 웹/WAS 인프라 이중화 및 컨테이너라이징
* **컨테이너 기반 형상 관리:** Nginx(Web)와 Tomcat(WAS) 인프라를 Docker 컨테이너 파일 시스템으로 가상화하여 AWS 인스턴스와 NCP 인스턴스 양측에 100% 동일한 구동 환경 신속 배포
* **배스천 호스트(Bastion Host) 보안 통제:** 외부 공인 인터넷 노출이 제한된 AWS와 NCP의 프라이빗 WAS 서버군을 제어하기 위해 각 클라우드 진입점에 Bastion 단일 통로 개설 및 프라이빗 키 보안 관리

### 4️⃣ 글로벌 DNS(Route 53 x Global DNS) 및 교차 로드 밸런싱
* **클라우드별 가용성 로드 밸런서 배치:**
  * **AWS 영역:** HTTP 트래픽을 지능적으로 분산하는 `AWS ALB(Application Load Balancer)` 가동
  * **NCP 영역:** 고성능 대용량 레이어 처리를 위한 `NCP NLB(Network Load Balancer)` 배치
* **멀티 클라우드 글로벌 라우팅 수립:** AWS Route 53 및 NCP Global DNS 레코드를 연동하여, 사용자 트래픽을 양대 클라우드의 로드 밸런서 공인 DNS 주소로 다이렉트 별칭(Alias) 포워딩 수립
* **장애 조치(Failover) 정책 수립:** 주기적인 Health Check를 수행하여 특정 클라우드 벤더의 데이터 센터에 전체 장애가 발생하더라도, 트래픽을 정상 가동 중인 다른 클라우드로 즉시 우회(Failover)시키는 24/7 무중단 아키텍처 검증

### 5️⃣ 엔드투엔드(End-to-End) 데이터 동기화 및 최종 트랜잭션 검증
* **클라우드 간 백엔드 인터페이스 연계:** AWS Nginx에서 NCP Tomcat으로, 또는 NCP Nginx에서 AWS Tomcat으로 교차 프록시 통신이 원활하게 수행되는지 상호 연결성 테스트 완료
* **최종 동작 확인:** 사용자가 멀티 클라우드 엔드포인트 주소로 접속하여 웹 어플리케이션 내 데이터를 적재 및 쿼리하는 모의 트래픽을 유도, 이기종 클라우드 간 네트워크 패킷 지연 없이 안정적으로 멀티 티어 서비스가 가동되는 최종 성과 달성

---

## 📈 배운 점 및 성과 (Retrospective)
- **클라우드 아그노스틱(Cloud Agnostic) 역량 내재화:** 특정 CSP(클라우드 서비스 제공사)에 종속되지 않고 AWS와 NCP의 특성을 상호 보완적으로 활용하여 범용적인 클라우드 아키텍처 설계 능력을 확립했습니다.
- **가용성 극대화 및 재해 복구(DR) 수립:** IPsec VPN 인프라와 글로벌 DNS 제어를 직접 수행하며, 기업용 인프라에서 가장 가치 있게 여겨지는 '서비스 연속성 보장'과 '재해 복구 시나리오'를 실무 수준으로 구현했습니다.
- **복합 네트워크 라우팅 체계 마스터:** 서로 다른 가상 사설망의 게이트웨이, 커스텀 라우팅 룰, 인바운드 보안 그룹을 체계적으로 엮어내며 하이브리드 대규모 인프라의 트래픽 흐름을 주도적으로 통제하는 능력을 배양했습니다.
