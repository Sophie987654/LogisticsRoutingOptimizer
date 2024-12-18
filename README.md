# LogisticsRoutingOptimizer

# **CJ 대한통운 미래기술 챌린지 2023: 실시간 주문 대응 Routing 최적화 알고리즘**

---

## **프로젝트 개요**
### **목표**
- 운행 중인 차량들에 그룹화된 실시간 주문을 연속적으로 할당하는 알고리즘 개발
- 총 차량 운영비용을 최소화하며, 주문 할당 및 경로 설정을 수행

### **결과물**
- 총 운영비용 최소화
- 제약사항을 만족하는 주문 처리 및 차량 배차 결과 산출

---

## **프로젝트 내용**
1. **알고리즘 개발**
   - 차량별 제약조건을 고려하여 실시간으로 배송 주문을 차량에 배정
   - 새로운 경로를 동적으로 산출하는 기능 포함

2. **주문 및 데이터 처리**
   - **주문 데이터 기간** : 7일
   - **주문 발생** : 6시간 간격으로 묶음 형태 제공 (총 24회, 1일 4회)
   - 마지막 날은 남은 물량 처리를 위해 주문이 발생하지 않음

3. **최적화 목표**
   - 차량 운영비용 최소화 (고정비용 + 거리비례 운송비용)

---

## **제공 데이터**
### **1. 차량 데이터**
- 차량번호, 최대 적재량, 출발 터미널 ID, 차량 고정비, 단위 거리당 운송비

### **2. 주문 데이터**
- 주문번호, 상차 터미널 ID, 착지 ID, CBM, 하차 가능시간

### **3. 터미널 데이터**
- 터미널 ID, 상차 가능 시간

### **4. 경로 데이터**
- 착지 및 터미널 간의 Origin-Destination (OD) matrix

---

## **제약 사항**
1. **최적화 라이브러리 사용 금지**
   - 라우팅 및 물량 배정 관련 라이브러리 사용 금지
2. **주어진 차량 및 기간 내 배송 완료**
   - 차량의 최대 CBM 초과 적재 불가
3. **노드 간 이동만 허용**
   - 경유지 변경 불가
4. **주문 배치 통합 처리 금지**
   - 미처리 물량은 잔류 가능하나, 발생 시각 기준 72시간 내 처리 필요

---

## **출력 포맷**
- **주문 처리 결과 파일** : 각 주문의 처리 여부 및 배차 내역 포함
- **차량 배차 결과 파일** : 각 차량의 이동 경로 및 운행 비용 포함

---

## **참고 사항**
- 제공된 터미널 및 착지 간 이동 경로 데이터를 활용하세요.
- 최적화 라이브러리 사용은 금지되며, 기본 알고리즘으로 문제를 해결해야 합니다.

---

## **프로젝트 개요**

### **목표**
- 운행 중인 차량들에 그룹화된 실시간 주문을 연속적으로 할당하는 알고리즘 개발
- 총 차량 운영비용을 최소화하며, 주문 할당 및 경로 설정을 수행

### **결과물**
- 총 운영비용 최소화
- 제약사항을 만족하는 주문 처리 및 차량 배차 결과 산출

---

## **아이디어**

### **Routing 전략**
- 차량 적재 한도(maxCBM)와 주문 적재량(CBM)을 고려하여, 동일 목적지 배송 시 적재 시간을 절감할 수 있는 최적화 방안 설계.
- 주문 데이터를 다음 4가지 유형으로 분류하여 처리:
  1. 동일 터미널 - 동일 목적지
  2. 다른 터미널 - 동일 목적지
  3. 동일 터미널 - 다른 목적지
  4. 다른 터미널 - 다른 목적지

### **차량 복귀 전략**
- 다른 터미널로 유동 복귀 가능하도록 설계.
- 복귀 터미널 선택 지표 :
  - 비용 = 거리 × (복귀 예상 차량 + 현재 상주 차량) / 해당 터미널 주문 수

### **72시간 내 모든 주문 처리 전략**
- 특정 시간 간격(interval) 설정 후, 차량 수급 불균형 발생 시 차량 이동 재배치를 통해 미처리 주문 방지.

---

## **구동환경**

- **Google Colab Pro**
- **주요 라이브러리 :**
  - pandas : 데이터 처리 및 분석
  - numpy : 수치 계산
  - datetime : 시간 데이터 처리
  - queue.PriorityQueue : 우선순위 큐 구현
  - math : 수학 연산
  - itertools : 조합 및 순열 계산
  - collections.defaultdict : 기본값 딕셔너리
  - google.colab.drive : 드라이브 마운트


---

## **데이터 전처리**

### **1. 주문 데이터 전처리**
- 주문 데이터를 정리하고 필요한 정보를 추출.
- 날짜 및 시간 데이터를 그룹화하여 처리.

### **2. 터미널 좌표 및 각도 정보 계산**
- 터미널 데이터를 활용해 하역 타임 윈도우 및 시계 방향 각도 계산.

---

## **객체 설명**

### **1. Order (주문 객체)**
- 주문 정보를 포함하며 처리 상태를 관리.
- **주요 필드 :**
  - 주문 ID, 터미널 ID, 목적지 ID, 적재량(CBM), 긴급 여부, 상태

### **2. Vehicle (차량 객체)**
- 차량의 정보, 경로, 주행 거리 등을 관리.
- **주요 필드 :**
  - 차량 ID, 현재 위치, 경로 리스트, 남은 적재 한도

### **3. Terminal (터미널 객체)**
- 터미널별 주문 및 차량 상태를 관리.
- **주요 필드 :**
  - 터미널 ID, 좌표(latitude, longitude), 주문 리스트, 상주 차량 수

---

## **알고리즘 개요**

### **1. 배송 우선순위**
1. SameTerSameDest : 동일 터미널-동일 목적지 주문 묶음 배송.
2. DiffTerSameDest : 다른 터미널-동일 목적지 최적 경로 계산.
3. SameTerDiffDest : 동일 터미널-다른 목적지 주문 조합 처리.
4. DiffTerDiffDest : 다른 터미널-다른 목적지 경로 기반 묶음 배송.

### **2. 하차 후 차량 복귀 알고리즘**
- 복귀 비용, 차량 수, 터미널 수요를 고려한 복귀 터미널 선택.

### **3. 시뮬레이션**
- 주문, 차량, 터미널 정보를 기반으로 한 시뮬레이션 구현.

---

## **알고리즘 설명**

### **1. 내부 함수**
- 데이터 가공 및 계산에 사용되는 주요 함수들:
  - 거리 계산 함수
  - 터미널 상태 업데이트 함수
  - 주문 상태 확인 함수

### **2. 배송 처리 함수**
- 입력으로 주문 데이터를 받아, 최적 경로 및 배송 결과 반환.
- **처리 단계 :**
  1. 주문 유형 분류
  2. 차량 적재 가능 여부 확인
  3. 경로 계산 및 배송 완료 처리
  4. 복귀 차량 최적화 처리
