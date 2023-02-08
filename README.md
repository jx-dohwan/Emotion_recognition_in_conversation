
## 💡프로젝트 소개

```
1️⃣ 주제 : 대화속 감정인식
2️⃣ 설명 : [CoMPM 논문](https://arxiv.org/pdf/2108.11626v3.pdf)을 기반으로 ERC 모델을 구 
```
## 1. CoMPM 논문 소개
- CoM(context module) : 입력으로는 대화의 발화들이 전부 들어간다.
- PM(pre-trained memory module) : CSK와 같이 context-independent발화의 feature을 담아내기 위함이다. 



---
## 1. Model Test
해당 모델은 HuggingFace에 업로드하여 pipeline으로 쉽게 사용할 수 있다.
```
pip install transformers==4.25.1
```
```
from transformers import pipeline

model_name = "jx7789/kobart_summary_v2"

dialogue = ["다들 설날에 함 보자?", "며칠에 볼지는 안 정했지?", "ㅇㅇ 아직 안정함ㅋㄱㅋ", "ㅇㅋ", "설 당일은 다들 바쁠테고", "토요일이나 월 중에 보면 될려나", "토욜", "저녁 한끼하고", "볼링이나 칩시다"]

gen_kwargs = {"length_penalty": 2.0, "num_beams":8, "max_length": 128}

pipe = pipeline("summarization", model=model_name)
print(pipe("[sep]".join(dialogue), **gen_kwargs)[0]["summary_text"])
```
##### output : 다들 설날에 볼지 아직 정하지 않았고 당일은 다들 바쁠 테니 토요일 저녁에 볼링이나 칩시다.

## 2. Service Test
### 1) 로컬 Test
```
pip install transformers==4.25.1
pip install Flask 
```
```
python run.py
```
### 2) Docker Test
- Ubuntu-18.04(WSL) 환경에서 사용
```
docker build -t flask .
docker run -d -p 3000:3000 flask
```

---
## 🗓️ 프로젝트 개선 진행
### 1. 모델링
#### [최종 결과 코드 보러가기](https://github.com/jx-dohwan/KoBART_generation_summary_service/blob/main/make_models/KoBART_Summary_v3.ipynb)
|기존 서비스|개선 서비스|진행사항(%)|RougeL F1|
|:---------:|:----------:|:------:|:------:|
|성능저하시키는 전처리|불필요한 전처리 기법 제거|100%||
|mecab으로 길이측정|model tokenizer로 길이측정|100%||
|학습, 검증 데이터 동일한 전처리 적용|학습, 검증 데이터 전처리 구분|100%||
|테스트 데이터셋 미구축|[테스트 데이터셋 구축](https://github.com/jx-dohwan/KoBART_generation_summary_service/blob/main/make_models/kobart_summary.ipynb)|100%|0.2386|
|BOS, SEP 미사용|[BOS, SEP사용](https://github.com/jx-dohwan/KoBART_generation_summary_service/blob/main/make_models/KoBART_Summary_v2.ipynb)|100%|0.2439|
|일반 fine-tuning|[도메인 적응](https://github.com/jx-dohwan/KoBART_generation_summary_service/blob/main/make_models/domain_adaptation.ipynb)|100%|0.2526|
|모델 미배포|[Huggingface에 배포](https://huggingface.co/jx7789/kobart_summary_v3)|100%||

### 2. Serving
개선 서비스|진행사항(%)|
|:----------:|:------:|
|[웹 구현](https://github.com/jx-dohwan/KoBART_generation_summary_service/tree/main/Serving)|100%|
|[전처리 속도 개선](https://github.com/jx-dohwan/KoBART_generation_summary_service/blob/main/Serving/preprocessor.py)|100%|
|[도커 빌드](https://github.com/jx-dohwan/KoBART_generation_summary_service/blob/main/Serving/Dockerfile)|100%|

### 3. 코드 객체지향화
|기존 서비스|개선 서비스|진행사항(%)|
|:---------:|:----------:|:------:|
|함수형 코드|Pytorch Lighting|40%|
|Jupyter Notebook|Python Script|40%|

---


---
