# 2025-AI-캡스톤
## Overview
ResNet18 모델 사용한 고라니와 노루 fine-grained and data imbalance 문제 해결

(고라니) : (노루)의 비율을 1:1 ~ 1:0.001 까지 감소시키면서 imbalance 한 상황에서 train, valid
## Process
### 0. Installation
#### 0-1. Requirements
- Python 3.10.12
- PyTorch >= 1.13.0
- torchvision >= 0.14.0
- torchmetrics >= 0.11.0
- Pillow >= 9.0.0
- tqdm >= 4.64.0
> All necessary packages are listed in `requirements.txt`.
> You can download and install it by following the instructions later in this README.
#### 0-2. AIhub Dataset (Train Dataset)
<details>
<summary><strong>[See details]</strong></summary>
This project uses a wildlife image dataset provided by AIHub, a public data platform operated by the Korean government.  
Due to license and privacy restrictions, the dataset is **not included in this repository** and must be downloaded manually by the user.

- Source: [AIHub - Wildlife Image Dataset](https://www.aihub.or.kr/aihubdata/data/view.do?currMenu=&topMenu=&aihubDataSe=data&dataSetSn=645)
- Description: Images of 11 wild animal species (e.g., boar, roe deer) captured by infrared and normal cameras
- Purpose: For training and evaluating image classification models
##### 1) How to download
1. Go to the [AIHub dataset page](https://www.aihub.or.kr/)
2. Sign up and log in
3. Agree to the terms of use and request access
4. Download the provided dataset file (usually in .zip format)

##### 2) Directory structure (example)
After extracting the dataset, please organize it as follows:
```
project_root/
  ├── data/
  │ ├── train/
  │ │ ├── class_01_boar/
  │ │ ├── class_02_roe_deer/
  │ │ └── ...
  │ ├── valid/
  │ └── test/
```
</details>

### 1. git clone
```
git clone https://github.com/lxxsxoh/Test-using-ResNet18-for-fine-grained-problem.git
```
### 2. Setting
> 폴더 확인
```
ls Test-using-ResNet18-for-fine-grained-problem
```
> 해당 폴더 접속
```
cd Test-using-ResNet18-for-fine-grained-problem
```
> install requirements
```
pip install -r requirements.txt
```
### 3. Training (with AIhub data, train 과정이 필요 없다면 바로 4번으로)
> train

```
python3 train.py [OPTIONS]
```
|Argument|Type|Default|Description|
|-----|-----|-----|-----|
|--train_data_root|string||train data 가져올 경로|
|--ratio|string|1:1|고라니 대 노루 비율|
|--classes|int|2|분류할 class 수|

> validation
```
python3 validation.py [OPTIONS]
```
|Argument|Type|Default|Description|
|-----|-----|-----|-----|
|--model_root|string||pretrained weight 가져올 경로(.pth 파일의 경로)|
|--test_data_root|string||valid data 가져올 경로|
|--classes|int|2|분류할 class 수|
> The experiment should be conducted under the following five ratio conditions: 1:1, 1:0.1, 1:0.01, 1:0.005, and 1:0.001.
### 4. Test Using pretrained model (별도의 train 없이 pretrained model 사용하여 test만)
> pth 파일 사용

사전학습(pretraining)은 AIHub 데이터셋을 기반으로 수행되었으며, 해당 모델의 최적 가중치 best_model.pth에 저장되어 있다. 본 실험에서는 이를 활용하여 테스트 단계만을 수행한다.

> pth 폴더 구성
```
trained_model/
  ├── base/
  │   ├── best_model(1).pth
  │   ├── best_model(0.1).pth
  │   ├── best_model(0.01).pth
  │   ├── best_model(0.005).pth
  │   └── best_model(0.001).pth
  ├── cont/
  │   └── best_model(0.1).pth
  ├── over/
  │   └── best_model(0.01).pth
  └── over+con/
      └── best_model(1).pth
```
> 요구되는 test 폴더 구성 **(각 클래스 별 2,000장, Image Size 최소 224X224)**
```
Test-using-ResNet18-for-fine-grained-problem/
  ├── dataset/
  │ ├── test/
  │ │ ├── water_deer/
  │ │ ├── roe_deer/
  │ │ └── ...
  │ ├── train/
  │ └── valid/
```
> test
```
python3 test.py [OPTIONS]
```
> test example
```
python3 test.py \
  --model_root ./trained_model/ \
  --test_data_root ./dataset/valid \
  --classes water_deer roe_deer
```
|Argument|Type|Default|Description|
|-----|-----|-----|-----|
|--model_root|string||pretrained weight 가져올 경로(.pth 파일의 경로)|
|--test_data_root|string||test data 가져올 경로|
|--classes|string||분류할 class|
|--batch_size|int|64|배치사이즈|
> The experiment should be conducted under the following five ratio conditions: 1:1, 1:0.1, 1:0.01, 1:0.005, and 1:0.001.
### 5. Result Example
최종적으로, 총 4개의 모델 **(baseline, Over, Con, Over+Con)** 각각에 대해 5가지 데이터 비율 **(1:1, 1:0.1, 1:0.01, 1:0.005, 1:0.001)** 조건에서의 실험 결과가 도출되어야 한다.
> 결과 저장 경로
```
[--model_root]/result_log.txt
```

> Example
>  >baseline
![image](https://github.com/user-attachments/assets/420774f3-7ec0-4f00-90ac-5be11ae6e6fc)
>  >over+con
![image](https://github.com/user-attachments/assets/328779f7-7f65-40f1-825c-4b4d335787e0)
