# 3-1 교내 프로젝트 Remaster

## 프로젝트 개요
- 주제: 국민건강영양조사 데이터를 활용한 비만 예측 모델 생성
- 데이터: 국민건강영양조사 원시자료 2008년, 2009년 데이터
- 목적: 기본정보를 통해 체지방률 기준에 따른 비만 여부 판단 (정상, 과체중, 비만)

## 파일 구조

### 1. data_preparation.ipynb
- sas 파일 로드
- ID를 KEY로 년도별 기본 DB, 체지방DB 병합
- 년도별 DB를 하나의 DB로 병합
- target 값 생성('fat')
- 성별에 따른 체지방률('DW_WBT_pFT') 기준표
  - 남자: 정상(16.7~22.5), 과체중(22.5~24.8), 비만(24.8 이상)
  - 여자: 정상(28.0~34.3), 과체중(34.3~36.49), 비만(36.5 이상)
- 체지방률을 기준으로 한 target 특성인 'fat' 컬럼을 추가
  - 0: 정상, 1: 과체중, 2: 비만
- 'data_set.csv' 생성

### 2. data_profiling.ipynb
- 결측치 제거
- 정상, 과체중, 비만을 같은 개수만큼 샘플링하여 'sampled_data.csv' 생성
- pandas_profiling과 seaborn 라이브러리를 이용하여 데이터 경향 파악
- 문제점 발견: "특성의 고유값마다 fat의 값(정상, 과체중, 비만) 비율이 비슷한 컬럼이 대다수"

### 3. feature_filterling.ipynb
- 변별력이 낮은 특성을 걸러내는 함수 작성
   - mse_col_filter(ratio_data, threshold): 각 특성의 고유값별 fat 값 비율을 구하고, 그 비율의 평균 계산
   - 평균 비율과 개별 비율 간의 mse 값을 계산하여 filtering (임계값: 0.001)
   - 연속형 컬럼과 명목형 컬럼을 구분하고 pickle로 최종 특성 리스트 저장

### 4. modeling.ipynb
- 원핫인코딩과 min_max_scaling 수행 후 xgboost 모델 학습 후 성능 평가 (f1 score)
- 다중 분류 고려하여 딥러닝 모델까지 학습

### 5. regression.ipynb
- 동일한 데이터셋을 활용하여 체지방률 예측을 목표로 회귀모델 설계

### function_file.ipynb
- 모델 성능 평가용 함수 모음 파일

### total_list.pkl
- 명목형, 연속형 컬럼을 구분한 리스트
