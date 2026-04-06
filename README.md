# AR Aging Risk Analyzer

## Project Overview

이 프로젝트는 매출채권 데이터를 SQL과 Python으로 분석하여, AR Aging 구간별 채권 현황과 장기 미수 위험 고객을 식별하는 것을 목표로 합니다.

고객별 채권을 `0~30일`, `31~60일`, `61~90일`, `91일 이상` 구간으로 나누고, 고객 단위 및 고객 등급 단위에서 위험도를 요약합니다. 또한 SQL 결과를 CSV 파일로 저장하고, 고위험 고객의 장기 미수 금액을 시각화하여 단순 거래 집계가 아닌 채권 건전성 관점의 분석 결과를 제공합니다.

이 프로젝트는 단순히 매출 데이터를 합산하는 데서 끝나지 않고, 장기 미수 비중이 높은 고객을 식별하여 회수 우선순위 판단에 활용할 수 있는 기초 분석 구조를 만드는 데 목적이 있습니다.

## Why It Matters

매출채권은 회수 지연 기간이 길어질수록 현금흐름과 채권 회수가능성 측면에서 리스크가 커질 수 있습니다.

특히 단순 매출 규모만으로는 실제 위험도를 충분히 설명할 수 없기 때문에, 채권을 Aging bucket으로 나누고 장기 미수 비중을 함께 보는 것이 중요합니다. 이 프로젝트는 고객별 위험도와 고객 등급별 위험 패턴을 함께 요약하여, 실제 채권 관리 관점에서 의미 있는 정보를 도출하는 것을 목표로 합니다.

## Tech Stack

- Python
- SQLite
- pandas
- matplotlib

## Project Structure

```text
ar-aging-risk-analyzer/
├── practice.db
├── setup_data.py
├── main.py
├── test.py
├── sql/
│   ├── 01_customer_aging_summary.sql
│   ├── 02_grade_risk_summary.sql
│   └── 03_high_risk_customers.sql
├── output/
│   ├── customer_aging_summary.csv
│   ├── grade_risk_summary.csv
│   ├── high_risk_customers.csv
│   └── high_risk_customers_chart.png
└── README.md
```

## Key Features

- 프로젝트용 샘플 고객 및 거래 데이터를 SQLite DB에 재구성
- 고객별 AR Aging 요약 리포트 생성
- 고객 등급별 위험 고객 수 및 장기 미수 비중 요약
- 고위험 고객 리스트 추출
- SQL 결과를 CSV 파일로 저장
- 고위험 고객 장기 미수 금액 시각화

## 주요 SQL 분석 결과

1) 고객별 AR Aging 요약

고객별 총 채권 금액을 다음 구간으로 나누어 요약합니다.

- 0~30일
- 31~60일
- 61~90일
- 91일 이상

또한 61일 이상 장기 미수 금액(over_61_total_amount)을 기준으로 위험 고객 여부(risk_flag)와 장기 미수 비율(risk_ratio)을 계산합니다.

2) 고객 등급별 위험 요약

고객 등급별로 다음 지표를 계산합니다.

- 전체 고객 수
- 총 채권 금액
- 61일 이상 장기 미수 금액
- 위험 고객 수
- 위험 고객 비율
- 장기 미수 금액 비율

3) 고위험 고객 리스트

61일 이상 장기 미수 금액이 존재하는 고객만 추출하여, 장기 미수 비중이 높은 순서대로 정렬합니다.

## Key Insights

- Basic과 VIP 등급 모두 전체 고객의 50%가 위험 고객으로 분류되어, 특정 등급에만 국한된 문제가 아니라 전반적인 채권 관리 필요성이 확인되었습니다.
- 다만 61일 이상 장기 미수 금액 비중은 VIP 등급이 더 높게 나타나, 위험 고객 수와 금액 기준 리스크는 다르게 해석할 필요가 있음을 확인할 수 있었습니다.
- 개별 고객 기준으로는 장기 미수 비율이 가장 높은 고객과 장기 미수 금액이 가장 큰 고객이 서로 달라, 채권 관리 시 비율과 금액을 함께 고려해야 함을 확인할 수 있었습니다.

## Detailed Analysis

- Shin 고객은 장기 미수 비율이 가장 높아 채권 건전성 측면에서 가장 취약한 고객으로 볼 수 있었습니다.
- Lee 고객은 장기 미수 금액이 가장 커, 회수 우선순위 관점에서 가장 중요한 고객 중 하나로 해석할 수 있었습니다.
- Park 고객은 장기 미수 비율이 극단적으로 높지는 않지만 금액이 적지 않아, 비율과 금액을 함께 고려해야 하는 혼합형 위험 고객으로 볼 수 있었습니다.

## How to Run

1. 데이터베이스 재구성
```
python3 setup_data.py
```
2. 결과 파일 생성
```
python3 main.py
```
3. SQL 테스트 실행
```
python3 test.py
```

## Generated Result

- output/customer_aging_summary.csv
- output/grade_risk_summary.csv
- output/high_risk_customers.csv
- output/high_risk_customers_chart.png

## What I Learned

- SQL의 CTE, CASE WHEN, 조건부 집계를 활용해 실무형 리포트를 구성하는 방법
- LEFT JOIN과 COALESCE를 이용해 주문 없는 고객까지 포함하는 방식
- SQLite 결과를 pandas DataFrame으로 읽어 CSV로 저장하는 방법
- Python과 SQL을 연결해 분석 결과를 자동화된 산출물로 만드는 흐름
- 단순 거래 집계가 아니라 채권 건전성 관점에서 데이터를 해석하는 방법
