# 🧠 CampusMind: RAG 기반 지능형 학습 보조 시스템

**설계: 이충환**

> **🔗 배포 링크: [https://ragtutor-9chlee9.streamlit.app/](https://ragtutor-9chlee9.streamlit.app/)**
---

## 📖 프로젝트 소개

**CampusMind**는 RAG(Retrieval-Augmented Generation) 아키텍처를 기반으로 한 지능형 학습 보조 시스템입니다.  
사용자가 업로드한 강의 자료(PDF, PPT, Word, 이미지, 음성, 영상)를 자동으로 분석하여 다양한 학습 기능을 제공합니다.

### 아키텍처

#### 솔루션 아키텍처
![솔루션 아키텍처](솔루션%20아키텍처.PNG)

#### 논문 아키텍처
![논문 아키텍처](논문%20아키텍처.PNG)

**데이터 흐름**
```
문서 업로드 → 청크 분할 → 임베딩(text-embedding-3-small) → FAISS 벡터 DB 저장
질의 입력   → 벡터 검색  → 관련 청크 검색(MMR)            → GPT-4o 답변 생성
```

---

## ✨ 주요 기능

| 기능 | 설명 |
|------|------|
| 📝 **핵심 요약 및 용어 정리** | 강의 자료를 자동 요약하고 핵심 용어를 정리 |
| 🎨 **구조 시각화** | 마인드맵 / 스파이더 다이어그램으로 내용 구조화 (Graphviz) |
| 🃏 **플래시 카드** | Q&A 형식의 암기 카드 자동 생성 |
| 🧩 **퀴즈** | 4지선다 객관식 퀴즈 자동 생성 |
| 🎧 **오디오 브리핑** | 핵심 요약을 TTS로 음성 변환 (OpenAI TTS-1) |
| 💬 **AI 조교** | RAG 기반 대화형 질의응답 (대화 맥락 유지) |

---

## 📁 지원 파일 형식

| 분류 | 형식 |
|------|------|
| 문서 | PDF, Word (.docx, .doc) |
| 프레젠테이션 | PowerPoint (.pptx, .ppt) |
| 이미지 | JPG, PNG, JPEG (GPT-4o Vision API) |
| 오디오 | MP3, WAV, M4A (Whisper 전사) |
| 영상 | MP4, AVI, MOV (MoviePy → Whisper 전사) |

---

## ☁️ 배포 환경 (Deployment Environment)

본 프로젝트는 **Streamlit Cloud** 환경을 통해 배포 및 운영되며, 다음과 같은 환경을 기준으로 설정되었습니다.
- **Python 버전**: `3.10`
- **웹 프레임워크 플랫폼**: Streamlit
- 로컬 또는 자체 서버 구축 시에도 **Python 3.10** 버전의 사용이 강력히 권장됩니다.
  
---

## 🚀 로컬 실행 가이드

### 0. 소스 코드 다운로드

```bash
git clone https://github.com/chlee19990109-cloud/RAG_tutor.git
cd RAG_tutor
```

### 1. 가상환경 생성

```bash
# 기본 코드
conda create -n [환경이름] python=[버전]

# 배포 환경과 동일한 파이썬 버전인 3.10으로 프로젝트 환경 설치를 권장합니다.
conda create -n rag_tutor python=3.10
```

### 2. 가상환경 활성화 및 확인

```bash
# 활성화
conda activate rag_tutor

# 파이썬 버전 확인
python --version
# Python 3.10.x 가 출력되어야 합니다
# 만약 3.10 버전이 아니면 가상환경 비활성화 후 1번으로 돌아가 새 가상환경을 생성하세요
```

### 3. 필수 추가 명령어 (참고)

```bash
# 생성된 환경 목록 확인
conda env list

# 가상환경 비활성화
conda deactivate

# 가상환경 삭제
conda remove -n rag_tutor --all
```

### 4. 가상환경 활성화 후 인터프리터 설정 (VS Code)

```
Ctrl + Shift + P → "Python: Select Interpreter" 검색 → 앞에서 생성한 가상환경 선택
```

### 5. 필요한 라이브러리 설치

```bash
pip install -r requirements.txt
```

> **⚠️ 참고**: `graphviz` 시각화 기능을 사용하려면 [Graphviz 공식 사이트](https://graphviz.org/download/)에서 시스템 패키지도 별도 설치해야 합니다.

### 6. OpenAI API 키 준비

앱 실행 후 사이드바에서 OpenAI API 키를 직접 입력합니다.  
API 키는 [OpenAI Platform](https://platform.openai.com/api-keys)에서 발급받을 수 있습니다.

### 7. 앱 실행

```bash
streamlit run tutor_final.py
```

브라우저에서 `http://localhost:8501` 이 자동으로 열립니다.

---

## 📦 기술 스택 및 구현 상세 (Technical Details)

`tutor_final.py`를 중심으로 한 핵심 기술 요소 요소 분해 및 구현 논리는 다음과 같습니다.

### 1. 프론트엔드 및 데이터 핸들러 (Streamlit)
* **상태 관리**: `st.session_state`를 활용하여 새로고침 되더라도 변수, 비용 데이터, AI 도우미 채팅 이력을 지속적으로 보존합니다.
* **캐싱 및 최적화**: `@st.cache_resource` 데코레이터를 이용해 OpenAI 클라이언트 모델 인스턴스 반복 생성을 억제합니다. 같은 파일 조합에 대해서는 파일 내용을 임베딩하는 API 재호출 방지 차원에서 미리 작성된 DB 결과를 캐싱 반환하도록 MD5 해싱 전략을 사용합니다.
* **멀티 링구얼 지원**: UI 인터페이스 텍스트 딕셔너리를 두어 한국어/영어 동시 제공 및 LLM 출력 언어를 유동적으로 지시합니다.

### 2. LLM / AI 추론 (LangChain & OpenAI)
* **대화형 컴포넌트**: 과거 `RetrievalQA` 모듈 대신 `ConversationalRetrievalChain`을 적용하여 대화의 히스토리와 문맥을 이해하는 지능적 연속 질의에 대비했습니다.
* **멀티모달 직접 호출**: 이미지(Vision), 전사(Whisper), 음성 기계 번역(TTS)은 안전성 및 구조 제어를 위해 LangChain으로 파이프라인을 구축하지 않고 OpenAI 직접 프롬프팅 방식을 적용했습니다.
* **제어된 출력물 포맷팅**: 플래시 카드 및 퀴즈 등을 제작할 때 시스템 프롬프트에 `response_format={"type": "json_object"}` 방식을 강제함으로서 결과물 파싱 시 일어나는 포맷 에러 가능성을 원천 차단했습니다.

### 3. 임베딩, 검색 최적화 및 DB
* **텍스트 스플리팅**: `RecursiveCharacterTextSplitter`를 사용해 단락→문장→단어 순으로 1500자 크기(200자 오버랩) 문맥 손실 없는 청크 단위로 나누었습니다.
* **벡터 DB (FAISS)**: 페이스북 연구소의 로컬 경량형 FAISS (CPU 버젼) 데이터베이스를 사용하여 `text-embedding-3-small`로부터 가져온 임베딩 결과물 유사성 검색을 수행합니다.
* **다양성 기반 검색**: 검색 쿼리에서 가장 매칭 되는 단순 유사 결과를 가져오는 것을 넘어서, MMR(Maximal Marginal Relevance) 검색 방식을 채택하여 중복 내용을 방지하고 폭넓은 지식을 바탕 하도록 검색력을 제고했습니다. (`k=20`, `fetch_k=50`)

### 4. 구조 시각화 (Graphviz)
* 사용자 주제 모드(전체/특정 논제)에 따라 다이어그램의 형태 모드가 분류됩니다.
* 마인드 맵의 경우 `dot` 엔진 알고리즘을 사용하여 계층적인 구조를 가져오고, 스파이더 다이어그램의 경우 `neato` 알고리즘 기반 상호 복합적 방사형 구조를 전개하도록 DOT 언어 파이프라인으로 구현되었습니다. 

---

## 💸 API 비용 추적 시스템

앱 내 사이드바에서 OpenAI API 사용 비용 정보를 실시간으로 계산하여 화면에 송출합니다 (세션 기반).

| 모델 | 과금 기준 | 단가 | 활용 |
|------|----------|------|------|
| **GPT-4o** | 토큰 수 | 입력 $2.50 / 출력 $10.00 (1M 토큰당) | 요약, 퀴즈, 플래시카드, 대화 생성 |
| **TTS-1** | 글자 수 | $15.00 / 1M 글자 | 추출된 오디오 브리핑 생성 |
| **Whisper-1** | 재생 시간 | $0.006 / 분 | 동영상(MoviePy), 오디오 전사 |
| **text-embedding-3-small** | 토큰 수 | $0.02 / 1M 토큰 | FAISS 벡터 저장 임베딩 생성 |

> ※ 모델 요금은 변경될 수 있으므로 [OpenAI 공식 요금표](https://platform.openai.com/pricing)를 참고하시기 바랍니다.

---

## 🌐 언어 지원

한국어 / English (사이드바에서 글로벌 전환 가능)
