# NAVER CLOUD CLOVA Studio 인턴십 프로젝트

## 프로젝트 개요

NAVER CLOUD의 CLOVA Studio 팀에서 진행한 인턴십 프로젝트입니다. LLM(Large Language Model) 관련 다양한 연구와 실험을 진행했습니다.

## 주요 연구 내용 및 결과

### [1. 국가 분류 튜닝 task](국가%20분류%20튜닝%20task.md)

- HCX-003, HCX-DASH-001 모델 대상 튜닝 실험 진행
- 데이터셋 크기에 따른 성능 변화 분석
- HCX-003: 2,000개 이상 데이터셋에서 오류율 2% 이하 달성
- HCX-DASH-001: 비용 효율성 우수, 더 많은 데이터로 성능 개선 가능성 확인

### [2. 모델 평가 - Newport vs Calgary](모델%20평가%20-%20Newport%20vs%20Calgary.md)

- Newport (HCX-T04)와 Calgary (HCX-003) 모델 비교 평가
- 12개 세부 Skills x 10개 도메인 x 5개 난이도 레벨로 구성된 종합 평가
- GPT-4-turbo를 활용한 정량적 평가 진행
- Newport 모델이 대부분의 영역에서 Calgary 대비 우수한 성능 확인
- 특히 구조화 작업에서 큰 성능 개선 확인

### [3. 문체 변환 튜닝 task](문체%20변환%20튜닝%20task.md)

- 문체 변환과 요약 기능 동시 사용시 발생하는 문제 해결
- 토큰 수에 따른 문체 변환 성공률 분석
- AI 허브 논문 자료 요약 데이터 활용한 튜닝 데이터셋 구성
- 리스트 형태, 문장 형태, 문체 변환 특화 등 다양한 튜닝 방식 실험
- 문체 변환 성공률 향상 및 요약 품질 개선 달성

### [4. 요약 모델 평가 비교](요약%20모델%20평가%20비교.md)

- 31개 사내향 use-case와 10개 평가 문항으로 성능 비교
- Lima와 Choco 기반 요약 모델의 답변 길이, 불렛 개수 등 비교
- Lima가 일반적으로 더 긴 답변과 많은 불렛 포인트 사용
- 뉴스 헤드라인 요약에서는 Choco가 더 상세한 답변 제공
- 두 모델 모두 단어/구절 입력시 Chat Completion 형태의 답변 생성

### [5. 토큰 수의 한계를 극복하라! (1)](토큰%20수의%20한계를%20극복하라!%20(1).md)

- 슬라이딩 윈도우 API를 활용한 토큰 제한 극복 방안 연구
- Chat Completion API와 연계하여 긴 대화 처리 구현
- 대화 턴 단위로 오래된 대화 제거하여 메모리 효율화
- Python 기반 모듈화된 구조로 구현하여 재사용성 확보
- 실제 대화 테스트를 통한 성능 검증

### [6. LLMOps 효율화](LLMOps%20효율화.md)

- Caching, Prompt Compression/Optimization, Model Cascading/Routing 연구
- Context Length 한도, 비용, Latency, 성능 등 다양한 요소 고려
- 사용자와 공급자 관점에서의 효율화 방안 분석
- Input/Output 가공을 통한 비용 및 Latency 최적화 방안 도출
- Context caching, Semantic caching 등 다양한 최적화 기법 연구

### [7. Search API & Chat-Completion API 비교](Search%20API%20%26%20Chat-Completion%20API%20비교.md)

- CLASSIFICATION 작업에서의 API 성능 비교
- 데이터 증가에 따른 효과 분석
- System Prompt 영향도 평가
- 각 API의 장단점 및 적합한 사용 사례 도출



