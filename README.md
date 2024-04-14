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
- 범주형 변수['Gender','Education_Status','Employment_Status','Industry_Status','Occupation_Status','Race','Hispanic_Origin','Martial_Status','Household_Status','Household_Summary','Citizenship','Birth_Country','Birth_Country (Father)','Birth_Country (Mother)','Tax_Status','Income_Status')들을 countplot으로 확인 (학력 수준이 높을수록, 백인, 기혼, 미국인일수록 수가 많은 것 확인)
- 수치형 변수('Age', 'Working_Week (Yearly)', 'Gains', 'Losses', 'Dividends', 'Income')들을 countplot으로 확인
- 수치형 변수들의 상관관계 heatmap을 통해 확인(working_week(yearly)와 income이 0.42로 그나마 높은 상관관계를 보이는 것을 확인)
- Education_Status별 평균 income barplot을 통해 확인(교육수준이 높을수록 평균 income이 높은 것을 확인)
- Marital_Status별 평균 income barplot을 통해 확인(Married(Armed Force Spouse가 평균 income이 가장 높은 것을 확인)
- Industry_Status별 평균 income barplot을 통해 확인(Utilities & Sanitary가 평균 income이 가장 높은 것을 확인)
- Race, Occupation_Status별 평균 income barplot을 통해 각각 확인(White, Technicians&Support가 평균 income이 가장 높은 것을 확인)
- (Race, Education_Status)별 (Gender, Employment_Status)별 (Race, Gender)별 (Gender, Education_Status)별 평균 income barplot을 통해 확인 (Gender의 경우 M일 때 상대적으로 F보다 평균 income이 높은 것을 확인)


  

# 데이터 전처리
- "Employment_Status", "Industry_Status", "Occupation_Status", "Race", "Hispanic_Origin", "Martial_Status", "Household_Summary","Citizenship", "Tax_Status","Income_Status","Education_Status","Birth_Country" 범주형 변수들을 Label Encoding 처리
- Gender 변수 M=1, F=0으로 처리
- 수치형 변수들의 조합으로 'Age_Working_Week','Gains_Losses', 'Age_Dividends', 'Age_Losses', 'Working_Week_Dividends', 'Gains_Dividends', 'Working_Week_Losses', 'Age_Working_Week_Gains', 'Working_Week_Gains_Losses' Feature Engineering을 통해 생성

  

# 모델링
- parameter(random_state=2024, n_estimators=200, max_depth=12)를 설정하여 randomforest 모델 fitting(RMSE: 3.02)
- parameter(random_state=2024, n_estimators=100, max_depth=10)를 설정하여 catboost 모델 fittint(RMSE: 3.02)
- parameter(random_state=2024, n_estimators=100, max_depth=10)를 설정하여 xgboost 모델 fittint(RMSE: 3.13)
- parameter(random_state=2024, learning_rate=0.05, max_depth=20, min_child_samples=30, n_estimators=180, num_leaves=40)를 설정하여 lgbm 모델 fitting(RMSE: 2.95) -> 최종모델 선정
- optuna 방법을 활용하여 하이퍼파라미터튜닝(learning_rate=0.05629447251416819, max_depth=18, min_child_samples=12, n_estimators=146, num_leaves=35) 수행 후 lgbm 모델 fitting
- randomforest(max_depth=15, random_state=2024), lgbm(learning_rate=0.05, max_depth=20, min_child_samples=30, num_leaves=40,random_state=202),  catboost((random_state=2024, n_estimators=100, max_depth=10, learning_rate=0.1) 설정 후 stacking model(최종모델 lgbm(random_state=2024, n_estimators=100, max_depth=20, learning_rate=0.05, num_leaves=40, min_child_samples=30)) fitting(RMSE: 2.99)

# 느낀점
- 단일 모델이 아닌 ENSEMBLE방식을 다양하게 활용하여 모델링을 진행했으면 더 좋은 성과가 나오지 않았을까 생각한다. 다음 대회에 참여하게 된다면 다양한 ENSEMBLE 방법을 적용하여 더 성능이 높은 모델을 만들도록 해야겠다.
