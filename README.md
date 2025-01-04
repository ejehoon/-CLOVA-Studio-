# NAVER CLOUD CLOVA Studio 인턴

<img src="https://github.com/ejehoon/NAVER_CLOUD-CLOVA_Studio/blob/main/clovastudio.png" alt="국가 분류 튜닝" width="70%">

## CLOVA Studio 소개

CLOVA Studio는 NAVER CLOUD의 LLM(Large Language Model) 관련 연구 및 플랫폼 개발을 지원하는 서비스입니다. AI 기술을 바탕으로 다양한 산업과 사용 사례에 적합한 모델 튜닝, 응용, 사용자 경험 개선 작업을 수행합니다.

네이버 클라우드 CLOVA Studio팀에서 기획 & 리서치 인턴으로 근무하면서 B2B 사용자 관점에서 AI 기반 서비스의 요구사항과 기능 사례를 조사·분석하는 업무를 담당했습니다. 또한, LLMOps 전 주기 지원을 위한 개발 도구 및 플랫폼 기능 활용 사례를 구상하고, CLOVA Studio의 사용자 경험 개선에 기여하며 시장 환경 분석과 고객 니즈 파악 능력을 한층 강화할 수 있었습니다.
<br>
## 프로젝트 개요

NAVER CLOUD의 CLOVA Studio 팀에서 인턴으로 속하며 진행했던 과제입니다. LLM(Large Language Model) 관련 다양한 연구와 실험을 진행했습니다.
<br>

# 주요 연구 내용 및 결과

아래 제목을 클릭하여 각 연구 주제의 상세 내용을 확인할 수 있습니다.

## [1. 국가 분류 튜닝 task](국가%20분류%20튜닝%20task%20eb33317d27ca413ea7f8929091bdafe8.md)

<img src="https://github.com/ejehoon/NAVER_CLOUD-CLOVA_Studio/blob/main/%EA%B5%AD%EA%B0%80%20%EB%B6%84%EB%A5%98%20%ED%8A%9C%EB%8B%9D%20task/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-09_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_8.10.45.png" alt="국가 분류 튜닝" width="70%">

- HCX-003, HCX-DASH-001 모델 대상 튜닝 실험 진행
- 데이터셋 크기에 따른 성능 변화 분석
- HCX-003: 2,000개 이상 데이터셋에서 오류율 2% 이하 달성
- HCX-DASH-001: 비용 효율성 우수, 더 많은 데이터로 성능 개선 가능성 확인
<br>

## [2. 모델 평가 - Newport vs Calgary](<모델%20평가%20-%20Newport%20(HCX-T04)%20vs%20Calgary(HCX-003%200760706720474e66924d3ed1505e0ddd.md>)

<img src="https://github.com/ejehoon/NAVER_CLOUD-CLOVA_Studio/blob/main/%EB%AA%A8%EB%8D%B8%20%ED%8F%89%EA%B0%80%20-%20Newport%20(HCX-T04)%20vs%20Calgary(HCX-003)/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-14_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_6.07.12.png" alt="모델 평가" width="40%">

- Newport (HCX-T04)와 Calgary (HCX-003) 모델 비교 평가
- 12개 세부 Skills x 10개 도메인 x 5개 난이도 레벨로 구성된 종합 평가
- GPT-4-turbo를 활용한 정량적 평가 진행
- Newport 모델이 대부분의 영역에서 Calgary 대비 우수한 성능 확인
- 특히 구조화 작업에서 큰 성능 개선 확인
<br>

## [3. 문체 변환 튜닝 task](문체%20변환%20튜닝%20task%20c3a3ad0a68c04a8098b096ff22d2de59.md)

<img src="https://github.com/ejehoon/NAVER_CLOUD-CLOVA_Studio/blob/main/%EB%AC%B8%EC%B2%B4%20%EB%B3%80%ED%99%98%20%ED%8A%9C%EB%8B%9D%20task%20/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-09_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_8.12.25.png" alt="문체 변환 튜닝" width="50%">

- 문체 변환과 요약 기능 동시 사용시 발생하는 문제 해결
- 토큰 수에 따른 문체 변환 성공률 분석
- AI 허브 논문 자료 요약 데이터 활용한 튜닝 데이터셋 구성
- 리스트 형태, 문장 형태, 문체 변환 특화 등 다양한 튜닝 방식 실험
- 문체 변환 성공률 향상 및 요약 품질 개선 달성
<br>

## [4. 요약 모델 평가 비교](요약%20모델%20평가%20비교%2072a41abbf5644d3ba819ba744152dd9d.md)

<img src="https://github.com/ejehoon/NAVER_CLOUD-CLOVA_Studio/blob/main/%EC%9A%94%EC%95%BD%20%EB%AA%A8%EB%8D%B8%20%ED%8F%89%EA%B0%80%20%EB%B9%84%EA%B5%90%20/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-28_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB_2.12.39.png" alt="요약 모델 비교" width="30%">

- 31개 사내향 use-case와 10개 평가 문항으로 성능 비교
- Lima와 Choco 기반 요약 모델의 답변 길이, 불렛 개수 등 비교
- Lima가 일반적으로 더 긴 답변과 많은 불렛 포인트 사용
- 뉴스 헤드라인 요약에서는 Choco가 더 상세한 답변 제공
- 두 모델 모두 단어/구절 입력시 Chat Completion 형태의 답변 생성
<br>

## [5. 토큰 수의 한계를 극복하라! (1)](<토큰%20수의%20한계를%20극복하라!%20(1)%20-%20슬라이딩%20ᄋ%2077cc77f131dc46218cb46251714bd7eb.md>)

<img src="https://github.com/ejehoon/NAVER_CLOUD-CLOVA_Studio/blob/main/%ED%86%A0%ED%81%B0%20%EC%88%98%EC%9D%98%20%ED%95%9C%EA%B3%84%EB%A5%BC%20%EA%B7%B9%EB%B3%B5%ED%95%98%EB%9D%BC!%20(1)%20-%20%EC%8A%AC%EB%9D%BC%EC%9D%B4%EB%94%A9%20%EC%9C%88%EB%8F%84%EC%9A%B0%20%ED%99%9C%EC%9A%A9%ED%95%98%EA%B8%B0/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-14_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_5.18.39.png" alt="토큰 한계 극복" width="50%">

- 슬라이딩 윈도우 API를 활용한 토큰 제한 극복 방안 연구
- Chat Completion API와 연계하여 긴 대화 처리 구현
- 대화 턴 단위로 오래된 대화 제거하여 메모리 효율화
- Python 기반 모듈화된 구조로 구현하여 재사용성 확보
- 실제 대화 테스트를 통한 성능 
- 실제 네이버 클라우드 포럼(Cookbook)에 게시
<br>

## [6. LLMOps 효율화 (Caching, Prompt Compression)](<LLMOps%20효율화%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e.md>)

<img src="https://github.com/ejehoon/NAVER_CLOUD-CLOVA_Studio/blob/main/LLMOps%20%ED%9A%A8%EC%9C%A8%ED%99%94%20(Caching%2C%20Prompt%20Compression%3AOptimization%2C%20Model%20Cascading%3ARouting%2C%20etc.)/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-28_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB_2.11.37.png" alt="LLMOps 효율화" width="50%">

- Caching, Prompt Compression/Optimization, Model Cascading/Routing 연구
- Context Length 한도, 비용, Latency, 성능 등 다양한 요소 고려
- 사용자와 공급자 관점에서의 효율화 방안 분석
- Input/Output 가공을 통한 비용 및 Latency 최적화 방안 도출
- Context caching, Semantic caching 등 다양한 최적화 기법 연구
<br>

## [7. Search API & Chat-Completion API 비교](Search%20API%20%26%20Chat-Completion%20API%20차이%20비교%200b7b689e945145fc834c58c2771b62b2.md)

<img src="https://github.com/ejehoon/NAVER_CLOUD-CLOVA_Studio/blob/main/Search%20API%20%26%20Chat-Completion%20API%20%EC%B0%A8%EC%9D%B4%20%EB%B9%84%EA%B5%90%20/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-12_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB_12.11.40.png" alt="API 비교" width="50%">

- CLASSIFICATION 작업에서의 API 성능 비교
- 데이터 증가에 따른 효과 분석
- System Prompt 영향도 평가
- 각 API의 장단점 및 적합한 사용 사례 도출


