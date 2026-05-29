# 김대연 _Kim Dae yeon_ 👋

> **"데이터 증가로 인한 성능·안정성 문제를 구조적으로 분석하고 해결합니다."**  
> 실행 계획 분석을 통한 쿼리 최적화, 동시성 제어, 아키텍처 개선을 통해 더 나은 사용자 경험을 만듭니다.

<!-- ===================== -->
<!-- Tech Stack (Top Area) -->
<!-- ===================== -->
![Java](https://img.shields.io/badge/Java-ED8B00?style=flat-square&logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-6DB33F?style=flat-square&logo=springboot&logoColor=white)
![Spring Security](https://img.shields.io/badge/Spring%20Security-6DB33F?style=flat-square&logo=springsecurity&logoColor=white)
![JPA](https://img.shields.io/badge/JPA-59666C?style=flat-square&logo=hibernate&logoColor=white)
![React](https://img.shields.io/badge/React-20232A?style=flat-square&logo=react&logoColor=61DAFB)
![Next.js](https://img.shields.io/badge/Next.js-000000?style=flat-square&logo=nextdotjs&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=flat-square&logo=postgresql&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=flat-square&logo=mysql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=flat-square&logo=redis&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-232F3E?style=flat-square&logo=amazon-aws&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![Kafka](https://img.shields.io/badge/Kafka-231F20?style=flat-square&logo=apachekafka&logoColor=white)
![Git](https://img.shields.io/badge/Git-F05032?style=flat-square&logo=git&logoColor=white)

## 🚀 Core Competencies

- **Performance Optimization**  
  실행 계획·인덱스 분석을 통해 조회 성능 **60~78% 개선**

- **Concurrency Control**  
  비관적 락과 상태 전이 검증으로 **동시성 오류 0% 달성**

- **System Architecture**  
  모듈 분리·MSA 설계로 장애 전파 차단

- **DevOps & Automation**  
  CI/CD·모니터링 자동화로 운영 안정성 강화

---

## 📂 Featured Projects

<details>
<summary><strong>StayOneKorea – 위치 검색·결제 동시성 개선</strong></summary>

- **기간**: 2025.10 ~ 2025.12  
- **역할**: 풀스택 엔지니어 (팀장)  
- **Tech Stack**: Spring Boot, PostgreSQL(PostGIS), Next.js, Cloudflare R2  

### 프로젝트 개요
외국인 사용자를 대상으로 고시원·단기 숙박을 연결하는 위치 기반 숙박 플랫폼입니다.  
검색과 결제는 사용자의 첫 이탈 지점이 될 수 있는 핵심 흐름으로, **데이터 증가에 따른 성능 저하와 비동기 결제 처리의 안정성 확보**가 주요 과제였습니다.

### 문제
- 좌표 기반 거리 계산을 매 요청마다 수행하면서 **공간 인덱스가 활용되지 않아 검색 응답 지연**
- `DISTINCT` 사용으로 인한 디스크 스필과 Full Scan 발생
- 결제 완료(Paid)·취소(Cancelled) 웹훅이 **비동기적으로 도착하며 Race Condition 발생**
- 동일 결제 건의 상태가 잘못 덮어써지는 치명적인 데이터 정합성 문제 존재

### 해결
- 좌표 컬럼을 `geometry(Point, 4326)` 타입으로 변경하고 **GIST 공간 인덱스 적용**
- `ST_DWithin` 기반 검색으로 인덱스 활용도를 극대화하고 불필요한 DISTINCT 제거
- 결제 조회 단계에 **비관적 락(Pessimistic Lock)** 적용으로 트랜잭션 순차 처리
- 상태 전이 검증 로직을 추가해 **올바르지 않은 상태 변경을 구조적으로 차단**

### 결과
- 위치 검색 성능 **1.8s → 0.4s (78% 개선)**
- 검색 처리량 **3.4배 향상**
- 결제 동시성 오류율 **10% → 0%**
- 실사용 환경에서도 결제·검색 흐름의 안정성 확보

</details>

---

<details>
<summary><strong>프차천국 – 조회 응답 개선·데이터 모델 고도화</strong></summary>

- **기간**: 2025.08 ~ 2026.01  
- **역할**: 풀스택 엔지니어  
- **Tech Stack**: Spring Boot, MySQL, Caffeine Cache, Algolia  

### 프로젝트 개요
예비 창업자를 위한 맞춤형 프랜차이즈 추천 플랫폼입니다.  
추천 정확도를 높이기 위해 데이터 구조가 복잡해지면서 **조회 성능 저하와 유지보수 비용 증가** 문제가 발생했습니다.

### 문제
- 3배수 구조의 테이블이 최대 33개까지 확장되며 **중복 조인과 복잡한 조회 로직 발생**
- 우선순위 계산을 조회 시점에 수행해 요청마다 연산 비용 증가
- 추천 브랜드 API 호출 시 반복적인 DB I/O로 **평균 응답 시간 3.8초**
- 엔티티 캐싱 구조에서 `LazyInitializationException` 등 동시성 이슈 발생

### 해결
- 15개 테이블을 4개로 통합하는 **역정규화 모델 설계**
- 우선순위 계산을 조회 시점이 아닌 **저장 시점(Write-Time)**으로 이동
- 추천 결과를 **Caffeine Local Cache + DTO 기반 캐싱 구조**로 재설계
- 캐시와 조회 책임을 분리해 동시성 문제 제거

### 결과
- 추천 API 응답 속도 **3.8s → 37ms**
- 일반 조회 응답 **900ms → 300ms (3배 개선)**
- 유지보수 작업량 **67% 절감**
- 추천 트래픽 증가 상황에서도 안정적인 응답 성능 확보

</details>

---

<details>
<summary><strong>MOIM – 대용량 알림 시스템</strong></summary>

- **기간**: 2025.04 ~ 2025.05  
- **역할**: 백엔드 엔지니어  
- **Tech Stack**: Spring Boot, Kafka, MongoDB, Redis  

### 프로젝트 개요
모임 생성·참여·알림 기능을 제공하는 커뮤니케이션 플랫폼입니다.  
알림 트래픽이 급격히 증가하면서 **저장·삭제·조회 전반에서 성능 병목**이 발생했습니다.

### 문제
- JPA `saveAll()` 기반 단건 INSERT 반복으로 알림 저장 TPS 저하
- 대량 알림 삭제 시 DELETE 쿼리로 인한 락 대기 발생
- 조회 시 불필요한 범위 스캔으로 응답 지연

### 해결
- 알림 저장을 **JDBC Batch Insert** 기반으로 전환해 트랜잭션 오버헤드 제거
- 주 단위 **Range Partitioning** 적용 후 `DROP PARTITION` 방식으로 데이터 삭제
- 파티션 프루닝을 활용해 조회 범위 최소화

### 결과
- 알림 저장 TPS **0.9 → 25 (25배 향상)**
- 데이터 삭제 속도 **14배 개선**
- 조회 성능 **6.5배 향상**
- 대규모 알림 트래픽에서도 안정적인 시스템 구조 확보

</details>

---

<details>
<summary><strong>Unearth – 백오피스 구축 및 데이터수집 안정화 (인턴)</strong></summary>

- **기간**: 2025.07  
- **역할**: 백엔드 개발자  
- **Tech Stack**: Django, Spring Boot, MySQL  

### 프로젝트 개요
음원 데이터를 수집·분석하는 B2B 백오피스 시스템입니다.  
데이터 수집 실패가 잦았으나 즉시 인지되지 않아 **운영 대응이 지연되는 구조적 문제**가 있었습니다.

### 문제
- 데이터 수집 실패 발생 시 운영자가 수시간 후에 인지
- 실패 원인 추적이 어려워 재발 방지에 한계
- 루프 기반 반복 조회로 인한 N+1 문제 발생

### 해결
- 실패 로그 테이블 설계 및 **자동 재시도 로직** 구현
- **Slack Webhook 기반 실시간 장애 알림 체계** 구축
- Fetch Join 및 일괄 조회 구조로 조회 로직 개선
- Django 크롤러와 Spring Boot 백오피스를 분리해 장애 영향 범위 최소화

### 결과
- 크롤링 실패율 **40% → 8%**
- 장애 대응 시간 **3시간 → 20분**
- 조회 성능 **500ms → 200ms (60% 개선)**
- 운영자가 즉시 대응 가능한 안정적인 수집 시스템 구축

</details>

---
<!--
## 🏆 Awards

- **세종 DX 해커톤 대상** (2024) – 문화관광 서비스
- **ESG 프로젝트 공모전 대상** (2023)
- **고려대 창업 경진대회 우수상** (2024)
- **T-SUM AI 경진대회 최우수상** (2024)
- **미래모빌리티 아이디어 공모전 대상** (2023, CES 참관)

---

## 🌱 Activities
- 세종사회봉사단 3기(2022/12/20 - 2023/12/20) - 고려대학교 세종캠퍼스 사회봉사단
- 한국장학재단 하계 재능 캠프(2022/08/01 - 2022/08/07) - 현덕언더스쿨지역아동센터 재능 캠프  
- Nexon Project MOD supporters (2022/07/04 - 2022/09/03) - Nexon & 멋쟁이사자처럼 서포터즈
- Like Lion 10th-11th | Underdog Rev (2022/03/16 ~ 2024/12/31) - 고려대학교 세종캠퍼스 소속 IT 동아리 운영진

---

## 📞Contact
[![Email](https://img.shields.io/badge/Email-kim946509%40gmail.com-d14836?style=flat-square&logo=gmail&logoColor=white)](mailto:kim946509@gmail.com)
[![Tech Blog](https://img.shields.io/badge/Tech%20Blog-AgongStory-orange?style=flat-square&logo=tistory&logoColor=white)](https://agongstory.tistory.com)
[![GitHub](https://img.shields.io/badge/GitHub-kim946509-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/kim946509)
-->
