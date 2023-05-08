# 도배 하자 유형 분류 AI 경진대회  
<https://dacon.io/competitions/official/236082/overview/description>    
<br />



# 1. 참여 계기  
최근 딥러닝에 관심이 많아 공부하던 도중, 데이콘에서 대회가 열려있기에 참가해 보았다.

입상 등 결과를 위해서라기 보다는, 근래 공부하였던 딥러닝 기법들을 내가 얼마나 스스로 사용할 수 있나 확인해보고 싶었다.



<br />




# 2. 참여 인원 / 기간
* 개인 프로젝트
* 2023년 4월 16일 ~ 2023년 4월 28일


<br />




# 3. 사용 기술
* Python
* Matplotlib
* Sklearn
* Pytorch
* Torchvision
* WeightedRandomSampler
<br />



# 4. 작업 환경
* Google Colab
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
* 손실함수로 FocalLoss를 적용하였더니, 약

<br />




# 6. 결과
* 평가 기준은 WeightedF1Score로, Baseline에서 주어진 스코어는 0.2201 이였다.
* 내가 처음으로 나온 스코어는 0.2645로, Baseline보다는 높지만 그래도 여전히 너무 낮은 결과였다.


<br />




# 7. 회고 / 느낀점
* 이번 대회는 사실 혼자서 어느정도까지 할 수 있을지 궁금해서 참여하게 된 것이지만, 나온 결과를 보고 실망하고 갈 길이 멀었다는 생각이 들었다.
* 하지만 딥러닝을 공부한 지 이제 겨우 한 달 정도이므로, 이번 대회의 상위 랭커의 코드가 나중에 공유가 된다면 반드시 그 코드들을 통해 더 깊게 공부해야겠다고 다짐했다.
* 아마 내 점수가 낮게 나온 이유는 과대적합 때문이라고 생각한다. 실제로 Validation까지는 점수가 매우 높게 나오다가, Test에서 점수가 터무니 없이 낮게 나온 걸 확인하였다.
* 과대적합을 막기 위해 여러가지 Augmentationd을 활용도 해보고, WeightedRandomSampler를 활용하면 이미지가 적은 라벨에서 너무 많이 추출되어 과대적합이 발생한다고 생각하여 라벨별로 수작업으로 일정 개수만큼 늘려보기도 하였지만, 여전히 과대적합이라는 문턱을 넘을 수가 없었다.
* 이번 대회는 특이하게도, 외부 데이터도 활용이 가능하다고 한다. 저작권이 없는 이미지라면, 데이터가 매우 적은 라벨에 외부에서 크롤링 해온 이미지를 활용할 수 있다는 뜻인데, 대회에서 이미지 라벨을 나눈 기준을 설명해 줄 때까지 보류하기로 하였다.
