# 도배 하자 유형 분류 AI 경진대회  
<https://dacon.io/competitions/official/236082/overview/description>  


<br />



# 1. 참여 계기  
빅콘테스트에서 개최한 데이터분석 퓨처스리그의 핀다앱 사용성 데이터를 통한 대출 여부 예측을 팀 프로젝트로 하기로 하였다.  
최종 정리 및 발표 자료는 pdf 파일에 저장하였다.


<br />




# 2. 참여 인원 / 기간
* 팀 프로젝트 (4인)
* 2022년 9월 1일 ~ 2023년 12월 1일


<br />




# 3. 사용 기술
* Python
* SQL
* Matplotlib
* Sklearn


<br />



# 4. 작업 환경
* Jupyter Notebook (CPU)
* Google Colab (GPU)


<br />



# 5. 프로젝트 진행 과정
<br />



## 1. 전처리
* LabelEncoder를 통해 데이터의 라벨들을 숫자로 인코딩 후, plt.imshow를 이용해 이미지 데이터들을 시각화 하였다.
* 데이터들의 라벨을 확인하였더니, 라벨별로 불균형이 매우 심한걸로 나타나 불균형을 어떻게 처리하는지가 관건일 것으로 보였다.

<br />


## 2. 커스텀 데이터셋 설정
* 이번 대회에서 데이터들은 분류하기 쉽도록 각 라벨별로 폴더안에 이미지들이 저장되어 있었기에, 원래라면 Torchvision의 ImageFolder를 이용하여 손쉽게 이미지를 불러오는 것이 가능하다.
* 그러나, ImageFolder는 데이터를 불러오는 속도가 매우 느리기 때문에, 추후에 모델을 학습할때 시간이 많이 소모될 것으로 예상되어, 커스텀 데이터셋을 만들었다.
* 이미지가 들어있는 폴더의 경로를 통해, 이미지와 라벨을 리턴하는 커스텀 데이터셋을 만든 뒤, DataLoader에서 가장 작업 환경에 걸맞는 num_workers를 찾아주었다.

<br />


## 3. 이미지 증강
* 앞서 언급했듯이, 이미지 라벨별로 데이터의 수가 매우 상이하다. 가장 적은 데이터는 3개, 많은 데이터는 1400여개로 데이터 수가 매우 차이가 나므로, WeightedRandomSampler를 활용하여 이미지 불균형을 해소하고자 하였다.
* 단순히 이미지 수만 늘리면 과적합이 되므로, Augmentation을 활용하여 이미지를 변형해가며 데이터를 늘렸다.


<br />


## 4. 모델 설정
* 모델은 베이스라인에서 주어져 있던 efficientnetb0를 사용하였다.

<details>
<summary>모델 구조</summary>
 
![efficientnetb0](https://user-images.githubusercontent.com/131629615/235849301-1c2c6ee0-db99-4419-bd4c-671d940a7c9b.png)

</details>


<br />


## 5. 사용한 기법
* 앞서 라벨별로 증강한 이미지를 원본 데이터와 합친 뒤, Stratified K-Fold를 사용하여 라벨별로 균등하게 5개의 Fold로 나누어 교차검증을 진행하였다.
* 손실함수로는 CrossEntropyLoss에 Weight를 부여하여 사용하였다.
* 최적화를 위한 옵티마이저는 이미지 분류 문제에 흔히 쓰이는 Adam을 이용하였다.
* 모델 학습 속도 증가를 위해 AMP와 GradScaler를 사용하였다.


<br />




# 6. 결과
* 평가 기준은 WeightedF1Score로, Baseline에서 주어진 스코어는 0.2201 이였다.
* 내가 처음으로 나온 스코어는 0.2645로, Baseline보다는 높지만 그래도 여전히 너무 낮은 결과였다.


<br />




# 7. 회고 / 느낀점
# 파일 순서
preprocessing -> EDA -> 표준화,PCA -> 오버샘플링 -> 모델링 -> 앙상블
