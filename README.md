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
- parameter(random_state=2024, n_estimators=100, max_depth=10)를 설정하여 xgboost 모델 fitting(RMSE: 597.05)
- parameter(random_state=2024, n_estimators=100, max_depth=10)를 설정하여 randomforest 모델 fittint(RMSE: 560.46)
- optuna 활용한 parameter('learning_rate': 0.04612756986536774, 'max_iter': 931, 'max_depth': 16, 'min_samples_leaf': 41, 'l2_regularization': 0.5793182507718159)를 설정하여 hgb 모델 fitting(RMSE: 543.58)
- optuna 활용한 parameter 'n_estimators': 288,'learning_rate': 0.027331707485934616,'num_leaves': 20,'max_depth': 13,'min_child_samples': 54,'subsample': 0.46324648447514566,'colsample_bytree': 0.5146306552485138)를 설정하여 lgbm모델 fitting(RMSE: 543.34)
- optuna를 활용한 parameter
  {lgb_params = {
    'num_leaves': 80,
    'learning_rate': 0.08856391656202889,
    'n_estimators': 138,
    'subsample': 0.7938571236989019,
    'colsample_bytree': 0.9016202740584827,
    'reg_alpha': 0.96095800250082,
    'reg_lambda': 0.5220401485447594,
    'min_child_samples': 21
}

  xgb_params = {
    'n_estimators': 987,
    'learning_rate': 0.06899495748113199,
    'max_depth': 3,
    'min_samples_split': 12,
    'min_samples_leaf': 3
}

  catboost_params = {
    'learning_rate': 0.2806971986577452,
    'iterations': 277,
    'depth': 4,
    'l2_leaf_reg': 9.706635385160684,
    'random_strength': 7.351833081184607,
    'bagging_temperature': 0.8959051902105583,
    'border_count': 20
}

  hgb_params = {
    'learning_rate': 0.09000242826110565,
    'max_iter': 267,
    'max_depth': 7,
    'min_samples_leaf': 8,
    'l2_regularization': 0.9394399140092757
}} 를 설정하여 최종모델 linear regression인 stacking 방식으로 fitting(RMSE:542.77)

- - optuna를 활용한 parameter
  {lgb_params = {
    'num_leaves': 80,
    'learning_rate': 0.08856391656202889,
    'n_estimators': 138,
    'subsample': 0.7938571236989019,
    'colsample_bytree': 0.9016202740584827,
    'reg_alpha': 0.96095800250082,
    'reg_lambda': 0.5220401485447594,
    'min_child_samples': 21
}

gb_params = {
    'n_estimators': 987,
    'learning_rate': 0.06899495748113199,
    'max_depth': 3,
    'min_samples_split': 12,
    'min_samples_leaf': 3
}

cat_params = {
    'learning_rate': 0.2806971986577452,
    'iterations': 277,
    'depth': 4,
    'l2_leaf_reg': 9.706635385160684,
    'random_strength': 7.351833081184607,
    'bagging_temperature': 0.8959051902105583,
    'border_count': 20
}

hgb_params = {
    'learning_rate': 0.09000242826110565,
    'max_iter': 267,
    'max_depth': 7,
    'min_samples_leaf': 8,
    'l2_regularization': 0.9394399140092757
}} 를 설정하여 최종모델 voting regressor방식으로 fitting(RMSE:541.85) -> 최종모델 선정

# 느낀점
- 다양한 모델링 방법과 앙상블 과정을 통해 코드 제출을 했지만 생각보다 높은 순위를 내지 못했다. EDA를 통해 적절한 전처리를 수행했어야 하는데 그러지 못해 뭔가 Feature Engineering하여 전처리 하는 과정에서 유의미한 결과를 내지 못했기 때문인 것 같다. 다음에는 EDA를 활용하여 유의미한 인사이트를 발굴하도록 더 데이터 시각화에 중점을 두어서 분석을 진행해 봐야 겠다.
