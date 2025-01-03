# 데이터 수집 📊

## 프로젝트 개요
이 프로젝트는 서울 및 수도권 지역의 대기질과 공중 보건 간의 상호작용을 분석하기 위한 데이터 수집 절차를 설명합니다. .
Python, Pandas, SQL을 활용하여 데이터를 수집, 저장 및 관리합니다.

---

## 데이터 소스 및 수집 방법 🌐

### 1. 대기질 데이터 🌍

#### (1) 서울특별시 대기환경정보
- **웹사이트**: [서울특별시 대기환경정보](https://cleanair.seoul.go.kr)
- **수집 방법**:
  1. 웹사이트 접속 후 "대기환경정보 > 자료실 > 대기오염 통계" 선택.
  2. PM2.5, PM10, NO₂ 데이터를 연도별로 선택하여 CSV 또는 Excel 파일로 다운로드.
  3. Python으로 데이터 불러오기:
     ```python
     import pandas as pd
     air_quality_data = pd.read_csv('path/to/air_quality.csv', encoding='utf-8')
     ```
  4. SQL에 저장:
     ```python
     import sqlite3
     conn = sqlite3.connect('air_quality.db')
     air_quality_data.to_sql('air_quality', conn, if_exists='replace', index=False)
     ```

#### (2) 서울 열린데이터광장
- **웹사이트**: [서울 열린데이터광장](https://data.seoul.go.kr)
- **수집 방법**:
  1. "실시간 자치구별 대기환경 현황" 데이터셋 검색 후 선택.
  2. API 키 발급받기: "마이페이지 > API 키 관리"에서 키 생성.
  3. Python으로 데이터 요청:
     ```python
     import requests
     url = "https://openapi.seoul.go.kr:8088/{API_KEY}/json/ListAirQualityByDistrict/1/1000/"
     response = requests.get(url)
     data = response.json()
     air_quality_df = pd.DataFrame(data['ListAirQualityByDistrict']['row'])
     ```

#### (3) OpenAQ
- **웹사이트**: [OpenAQ](https://openaq.org)
- **수집 방법**:
  1. OpenAQ API 문서를 참조: [OpenAQ API](https://docs.openaq.org/).
  2. Python으로 데이터 요청:
     ```python
     import requests
     url = "https://api.openaq.org/v2/measurements"
     params = {
         "country": "KR",
         "city": "Seoul",
         "parameter": "pm25",
         "date_from": "2013-01-01",
         "date_to": "2023-12-31",
         "limit": 10000
     }
     response = requests.get(url, params=params)
     data = response.json()
     openaq_data = pd.DataFrame(data['results'])
     ```

---

### 2. 질병 데이터 🏥

#### (1) 질병관리청
- **웹사이트**: [질병관리청](https://www.kdca.go.kr)
- **수집 방법**:
  1. "통계자료 > 질병 발생 통계" 메뉴로 이동.
  2. 호흡기 질환 데이터를 CSV 또는 Excel 파일로 다운로드.
  3. Python으로 데이터 읽기:
     ```python
     health_data = pd.read_excel('path/to/health_data.xlsx')
     ```

#### (2) 공공데이터포털
- **웹사이트**: [공공데이터포털](https://www.data.go.kr)
- **수집 방법**:
  1. "건강보험심사평가원_질병 통계정보" 데이터셋 검색.
  2. API 키 발급 후 Python으로 요청:
     ```python
     url = "https://api.data.go.kr/openapi/health-statistics"
     params = {
         "serviceKey": "YOUR_API_KEY",
         "type": "json",
         "diseaseType": "호흡기 질환",
         "startYear": 2013,
         "endYear": 2023
     }
     response = requests.get(url, params=params)
     data = response.json()
     health_df = pd.DataFrame(data['response']['body']['items'])
     ```

---

## 데이터 저장 📂

### 디렉터리 구조
수집된 데이터는 아래와 같은 디렉터리 구조로 관리합니다:
```plaintext
project/
├── data/
│   ├── raw/                # 원본 데이터
│   │   ├── air_quality/    # 대기질 데이터
│   │   └── health/         # 질병 데이터
│   ├── processed/          # 전처리된 데이터
│   │   ├── air_quality/    
│   │   └── health/
│   └── metadata/           # 데이터 설명 및 출처 정보
└── scripts/                # 데이터 수집 스크립트
