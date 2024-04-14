# 데이콘 AI 경진대회4

# 일시
- 2024-03-11 ~ 2024-04-08

# 주제
- 소득 예측 AI 해커톤

# 목적
- 개인 정보 데이터를 활용하여 소득 예측
 

# 데이터
- https://dacon.io/competitions/official/236230/data(데이터 출처)
- 데이터 개수 : 20000개
  
| Column                  | Non-Null Count   | Dtype   |
|----------------------|------------------|---------|
| ID                      | 20000 non-null  | object  |
| Age                     | 20000 non-null  | int64   |
| Gender                  | 20000 non-null  | object  |
| Education_Status        | 20000 non-null  | object  |
| Employment_Status       | 20000 non-null  | object  |
| Working_Week (Yearly)   | 20000 non-null  | int64   |
| Industry_Status         | 20000 non-null  | object  |
| Occupation_Status       | 20000 non-null  | object  |
| Race                    | 20000 non-null  | object  |
| Hispanic_Origin         | 20000 non-null  | object  |
| Martial_Status          | 20000 non-null  | object  |
| Household_Status        | 20000 non-null  | object  |
| Household_Summary       | 20000 non-null  | object  |
| Citizenship             | 20000 non-null  | object  |
| Birth_Country           | 20000 non-null  | object  |
| Birth_Country (Father)  | 20000 non-null  | object  |
| Birth_Country (Mother)  | 20000 non-null  | object  |
| Tax_Status              | 20000 non-null  | object  |
| Gains                   | 20000 non-null  | int64   |
| Losses                  | 20000 non-null  | int64   |
| Dividends               | 20000 non-null  | int64   |
| Income_Status           | 20000 non-null  | object  |
| Income                  | 20000 non-null  | int64   |




  

# 사용언어/ 최종 선정 모델
- python/ votingregressor(lgbm+gb+hgb+catboost)

# 모델 성능 지표
- RMSE

# EDA
- 범주형 변수('Gender', 'Educaton_Statue', 'subcontinent', 'country', 'traffic_source','device', 'continent', 'traffic_medium')들을 countplot으로 확인
- 범주형 변수별 평균 target(조회수) countplot으로 확인
- 수치형 변수('new', 'quality', 'duration', 'bounced', 'transaction', 'transaction_revenue')들을 countplot으로 확인
- new, bounced의 평균 조회수를 barplot으로 확인(new, bounced의 경우 0일 때가 1일 때보다 더 많음)
- 수치형 변수들의 상관관계 heatmap을 통해 확인(transaction과 transaction_revenue가 0.46, transaction과 quality가 0.48로 그나마 높은 상관관계를 보이는 것을 확인)
- continent별 new와, bounced의 평균 조회수 barplot으로 확인(모든 continent가 new가 0일 때 1일 때보다 평균 조회수가 높음-> 접속 경험이 있는 이용자들은 처음 접속한 이용자들보다 조회수가 높음)
- continent별 quality의 평균 조회수 barplot 확인(america의 경우 quality의 수가 높아질수록 tatget이 높아지는 경향을 보임)
- continent별 transaction의 평균 조회수 barplot 확인(transaction이 높을수록 평균 조회수가 높아지는 경향을 보임)
- device별 new의 평균 조회수 barplot으로 확인(desktop은 new가 0일 때 평균 조회수가 더 높고 mobile과 tablet은 1일 때 더 높음)
- device별 bounced의 평균 조회수 barplot으로 확인(device모두 new가 0일 때 압도적으로 높음)
- device별 transaction의 평균 조회수 barplot으로 확인(device 모두 transaction이 높을수록 평균 조회수가 높음)
- traffic_medium별 new, bounced의 평균 조회수 barplot으로 확인(모든 traffic_medium이 newr가 0일 때 평균 조회수가 높음)
- traffic_medium별 quality,transaction의 평균 조회수 barplot으로 확인(모든 traffic_medium이 quality가 높아질수록 평균 조회수가 높음)


  

# 데이터 전처리
- referral_path와 keyword 범주형 변수 ('Category1', 'Others', 'Category2', 'Category11', 'Category3','Category10', 'Category6', 'Category8', 'Category5', 'Category12','Category13', 'Category7', 'Category9', 'Category4') 이와 같은 범주 형태 가지도록 처리
- 'browser', 'OS', 'device', 'country','continent', 'subcontinent', 'traffic_source', 'traffic_medium', 'referral_path', 'keyword' 범주형 변수들 원핫인코딩 처리
- 'quality', 'duration', 'transaction', 'transaction_revenue' 변수 standard scaler를 활용하여 표준화
- 수치형 변수들의 조합으로 22개 변수 추가 생성(feature engineering)
  

# 모델링
- parameter(random_state=2024, n_estimators=200, max_depth=12)를 설정하여 randomforest 모델 fitting(RMSE: 3.02)
- parameter(random_state=2024, n_estimators=100, max_depth=10)를 설정하여 catboost 모델 fittint(RMSE: 3.02)
- parameter(random_state=2024, n_estimators=100, max_depth=10)를 설정하여 xgboost 모델 fittint(RMSE: 3.13)
- parameter(random_state=2024, learning_rate=0.05, max_depth=20, min_child_samples=30, n_estimators=180, num_leaves=40)를 설정하여 lgbm 모델 fitting(RMSE: 2.95) -> 최종모델 선정
- optuna 방법을 활용하여 하이퍼파라미터튜닝(learning_rate=0.05629447251416819, max_depth=18, min_child_samples=12, n_estimators=146, num_leaves=35) 수행 후 lgbm 모델 fitting
- randomforest(max_depth=15, random_state=2024), lgbm(learning_rate=0.05, max_depth=20, min_child_samples=30, num_leaves=40,random_state=202),  catboost((random_state=2024, n_estimators=100, max_depth=10, learning_rate=0.1) 설정 후 stacking model(최종모델 lgbm(random_state=2024, n_estimators=100, max_depth=20, learning_rate=0.05, num_leaves=40, min_child_samples=30)) fitting(RMSE: 2.99)

# 느낀점
- 단일 모델이 아닌 ENSEMBLE방식을 다양하게 활용하여 모델링을 진행했으면 더 좋은 성과가 나오지 않았을까 생각한다. 다음 대회에 참여하게 된다면 다양한 ENSEMBLE 방법을 적용하여 더 성능이 높은 모델을 만들도록 해야겠다.
