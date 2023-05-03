# 도배 하자 유형 분류 AI 경진대회
<https://dacon.io/competitions/official/236082/overview/description>

# 1. 참여 계기
최근 딥러닝에 관심이 많아 공부하던 도중, 데이콘에서 대회가 열려있기에 참가해 보았다.

입상 등 결과를 위해서라기 보다는, 근래 공부하였던 딥러닝 기법들을 내가 얼마나 스스로 사용할 수 있나 확인해보고 싶었다.

# 2. 참여 인원 / 기간
* 개인 프로젝트
* 2023년 

# 3. 사용 기술
* Python
* Matplotlib
* Sklearn
* Pytorch
* Torchvision
* WeightedRandomSampler

# 4. 작업 환경
* Google Colab

# 5. 프로젝트 진행 과정
1. 전처리
* LabelEncoder를 통해 데이터의 라벨들을 숫자로 인코딩 후, plt.imshow를 이용해 이미지 데이터들을 시각화 하였다.
* 데이터들의 라벨을 확인하였더니, 라벨별로 불균형이 매우 심한걸로 나타나 불균형을 어떻게 처리하는지가 관건일 것으로 보였다.


2. 커스텀 데이터셋 설정
* 이번 대회에서 데이터들은 분류하기 쉽도록 각 라벨별로 폴더안에 이미지들이 저장되어 있었기에, 원래라면 Torchvision의 ImageFolder를 이용하여 손쉽게 이미지를 불러오는 것이 가능하다.
* 그러나, ImageFolder는 데이터를 불러오는 속도가 매우 느리기 때문에, 추후에 모델을 학습할때 시간이 많이 소모될 것으로 예상되어, 커스텀 데이터셋을 만들었다.
* 이미지가 들어있는 폴더의 경로를 통해, 이미지와 라벨을 리턴하는 커스텀 데이터셋을 만든 뒤, DataLoader에서 가장 작업 환경에 걸맞는 num_workers를 찾아주었다.

3. 이미지 증강
* 앞서 언급했듯이, 이미지 라벨별로 데이터의 수가 매우 상이하다. 가장 적은 데이터는 3개, 많은 데이터는 1400여개로 데이터 수가 매우 차이가 나므로, WeightedRandomSampler를 활용하여 이미지 불균형을 해소하고자 하였다.
* 단순히 이미지 수만 늘리면 과적합이 되므로, Augmentation을 활용하여 이미지를 변형해가며 데이터를 늘렸다.

4. 모델 설정
* 모델은 베이스라인에서 주어져 있던 efficientnetb0를 사용하였다.


