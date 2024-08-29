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
* 단순히 이미지 수만 늘리면 과적합이 되므로 Augmentation을 활용하여 이미지를 변형해가며 데이터를 늘렸다.


<br />


## 4. 모델 설정
* 모델은 베이스라인에서 주어져 있던 efficientnetb0에서 더 높은 버전인 efficientnetb4를 사용하였다.

<details>
<summary>모델 구조</summary>
 
![efficientnetb0](https://user-images.githubusercontent.com/131629615/235849301-1c2c6ee0-db99-4419-bd4c-671d940a7c9b.png)

</details>

추후 CLIP 모델로 변경  

<br />


## 5. 사용한 기법
* 앞서 라벨별로 증강한 이미지를 원본 데이터와 합친 뒤, Stratified K-Fold를 사용하여 라벨별로 균등하게 5개의 Fold로 나누어 교차검증을 진행하였다.
* 손실함수로는 CrossEntropyLoss에 Weight를 부여하여 사용하였다.
* 최적화를 위한 옵티마이저는 이미지 분류 문제에 흔히 쓰이는 Adam을 이용하였다.
* 모델 학습 속도 증가를 위해 AMP와 GradScaler를 사용하였다.
* 손실함수로 FocalLoss를 적용하였더니, 약간의 성능 향상이 있었다.

<br />




# 6. 결과
* 평가 기준은 WeightedF1Score로, Baseline에서 주어진 스코어는 0.2201 이였다.
* 내가 처음으로 나온 스코어는 0.2645로, Baseline보다는 높지만 그래도 여전히 너무 낮은 결과였다.
* 여러 모델들과 기법들을 학습한 뒤 최종 스코어는 0.3592정도로, 높아지긴 했지만 여전히 낮은 결과였다.


<br />




# 7. 회고 / 느낀점
* 이번 대회는 사실 혼자서 어느정도까지 할 수 있을지 궁금해서 참여하게 된 것이지만, 나온 결과를 보고 갈 길이 멀었다는 생각이 들었다.
* 하지만 딥러닝을 공부한 지 이제 겨우 한 달 정도이므로, 이번 대회의 상위 랭커의 코드가 나중에 공유가 된다면 반드시 그 코드들을 통해 더 깊게 공부해야겠다고 다짐했다.
* 아마 내 점수가 낮게 나온 이유는 과대적합 때문이라고 생각한다. 실제로 Validation까지는 점수가 매우 높게 나오다가, Test에서 점수가 터무니 없이 낮게 나온 걸 확인하였다.
* 과대적합을 막기 위해 여러가지 Augmentationd을 활용도 해보고, WeightedRandomSampler를 활용하면 이미지가 적은 라벨에서 너무 많이 추출되어 과대적합이 발생한다고 생각하여 라벨별로 수작업으로 일정 개수만큼 늘려보기도 하였지만, 여전히 과대적합이라는 문턱을 넘을 수가 없었다.
* 이번 대회는 특이하게도, 외부 데이터도 활용이 가능하다고 한다. 저작권이 없는 이미지라면, 데이터가 매우 적은 라벨에 외부에서 크롤링 해온 이미지를 활용할 수 있다는 뜻인데, 대회에서 이미지 라벨을 나눈 기준을 설명해 줄 때까지 보류하기로 하였다.


<br />


# 8. 대회 종료 후 회고 
* 이번 대회에서 가장 크게 느낀점 중 하나는 딥러닝은 머신러닝과는 다르게 전처리보다도 모델의 성능이 더욱 중요하다는 것이다. 상위권의 코드를 보니, 전처리 과정은 의외로 내가 한것보다도 더 단순하거나 비슷한 정도였다. 그러나 단순히 베이스라인 모델의 좀 더 높은 버전을 사용한 나와는 달리 들어보지 못한 여러 모델들을 사용하였고, 그게 성적에 엄청 영향을 미친 것 같았다. 여태껏 해온 머신러닝을 이용한 프로젝트에서는 전처리가 성능에 있어서 매우 큰 영향을 끼쳤기에 전처리에 가장 많은 시간을 투자하는 것이 습관이 되었는지, 이번에도 전처리에만 매우 많은 시간을 투자하였지만, 오히려 다양한 모델들을 그 시간동안 더 학습하고 활용했으면 어땠을까 하는 아쉬움이 남는다. 하지만 덕분에 이미지 전처리에 활용되는 여러 기법들이나, 딥러닝 모델의 속도를 향상시키기 위한 mixed-precision 기법 등 앞으로도 활용할 수 있는 여러 요소들을 공부하였으니 만족하기로 하였다.
* 대회가 끝난 뒤 상위 유저의 코드가 올라왔는데, 이 대회의 데이터처럼 불균형이 매우 심한 데이터 셋을 다루는 문제를 long-tailed problem이라고 한다고 한다.
* 이 long-tailed를 해결하는 방법은 평소 내가 알던 불균형 데이터 처리 (오버샘플링, 언더샘플링, 이미지 augmentation, weighted sampling)와는 매우 달라서 놀랐다.
* 이쪽에 대해서 좀 더 공부해야 겠다는 생각이 들었고, 추가로 비전 트랜스포머에 대해서도 더 심도 있게 공부해야 겠다는 생각을 하였다.
* 특히나 놀라왔던 방법은 자연어 탐색과 이미지 분류를 혼합해서 사용하는 느낌인 CLIP이였다.
* CLIP 말고도 여러 랭커들의 방법들이 올라오는 중인 것 같아, 나중에 공부해보기로 하였다.
* TTA라는 방법이 있어서 사용해 보았더니, 성능이 비약적으로 올라갔다. 생각해보니 머신러닝에서 앙상블 학습을 하는 것처럼, 딥러닝에서도 test data에 변형을 해가며 예측한 결과들을 앙상블 할 생각을 하지 못했다는 것이 후회된다.

이 대회가 끝난 뒤, 객체 탐지에 대하여 학습을 하였다. 그러다가 비전 트랜스포머 모델이 이해가 되지 않았고, 마침 자연어 모델도 궁금해 졌기에 자연어 모델들을 학습하였다. 자연어 모델들을 학습하다가 트랜스포머를 학습하였고, 그후 다시 비전 트랜스포머를 공부하니 이해가 되었다.
