# 🌐 무제한 자원 기반 Modular RAG 시스템 (KMS with Modular RAG)

> **설계자**: 김대현 (201935607, 전기공학과)  
---

## 🧠 프로젝트 개요

**무제한 자원 기반 Modular RAG 시스템(Knowledge Management System)**은 외부 LLM 학습 환경과 내부 RAG 질의 시스템을 완전히 분리하여,
보안성과 확장성을 동시에 확보한 차세대 지식 검색 및 생성 시스템입니다.

- 외부 공개 데이터와 LLM으로 학습
- 내부망에서만 질의/응답 및 로깅 처리
- 다양한 멀티모달 질의(텍스트 + 이미지) 지원
- 최종 목표: **휴먼 F1 ≥ 95%, 질의 Recall ≥ 99%**

---

## 🏗️ 시스템 아키텍처

### 🔒 외부 학습 환경 (SFT only)
- **LLM**: Mixtral 8x7B, KoAlpaca 등
- **데이터**: AI Hub, KorQuAD, KLUE, CommonCrawl 등
- **도구**: HuggingFace, LoRA, DeepSpeed
- **보안 경계**: Snapshot Loader를 통한 해시 검증 및 모델 가중치만 반입

### 🧩 내부 운영 환경
- **Vector DB**: Milvus 1B
- **검색 계층**: BM25 + BGE-X + KoCLIP
- **재랭커**: dragonkue/bge-reranker-v2-m3-ko (Cross-Encoder)
- **생성기**: Mixtral 8x7B (텍스트), VARCO-VISION-14B (이미지 포함 질의 대응)
- **LLM Guard**: LlamaGuard-7B (PII, 정책 위반 검출)
- **인터페이스**: 사내 챗봇 API / 응답 빌더 / 로거

---

## 🔍 사용 모델 요약

| 역할 | 모델명 | 특징 |
|------|--------|------|
| Text Generator | [Mixtral 8x7B](https://huggingface.co/mistralai/Mixtral-8x7B-Instruct-v0.1) | MOE 구조, SFT 적용, 고효율 추론 |
| Vision Generator | [VARCO-VISION-14B](https://huggingface.co/NCSOFT/VARCO-VISION-14B) | 멀티모달 대응, 통계/설계도 포함 질의 |
| Retriever | [BGE-M3](https://huggingface.co/BAAI/bge-m3) | 구조화 데이터 대응, 다국어 지원 |
| Reranker | [BGE-Reranker-v2-M3-KO](https://huggingface.co/dragonkue/bge-reranker-v2-m3-ko) | 한국어 최적화, 벤치마크 1위 |
| Guard | [LlamaGuard-7B](https://huggingface.co/meta-llama/LlamaGuard-7b) | 정책 기반 위험 탐지, LoRA 확장 용이 |

---

## 🧪 학습 및 배포 과정

1. **데이터 파이프라인**: AI Hub 등 → 정제/QA 구조화 → Train/Val/Test 분할
2. **SFT 전략**:
   - Full Fine-tuning (8×H100)
   - RAFT 및 Mask-DPO
3. **배포**: Snapshot Loader로 내부망 반입 → Triton Inference Server 서빙
4. **성능 검증**:
   - RAGAS, MTEB-KO
   - Human F1 ≥ 95%

---

## 🚀 예상되는 기능적 특장점

- **운영망 ↔ 학습망 완전 분리**: 보안성 극대화
- **멀티모달 RAG 질의** 지원 (이미지 + 텍스트)
- **Long-Context** 처리 가능 (Mixtral 128K 토큰 실험 후 적용)
- **Expert-of-Experts Retriever**: 희귀 질의 Recall 99%+

---

## 📈 적용 기대 효과

| 분야 | 기대 효과 |
|------|------------|
| IT 지원 | 장애 해결 속도 ↑ (MTTR ↓ 40%) |
| 경영/사무 | 반복 질의 자동화, 규정 위반 ↓ |
| 법무 | 계약서/판례 검색 시간 절감 |
| 영업/마케팅 | 시장 맞춤형 제안서 작성 시간 단축 |
| 온보딩/교육 | 직무 러닝 가속화, 역량 성장 ↑ |

---

## 💰 시스템 예산 (1차 구축)

| 항목 | 금액 (USD) | 환산 (KRW 백만) |
|------|------------|-----------------|
| GPU 서버 (LLM + Retriever + Vision) | $702,000 | 959.0 |
| 스토리지/네트워크 | $30,000 | 41.0 |
| 인건비 (ML/DevOps 등) | $379,000 | 524.6 |
| 기타 (전력/클라우드 백업 등) | $24,600 | 33.4 |
| **총합 (CapEx + OpEx)** | **$1,135,600** | **약 155억 원 ±15%** |

---

## 📬 연락처

- 📧 Email: qwertypotter@gachon.ac.kr
  
---

## 📄 라이선스
- 본 프로젝트는 MIT License 하에 배포되며, 외부 모델 및 데이터셋의 별도 라이선스 조건을 따릅니다. 자세한 내용은 [LICENSE](./LICENSE) 파일을 참조하세요.


> 본 시스템은 무제한 자원 가정 하의 연구 목적 프로토타입으로, 일부 오픈모델의 라이선스 제한에 따라 상용 서비스 도입 시 별도 검토가 필요합니다.
