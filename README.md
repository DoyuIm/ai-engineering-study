# 임도유 (doyu) · AI 엔지니어링 스터디

![Python](https://img.shields.io/badge/Python-3.11-3776AB?logo=python&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?logo=docker&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-0.162-009688?logo=fastapi&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-7-DC382D?logo=redis&logoColor=white)
![LangChain](https://img.shields.io/badge/LangChain-RAG-1C3C3C?logo=langchain&logoColor=white)
![Claude](https://img.shields.io/badge/Claude-API-D97757?logo=anthropic&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-kind-326CE5?logo=kubernetes&logoColor=white)

5명이 7주 동안 **Docker → FastAPI → RAG → Kubernetes** 순서로 익히고, 마지막 주에 "k8s 위에 배포된 RAG API"를 팀으로 만드는 스터디의 개인 작업물이에요.
매주 직접 실습해서 확인한 숫자와 막힌 점을 같이 남겨요.

> 팀 저장소: [SoleMin/Study-service_basic](https://github.com/SoleMin/Study-service_basic) · 이 저장소는 팀 저장소의 `members/doyu/` 폴더를 옮겨 온 것이에요.

> 주차별로 한 것, 재 본 숫자, 막힌 점을 한 곳에 모은 기록: [PROGRESS.md](PROGRESS.md)

## 로드맵

| 주차 | 주제 | 상태 |
| --- | --- | --- |
| 1 | Docker 기초 | ✅ |
| 2 | FastAPI + docker compose + Redis 캐시 | ✅ |
| 3 | RAG 기초 (LangChain) | ✅ |
| 4 | RAG 고도화 + `/ask` API | ✅ |
| 5 | Kubernetes 기초 (kind) | ✅ |
| 6 | RAG 앱 k8s 배포 | ⬜ |
| 7 | 팀 프로젝트 통합 · 데모데이 | ⬜ |

## 주차별 결과물

| 주차 | 폴더 | 내용 | 상태 |
| --- | --- | --- | --- |
| 1 | `week1/` | Python 앱 이미지 빌드·실행, 빌드 캐시 순서 실험, `-p`·`-v` 실습, slim vs full 크기 비교 | 완료 |
| 2 | `week2/` | FastAPI `/chat`(가짜 LLM) + Redis TTL 캐시, compose, async 실험 | 완료 |
| 3 | `week3/` | 팀 위키 RAG: bge-m3 + Chroma + Claude, 출처 반환, 질문 20개로 Recall@5·MRR@5 채점 | 완료 |
| 4 | `week4/` | BM25·하이브리드(RRF)·리랭커 8가지 비교, FastAPI `/ask` + Docker, 평가 데이터 누수 발견 | 완료 |
| 5 | `week5/` | kind 3노드 클러스터, nginx·2주차 FastAPI+Redis 배포, 자가 복구·스케일, 일부러 고장 내기, 무중단 롤링 업데이트 | 완료 |

### 직접 잰 숫자

| 실험 | 결과 | 기록 |
| --- | --- | --- |
| Dockerfile 레이어 순서 (app.py만 수정 후 재빌드) | 의존성 먼저 **0.3s** vs 코드 먼저 2.6s | [week1-notes](wiki/raw/week1-notes.md) |
| 베이스 이미지 크기 | `python:3.11-slim` **234MB** vs `python:3.11` 1.62GB | [week1-notes](wiki/raw/week1-notes.md) |
| `/chat` 같은 질문 두 번 (LLM 2초 지연 가정) | 2,014ms → Redis 캐시 **0.6ms** | [week2-notes](wiki/raw/week2-notes.md) |
| 동시 요청 3개, 각 3초 대기 | `async` + `await` 3s/3s/3s, `async` 안 `time.sleep` **3s/6s/9s** | [async-fastapi](wiki/pages/async-fastapi.md) |
| 청크 크기별 검색 (질문 20개, k=5) | 200: Recall **1.00** / 500: 0.95 / 1000: 0.95 | [week3-notes](wiki/raw/week3-notes.md) |
| 팀 질문 25개 MRR@5 | 벡터 0.780 · 하이브리드 0.787 · 리랭커 **0.863** (질문당 3.2초) | [week4-notes](wiki/raw/week4-notes.md) |
| 평가 질문이 든 페이지를 검색에서 뺐을 때 | BM25 MRR 0.688 → **0.783** | [week4-notes](wiki/raw/week4-notes.md) |
| Docker 서버 시작 (임베딩 색인 저장) | 2시간 넘게 → **91초** | [week4-notes](wiki/raw/week4-notes.md) |
| 롤링 업데이트 중 요청 실패 | probe 없음 1/60 → readiness+preStop **0/80** | [week5-notes](wiki/raw/week5-notes.md) |

## 위키 페이지

| 주차 | 페이지 | 팀 위키 반영 |
| --- | --- | --- |
| 1 | [image-vs-container](wiki/pages/image-vs-container.md) | ✅ [반영됨](https://github.com/SoleMin/Study-service_basic/blob/main/team/wiki/image-vs-container_doyu.md) |
| 2 | [async-fastapi](wiki/pages/async-fastapi.md) | ✅ [반영됨](https://github.com/SoleMin/Study-service_basic/blob/main/team/wiki/async-fastapi_doyu.md) |
| 3 | [rag-pipeline](wiki/pages/rag-pipeline.md) | ✅ [반영됨](https://github.com/SoleMin/Study-service_basic/blob/main/team/wiki/rag-pipeline_doyu.md) |
| 4 | [hybrid-search-reranker](wiki/pages/hybrid-search-reranker.md) | 🔄 리뷰 대기 ([PR #20](https://github.com/SoleMin/Study-service_basic/pull/20)) |
| 5 | [k8s-pod-deployment-service](wiki/pages/k8s-pod-deployment-service.md) | ✅ [반영됨](https://github.com/SoleMin/Study-service_basic/blob/main/team/wiki/k8s-pod-deployment-service_doyu.md) |

## 실행해 보기

```bash
# 1주차: 이미지 빌드와 실행
cd week1 && docker build -t week1-app . && docker run --rm week1-app

# 2주차: FastAPI + Redis (실제 LLM은 3주차에 연결)
cd week2 && cp .env.example .env
docker compose up -d --build
open http://localhost:8000/docs

# 3주차: 팀 위키 RAG (팀 저장소를 옆 폴더에 클론해 두기)
cd week3 && python3.11 -m venv .venv && .venv/bin/pip install -r requirements.txt
cp .env.example .env               # ANTHROPIC_API_KEY 채우기 (없으면 검색만)
.venv/bin/python rag.py "도커 이미지와 컨테이너의 차이는?"
.venv/bin/python evaluate.py --sizes 200 500 1000

# 4주차: 검색 비교와 /ask 서버
cd week4 && python3.11 -m venv .venv && .venv/bin/pip install -r requirements.txt
.venv/bin/python compare.py --exclude rag-evaluation_mingi
.venv/bin/uvicorn app.main:app --port 8000      # POST /ask {"question": "..."}

# 5주차: kind 클러스터에 배포
kind create cluster --config week5/kind-config.yaml
docker build -t doyu-week2-api:1.0 week2 && kind load docker-image doyu-week2-api:1.0 --name study
kubectl apply -f week5/k8s/00-namespace.yaml -f week5/k8s/
kubectl -n study port-forward svc/api 8000:8000
```

## 폴더 구조

```
.
├── week1/          Dockerfile 실습 (Dockerfile.bad, .full은 비교 실험용)
├── week2/          FastAPI 앱, docker-compose.yml, .env.example
├── week3/          RAG (rag.py), 채점 (evaluate.py), 질문셋 (questions.jsonl)
├── week4/          검색기 (hybrid.py), 비교 (compare.py), /ask 서버 (app/), Dockerfile
├── week5/          kind 설정, k8s/ 매니페스트 (nginx, redis, api), k8s/broken/ 고장 예시
└── wiki/
    ├── raw/        실습 메모, 에러 로그, 참고 링크
    └── pages/      개념 정리 (한 줄 정의 / 왜 필요한가 / 최소 예제 / 막힌 점 / 출처)
```
