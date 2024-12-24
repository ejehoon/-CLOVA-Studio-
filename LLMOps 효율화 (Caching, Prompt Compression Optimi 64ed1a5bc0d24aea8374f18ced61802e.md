# LLMOps 효율화 (Caching, Prompt Compression/Optimization, Model Cascading/Routing, etc.)

Status: 시작 전

![스크린샷](LLMOps 효율화 (Caching, Prompt Compression Optimization, Model Cascading Routing, etc.)/스크린샷_2024-06-28_오전_2.11.37.png)




<aside>
💡 **리서치 배경 및 목적**

**배경**

대규모 언어모델을 활용하여 서비스를 개발하는 과정에서 여러 가지 문제에 직면.

1. **Context-Length의 한도**
    - 사용자 측면: 긴 문맥을 필요로 하는 작업에서 제한된 길이로 인해 어려움을 겪고 있음.
    - 공급자 측면: 더 긴 context를 제공하기 위해 모델의 크기 및 메모리 요구사항이 증가하여 인프라 부담.
2. **비용**
    - 사용자 측면: 모델 사용 빈도가 높아지고 대화 맥락 길어질 수록 비용 부담.
    - 공급자 측면: 긴 대화 맥락을 처리하기 위해 필요한 인프라와 리소스 비용이 증가
3. **Latency**
    - 사용자 측면: 응답의 Latency가 길어지면 사용자 경험이 저하
    - 공급자 측면: 여러 사용자의 요청을 동시에 처리해야 하므로 모델의 응답 시간을 줄이는 것이 중요.
4. **성능**
    - 사용자 측면: 높은 정확도와 일관성을 요구하는 작업에서 모델의 성능이 중요.
    - 공급자 측면: 모델의 성능을 최적화하여 다양한 작업에서 높은 정확도를 유지하는 것이 중요.

**목적**

LLM 기반의 서비스를 ****개발하는 **사용자**와, 이와 관련된 인프라 및 모델을 ****공급하는 **공급자 입장**에서

context-length의 한도, 비용, 속도, 성능 등 여러가지 요소들을 고려하며 효율적으로 LLM을 **사용하고 / 공급할 수 있는 방법**이 무엇인지 방법론과 제품 탐색을 통해 파악하기 위함

이를 통해 사용자 경험을 향상시키고, 공급자는 안정적이고 비용 효율적인 인프라를 구축

</aside>

# 문제 상황

| LLM 기반 서비스 개발시 고려해야할, 문제가 생길 수 있는 use-case | 사용자가 체감하는 문제점 |  |  |  | 공급자가 체감하는 문제점 |  | Input 가공 해결책 |  | Ouput 가공 해결책 | Input & Output 가공 해결책 |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
|  | Latency | 비용 | 성능(품질) | ContextLength | 리소스 소모 | 그 외 관점 | Context caching | Prompt 최적화 | Model cascading/routing | Semantic caching | 대화 내역 저장 |
| 큰 분량의 Input을 반복적으로 참고해야할 때(멀티모달 전환 시에도 고려 필요) | 🚨 | 🚨 |  |  | 💣 |  | ✅ | ✅ |  |  |  |
| 동일한 모델로만 Input을 모두 처리할 때
특정 작업에 가장 적절한 모델을 선택하기 어려울 때 | 🚨 | 🚨 | 🚨 |  | 💣 | 특정 모델 병목 현상 or
소외 현상(deprecated) |  |  | ✅ |  |  |
| 동일한 Input을 많은 사용자가 계속 요청할 때 | 🚨 | 🚨 |  |  | 💣 |  |  |  |  | ✅ |  |
| 누적된 맥락 유지와 기억이 필요할 때 | 🚨 |  | 🚨 | 🚨 | 💣 | context length 한계 |  |  |  |  | ✅ |
| 맥락 유지 대화 중 입력된 정보 변경을 원할 때 | 🚨 |  | 🚨 | 🚨 | 💣 | context length 한계 |  |  |  |  | ✅ |

# 해결 방법 개요

| 가공 범위 | 세부 방법 | 방법론 | 사용자의 이점 | 공급자가 고려해야할 점 |  |
| --- | --- | --- | --- | --- | --- |
|  |  |  |  | 엔지니어링 측면 | 기획 및 운영 측면 |
| **Only Input** | Context caching | 자주 사용되는 Input 데이터를 캐싱 | • Input 비용 절감(토큰 재계산 불필요) 및 latency 감소 효과(Caching된 Input에게 더 저렴한 토큰당 가격 부과시)
• 대용량의 여러 인풋, 멀티모달 전환시 latency에 더 효과적으로 대응 가능 | • Context의 내용을 유지할 수 있는 [캐싱 방법론](https://arxiv.org/pdf/2311.04934)
• 저장소 및 SDK의 제공 형태
• 기존 스펙과의 호환성 등 개발 측면 | • 가격 차별화 유무(캐싱된 토큰 수의 별도 가격 체계, 저장 비용 등)
• 기능 제공의 형태(대시보드 및 상품 화면) |
|  | Prompt 최적화 | 사용자가 입력한 프롬프트를 효율적으로 변환 | • 효율적인 프롬프트 작성 방법을 모르는 사용자들을 돕는 가이드라인
• 반복적으로 사용하는 프롬프트 재사용
• Input 비용 절감 및 latency 감소 효과 | • Prompt의 내용 압축 방법론
• 모델별로 상이한 prompt 양식 제공 필요 | • 최적화 도구의 이용 당위성 입증 필요(이용시 accuracy 향상, 사용자 경험 향상 등의 지표) |
| **Only Output** | Model cascading/routing | 여러 모델을 순차적으로 사용하거나 필요한 모델로 데이터를 보내는 방법 | • 여러 작업에 대해 최적의 모델을 사용할 수 있음 -> 정확도 향상 및 latency 감소 효과(여러 모델에 대한 접근 가능 전제하) | • 모델 routing 로직(모델 선택 기준)및 모델별 인프라 할당
• 기존 API들과의 스펙 호환 | • 비용 수준 설정 필요(routing/cascading에 별도의 비용을 부과할 것인가) |
| **Input & Output** | Semantic caching | 의미적 유사성을 기반으로 대화 목록을 캐싱 | • 유사도를 비교 후 대화 내역을 재사용하여 질문 답변 → Input, Output 비용 절감 및 latency 감소 효과 | • 유사도의 정확성
• Caching 저장소의 제공 형태
    ◦ 별도의 저장소 직접 제공 or 외부 패키지 연결만(Redis 등)
    ◦ 사용자 In memory caching을 위한 SDK만 오직 | • 저장소 직접 제공시 비용 산정 필요 |
|  | 대화 내역 저장 기능 | 필요한 대화만 선택적으로 저장하여 대화 진행 | • 요청할 작업에 필요한 대화 내역만 불러올 수 있음 → 불필요한 Input 비용 절감 및 latency 감소 효과
• 대화 내역을 다양한 작업에 대해 독립적으로 활용할 수 있음 | • 기존 API들과의 스펙 호환(특히 Chat Completions)
• 저장소 확보 및 구성의 문제 | • Memory 기능의 제공 범위 고려
    ◦ 챗 인터페이스(플레이그라운드 등) or 오직 API 형태로만 |

# Input을 가공하는 방법

CoT와 같은 고도화된 긴 길이의 프롬프트를 모델에게 전달하거나, 큰 분량의 다양한 문서들을 모델에게 주고 이와 관련된 서로 다른 여러 개의 요청 사항을 전달하는 경우처럼,

**모델이 큰 분량의 Input을 반복적으로 처리해야하는 상황이 자주 발생.**

예를 들어, 논문 1개를 주고 이 논문에 대한 여러 질문들을 할 때, 논문이라는 input은 모델에게 요청 사항과 함께 반복적으로 전달되어 input 토큰의 불필요한 소모가 발생하게 되고, 이는 높은 비용과 latency를 초래할 수 있음.

이를 해결하기 위한 방법으로는 크게 2가지로,

1. **Input의 캐싱을 통해**, 질문이 들어올 경우 질문과 함께 전체 context를 매번 모델에 전달하는 것이 아닌 **캐싱된 input을 전달하는 context caching**
2. **Input 자체를 압축해서 핵심적인 내용만 추려내** 질문과 함께 모델에 전달하는 **prompt 최적화**

**두 방법론을 통해 비용과 latency 모두를 절감할 수 있음**

## Context caching

![스크린샷 2024-07-11 오후 11.55.20.png](LLMOps%20%E1%84%92%E1%85%AD%E1%84%8B%E1%85%B2%E1%86%AF%E1%84%92%E1%85%AA%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-11_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_11.55.20.png)

Context caching은 여러 캐싱 방법론을 활용해 첫 실행에 생성한 input 토큰을 캐싱해 동일한 input 아래에 실행할 다양한 요청을 캐싱된 input으로 처리하는 방법.

용량이 큰 input을 반복적으로 토큰화해 모델에게 전달하는 것이 아닌, 이미 토큰화가 완료된 캐싱된 input을 모델에 전달한다는 점에서 비용과 latency의 감소가 가능하게됨.

### [Google context caching](https://ai.google.dev/gemini-api/docs/caching?lang=python) (Google AI Studio)

Gemini의 Context caching은 여러 요청 간에 계산된 토큰을 재사용할 수 있어, 각 새로운 요청 시 중요한 입력 컨텍스트를 다시 계산할 필요를 줄여줌. 텍스트, 문서, 이미지, 비디오, 오디오의 캐싱을 제공하며 타입에 따라 가격이 다름.

- 2024.06.19(수) 출시

**지원 형태:** Input의 토큰 당 가격을 절감해주는 형식.

| **지원 모델** | **Min_Token** | **Max_Token** | **프롬프트당 최대 이미지 수** | **최대 동영상 길이** | **최대 오디오 길이** | **TTL** |
| --- | --- | --- | --- | --- | --- | --- |
| • Gemini 1.5 Pro | 32k (32,768개) | 2,000k (2,097,152개) | 7,200개 | 2시간 | 약 19시간 | 최소/최대 제한 없음. |
| • Gemini 1.5 Flash | 32k (32,768개) | 1,000k (1,048,576개) | 3,600개 | 1시간 | 약 9.5시간 | 최소/최대 제한 없음. |

**캐싱 로직:** 밝혀지지 않음

**구현 예제:** 대용량 파일을 캐싱하고 이 파일과 관련된 질문을 요청할 때, Input 토큰이 캐싱되었음을 보여주고 있음.

- 캐싱 과정
    
    파일 업로드 (ex. MP3 파일)
    
    - Google Gemini의 클라우드 서비스에 사용할 파일을 업로드.
        
        ![스크린샷 2024-07-11 오후 11.57.24.png](LLMOps%20%E1%84%92%E1%85%AD%E1%84%8B%E1%85%B2%E1%86%AF%E1%84%92%E1%85%AA%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-11_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_11.57.24.png)
        
    
    캐시 생성
    **객체 캐싱**: 작업할 객체를 실제로 캐싱함
    
    ![스크린샷 2024-07-11 오후 11.58.05.png](LLMOps%20%E1%84%92%E1%85%AD%E1%84%8B%E1%85%B2%E1%86%AF%E1%84%92%E1%85%AA%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-11_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_11.58.05.png)
    
    **캐시 사용 비교**
    
    ![스크린샷 2024-07-11 오후 11.59.25.png](LLMOps%20%E1%84%92%E1%85%AD%E1%84%8B%E1%85%B2%E1%86%AF%E1%84%92%E1%85%AA%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-11_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_11.59.25.png)
    
    ![스크린샷 2024-07-11 오후 11.59.40.png](LLMOps%20%E1%84%92%E1%85%AD%E1%84%8B%E1%85%B2%E1%86%AF%E1%84%92%E1%85%AA%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-11_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_11.59.40.png)
    
    ![스크린샷 2024-07-11 오후 11.59.52.png](LLMOps%20%E1%84%92%E1%85%AD%E1%84%8B%E1%85%B2%E1%86%AF%E1%84%92%E1%85%AA%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-11_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_11.59.52.png)
    
    - **Metadata :** 토큰 수에 대한 정보가 담긴 필드
        - prompt_token_count : Input 토큰 수
        - candidates_token_count : Output 토큰 수
        - total_token_count : 전체 토큰 수
        - cached_content_token_count : 캐시된 토큰 수
    
    [**과금 체계](https://ai.google.dev/pricing):** 캐시는 존재하는 시간만큼 계산됨. 사용하는 유형에 따라 전용 가격이 존재. (7월 22일까지 캐시 저장 및 입력 토큰 무료)
    
    - 가격 정보
        
        ![스크린샷 2024-07-12 오전 12.00.25.png](LLMOps%20%E1%84%92%E1%85%AD%E1%84%8B%E1%85%B2%E1%86%AF%E1%84%92%E1%85%AA%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-12_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB_12.00.25.png)
        
        - Gemini 1.0 Pro는 캐싱을 지원하지 않으며, 무료 계정에서는 모델 상관 없이 캐싱을 지원하지 않음
        
        [**컨텍스트 캐싱 가격**](https://cloud.google.com/vertex-ai/generative-ai/pricing#context-caching)
        
        | **Model** | **Feature** | **Type** | **Price ( =< 128K context window )** | **Price ( > 128K context window )** |
        | --- | --- | --- | --- | --- |
        | Gemini 1.5 Flash | Cached Input | Image InputVideo InputText InputAudio Input | 0.000032875 / image0.000032875 / second0.00003125 / 1k characters0.000003125 / second | 0.00006575 / image0.00006575 / second0.0000625 / 1k characters0.00000625 / second |
        |  | Context Cache Storage | Image InputVideo InputText InputAudio Input | 0.000263 / image / hr0.000263/ second / hr0.00025 / 1k characters / hr0.000025 / second / hr |  |
        | Gemini 1.5 Pro | Cached Input | Image InputVideo InputText InputAudio Input | 0.00032875 / image0.00032875 / second0.0003125 / 1k characters0.00003125 / second | 0.0006575 / image0.0006575 / second0.000625 / 1k characters0.0000625 / second |
        |  | Context Cache Storage | Image InputVideo InputText InputAudio Input | 0.0011835 / image / hr0.0011835/ second / hr0.001125 / 1k characters / hr0.0001125 / second / hr |  |
    
    ### [Google context Caching (Vertex AI](https://cloud.google.com/vertex-ai/generative-ai/docs/context-cache/context-cache-overview))
    
    - 2024.06.27(목) 출시
    
    기본적으로 Studio AI와 같은 형태로 제공
    
    **구현 예제:**
    
    - 구현 예제
        
        ![스크린샷 2024-07-12 오전 12.01.24.png](LLMOps%20%E1%84%92%E1%85%AD%E1%84%8B%E1%85%B2%E1%86%AF%E1%84%92%E1%85%AA%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-12_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB_12.01.24.png)
        
        ![스크린샷 2024-07-12 오전 12.01.45.png](LLMOps%20%E1%84%92%E1%85%AD%E1%84%8B%E1%85%B2%E1%86%AF%E1%84%92%E1%85%AA%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-12_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB_12.01.45.png)
        
    
    **Google Context Caching 특이사항**
    
    1. `ttl` 또는 `expire_time으로 캐싱 관리 가능.`
    2. 파일 형태(mp3, mp4, text 등)를 따로 지정해 줄 필요 없음. 단 pdf의 경우 전처리를 따로 해야함. (최소 토큰 수 32k 조건 존재)
    3. Latency 차이가 생각보다 작았음. 예시는 많이 줄어든 경우. Latency 한정해서 RAG나 [Google의 Grounding](https://cloud.google.com/vertex-ai/generative-ai/docs/grounding/overview) 같은 방법이 더 효율적일 것으로 예상.

### [Langfuse](https://langfuse.com/changelog/2024-02-05-sdk-level-prompt-caching)

오픈 소스 LLM 엔지니어링 플랫폼

- 2024.02.06(화) 출시

**지원 형태:** 클라이언트 SDK의 메모리에 프롬프트를 저장 캐싱 TTL 조절 가능

**과금 체계:** 무료

**캐싱 로직:** 밝혀지지 않음.

**구현 예제 :** [공식 개발 가이드 예제](https://langfuse.com/docs/prompts/get-started#caching-in-client-sdks)가 있지만 Caching에 관한 예제는 없음.

## Prompt 최적화

![스크린샷 2024-07-12 오전 12.02.22.png](LLMOps%20%E1%84%92%E1%85%AD%E1%84%8B%E1%85%B2%E1%86%AF%E1%84%92%E1%85%AA%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-12_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB_12.02.22.png)

Prompt 최적화는 주어진 질문이나 요청을 대규모 언어 모델이 효율적으로 처리할 수 있도록, 프롬프트를 간결하고 명확하게 구성하는 과정.

요약 모델이나 사전 훈련된 작은 모델을 사용하여 최적화된 형식으로 재구성된 프롬프트를 생성하여 마지막 단계의 LLM 모델에게 제공하게됨.

예시로, 작성된 프롬프트의 길이를 줄이는 데 사용되는 LLMLingua, 사용자의 요청에 맞춰 최적화된 프롬프트를 생성하는 Generator Prompt 같은 도구가 존재함

### [LLMLingua](https://arxiv.org/abs/2310.05736)

Microsoft에서 발표한 LLMLingua는 작은 언어 모델(예: LLaMa-2-7B)을 활용하여 프롬프트의 길이를 줄이는 기술.

텍스트의 예측 정확도를 측정하는 지표인 perplexity 계산을 통해 중요 정보를 정확히 식별하고 불필요한 부분을 제거.

LLMLingua-2에서는 데이터 증류 기법을 도입하여, 거대 언어모델의 압축 능력을 작은 모델에 전수함으로써 압축 성능을 더욱 향상시키고 토큰 단위 분류 방식을 채택하여 프롬프트 압축의 정확성을 높였음.

| 구분 | 주요 기능 | 문맥 이해 및 평가 |
| --- | --- | --- |
| LLMLingua [(데모](https://huggingface.co/spaces/microsoft/LLMLingua))
*2023.10.09 | • 예산 조절: 프롬프트의 구성요소별로 다른 압축률을 동적으로 할당
• 추가 인스트럭션 튜닝: 큰 모델과 작은 모델 간의 분포 불일치를 줄이는 방법론 | 문서 차원에서 perplexity를 계산하여 프롬프트 내 정보의 중요도를 파악 |
| LongLLMLingua
*2023.10.10 | • 문서 순서 재편: 문서의 중요도 점수를 바탕으로 문서 순서 재정렬
• 후속 복구 방법: 응답과 기존 프롬프트 사이의 관계를 활용하여 원래 내용을 복구 | 문맥 기반 질문의 perplexity를 계산하고, 토큰 단위로 세부 관계를 평가 |
| LLMLingua-2 [(데모)](https://huggingface.co/spaces/microsoft/llmlingua-2)
*2024.03.19 | • 데이터 증류: 중요한 정보를 유지하면서 텍스트 압축, 본래의 텍스트와 압축된 형태의 텍스트 쌍을 포함한 데이터셋 제공
• 토큰 분류 테스크 : 보존 또는 삭제 레이블로 토큰 분류
• 트랜스포머 인코더 모델: 양방향 문맥을 활용하여 프롬프트를 효율적으로 압축 | 토큰 분류 및 데이터 증류를 통해 정보의 중요도를 평가하고, 문맥을 유지 |
- LLMLingua 원리
    
    ![스크린샷 2024-06-25 오전 12.41.20.png](LLMOps%20%E1%84%92%E1%85%AD%E1%84%8B%E1%85%B2%E1%86%AF%E1%84%92%E1%85%AA%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-25_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB_12.41.20.png)
    

### [Claude Prompt generator](https://www.anthropic.com/news/prompt-generator)

- 2024.05.22(수) 출시

Generate Prompt : 사용자가 제공하는 질문이나 요청에 대해 적합한 프롬프트를 생성해주는 도구

**지원 형태 :** Dashboard, Workbench상에서 사용 가능

**과금 체계 :** 가격 공시 X, Claude 3.5 Sonnet 기준 Input $3, Output $15 (백만토큰)

**구현 예제 :** BETA 버전의 Evaluate : 생성된 프롬프트의 변수를 변화시켜 다양한 시나리오에서 프롬프트의 효과성을 시험할 수 있음.

- Claude Workbench
    
    ![스크린샷 2024-06-25 오전 12.41.55.png](LLMOps%20%E1%84%92%E1%85%AD%E1%84%8B%E1%85%B2%E1%86%AF%E1%84%92%E1%85%AA%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-25_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB_12.41.55.png)
    

### [Llama.cpp](https://github.com/ggerganov/llama.cpp)

Llama.cpp는 양자화 기술을 통해 모델 크기를 줄여 메모리 사용량과 연산 자원을 절감하고, 프롬프트 캐싱 기능을 활용하여 동일한 프롬프트에 대한 응답 시간을 단축할 수 있음.

**주요 기능 :**

1. 양자화 : 모델의 매개변수를 작은 숫자로 변환해 메모리 사용을 줄이고 응답 속도를 개선. 양자화된 모델은 GGUF 파일 형식으로 저장됨.
2. 프롬프트 캐싱 : GGUF와 같은 파일 형식으로 프롬프트 처리 후의 상태를 저장.
3. 모델 분할 : 대형 모델을 여러 GPU에서 분산 처리해 프롬프트 응답 속도를 최적화.

**구현 예제 :** 모델을 사용하기 위해서는 Hugging Face 또는 GitHub에서 모델 파일을 로컬에 다운로드해야 함. [Hugging Face](https://huggingface.co/spaces/ggml-org/gguf-my-repo)에서 양자화와 모델 변환 과정을 시각적으로 보여주는 예제도 확인 가능.

- Llama.cpp 예제
    
    ![스크린샷 2024-06-21 오후 4.02.28.png](LLMOps%20%E1%84%92%E1%85%AD%E1%84%8B%E1%85%B2%E1%86%AF%E1%84%92%E1%85%AA%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-21_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_4.02.28.png)
    

# **Output을 가공하는 방법**

비용, 품질, 속도 등 다양한 변수를 고려한다면 항상 동일한 LLM을 모든 Input에 대해 사용하는 것은 효율적이지 않을 수 있음

예를 들어, 아주 간단한 요청임에도 무거운 모델을 사용하게 된다면 비용과 latency를 모두 놓칠 수 있음.

Input에 대해 가장 최적의 모델을 찾아서 요청을 보내주는 cascading / routing이 이 문제를 해결할 수 있는 대안이 될 수 있습니다.

## **Model cascading/routing**

![스크린샷 2024-07-12 오전 12.03.12.png](LLMOps%20%E1%84%92%E1%85%AD%E1%84%8B%E1%85%B2%E1%86%AF%E1%84%92%E1%85%AA%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-12_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB_12.03.12.png)

Model cascading/routing은, 모델의 수준을 단계적으로 높여 특정 기준을 만족하는 모델의 답변을 채택하거나, 모델의 수준에 상관없이 여러 다양한 모델들 속 기준을 만족하는 모델의 답변을 선택하는 방법론.

"특정 기준"은 이 기능을 제공하는 상품들마다 다르며, 점수 측정용 모델을 따로 만들어 사용하는 경우도 있고, 기준 책정 방식을 공개하지 않는 경우도 있음.

### [Martian](https://www.withmartian.com/)

**지원 형태:** Input에 맞는 최적의 model을 사용할 수 있는 Model router와 이 결과를 사전에 미리 테스트해볼 수 있고 성능에 대한 데이터를 측정할 수 있는 Model gateway를 API로 제공

**과금 체계:** 개인 유저(2,500개 요청까지 무료, 이후 20$/5,000개) or Enterprise(가격 직접 상담, 월간 연간 결제 가능)

**구현 예제:** Gateway를 사용해 Martian 홈페이지 내에서 Router를 빠르게 테스트해볼 수 있음

- 실행 화면
    
    ![스크린샷 2024-06-25 오전 12.44.19.png](LLMOps%20%E1%84%92%E1%85%AD%E1%84%8B%E1%85%B2%E1%86%AF%E1%84%92%E1%85%AA%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-25_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB_12.44.19.png)
    

**Routing 로직**: 공개 X

**특이 사항:** **여러 LLM의 API Key 상관없이, 오직 Martian의 Key만 발급받으면 되는 구조**. Router의 성능을 평가할 수 있는 벤치마크인 [RouterBench](https://blog.withmartian.com/post/router-bench)를 개발했음.

[**파라미터:**](https://docs.withmartian.com/martian-model-router/getting-started/hello-world) "willingness_to_pay"와 "max_cost"를 통해 지불하고자하는 가격의 상/하한선 내에서 model routing을 실행할 수 있음

### [LiteLLM](https://www.litellm.ai/)

**지원 형태:** 별도의 API가 아닌 SDK를 통해 Routing 기능을 사용자가 직접 코드를 통해 실행할 수 있게하며, **모든 LLM을 OpenAI Format과 동일한 형태로 실행할 수 있는 SDK를 가지고 있다는점이 내세우는 주요 강점**

**과금 체계:** Demo 일정을 잡으면 사측이 화상 회의를 통해 상담을 도와줌. 가격 공시는 홈페이지에 X

**구현 예제:** 별도의 인터페이스를 통해 Demo를 보여주진 않고, [Docs](https://docs.litellm.ai/docs/routing)에서 코드 실행 결과 정도를 확인해볼 수 있음.

[**Routing 로직**:](https://docs.litellm.ai/docs/routing#advanced---routing-strategies-%EF%B8%8F) 다양한 Routing 로직을 설명하고 제공하며, 각 방법에 따라 파라미터를 사용자가 직접 설정할 수 있음.

Rate limit, Latency, cost, least-busy 등 사용자가 LLM 사용을 하며 고민하는 결정 변수들을 모두 routing 전략으로 선택할 수 있음.

**특이 사항:** Routing은 LiteLLM이 통합된 SDK 내에서 지원하는 한 가지의 기능이고, 임베딩, 멀티모달 등 다양한 기능을 수행할 수 있는 LLM을 프록시 서버의 형태를 통해 제공하는 것이 LiteLLM의 본체.

Routing은 성능과 비용 절감을 우선 순위로 두고 성능을 홍보하기 보다는, LLM을 사용자가 입맛대로 조절하고 편리하게 사용할 수 있는 본체의 기능중 하나로서 보여주고 있음.

**파라미터:** 각 Routing strategy 내에 대응되는 파라미터가 있고, [비용과 관련된 사용자의 예산을 전역 변수로 설정](https://docs.litellm.ai/docs/budget_manager)할 수 있음

### [Unify.ai](https://unify.ai/)

**지원 형태:** API를 통해 Routing 기능을 사용 가능하며, **모델과 데이터셋을 선택해 직접 [Router를 학습](https://unify.ai/docs/interfaces/building_router.html)시킬 수 있음**

**과금 체계:** 확인 불가. Contact 필수

**구현 예제:** Router 학습의 경우 학습 인터페이스만 확인이 가능하며, 그 외에 Router의 실행 결과는 구동 코드만 [docs](https://github.com/unifyai/unify)나 [깃허브](https://github.com/unifyai/unify)를 통해 확인할 수 있음

- Router 학습 인터페이스
    
    ![스크린샷 2024-06-25 오전 12.45.46.png](LLMOps%20%E1%84%92%E1%85%AD%E1%84%8B%E1%85%B2%E1%86%AF%E1%84%92%E1%85%AA%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-25_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB_12.45.46.png)
    

**Routing 로직:** 학습을 하지 않은 기본 Router의 경우, 비용이나 latency를 옵션으로 설정할 수 있으며, 학습된 Router는 학습시 해당 옵션들을 설정해 학습할 수 있음

**파라미터:** Routing 로직의 비용과 관련된 input-cost, output-cost와 latency와 관련된 ITL, TTFT, Token/sec 등을 설정 가능함

# **Input & Output을 모두 가공하는 방법**

**반복적인 Input에 대해 매번 Input을 모델에게 전달하게 된다면, 매 회 비용이 발생하게 됨.**

만약 자주 요청하는 Input과 이에 대한 Output을 모두 저장해두고 이와 유사하거나 동일한 Input이 들어온다면, 모델에게 요청하지 않고 준비된 Output을 바로 출력할 수 있음.

이를 위한 방법이, 다양한 어플리케이션과 프레임워크에서 제공하는 **Semantic caching**

위의 반복적인 Input을 요청하는 상황보다 좀 더 범용적인 상황은, **이전에 요청했던 다양한 Input과 이에 대한 Output을 바탕으로 다음 Input을 요청하는 경우.**

다음 요청에 필요한 이전 Input-Output 맥락들을 저장하고 관리할 수 있다면, 연속적인 요청에 대해서 답변의 품질을 향상시킬 수 있음.

이를 위한 방법이, 다양한 어플리케이션과 프레임워크에서 제공하는 **memory**

## **Semantic caching**

![스크린샷 2024-07-12 오전 12.03.28.png](LLMOps%20%E1%84%92%E1%85%AD%E1%84%8B%E1%85%B2%E1%86%AF%E1%84%92%E1%85%AA%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-12_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB_12.03.28.png)

Semantic caching 기능은 대부분이 Redis, Cassandra, MongoDB Atlas, SQAlchemy 등의 저장소와 임베딩이 결합한 형태로 구현됨.

기존의 Input-Output 쌍들을 캐싱한 후, 새롭게 들어온 Input에 대해 캐싱된 Input-Output 쌍들의 Input과 대조합니다. 특정 기준 이상 유사함이 판단되었을 경우,

새롭게 들어온 Input을 LLM에 요청하지 않고 동일하다고 판단된 캐싱된 Input에 대응하는 Output을 바로 가져오게됨

Semantic caching은 LangChain과 같은 여러 LLM과 DB를 통합해 제공하는 프레임워크에서 SDK 형식으로 제공될 수도, GPTCache(Zillizcloud) 처럼 직접 제공될 수도 있음.

### [GPTCache](https://github.com/zilliztech/GPTCache?tab=readme-ov-file)

LLM 쿼리를 위한 시멘틱 캐시를 생성하는 라이브러리

**지원 형태:** 기본적으로 Python 라이브러리로 제공되며, Docker 이미지로도 제공되어 다양한 환경에서 사용 가능.

**과금 체계:** 무료 오픈소스 프로젝트.

**구현 예제:** GitHub 저장소에서 다양한 사용 사례와 튜토리얼 제공.

**캐싱 로직:** 시멘틱 유사성 기반. 임베딩 알고리즘으로 쿼리를 벡터화하고, 벡터 저장소에서 유사한 쿼리 검색.

**특이 사항:** 임베딩 생성 방식, 데이터 저장소, 유사도 평가 기준, 캐시된 응답의 후처리 방식, 유사도 임계값 등을 조정 가능함.

- 적용 로직
    
    ![스크린샷 2024-06-25 오전 12.46.55.png](LLMOps%20%E1%84%92%E1%85%AD%E1%84%8B%E1%85%B2%E1%86%AF%E1%84%92%E1%85%AA%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-25_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB_12.46.55.png)
    

### [PromptWatch.io](http://promptwatch.io/)

대시보드를 통해 프롬프트 모니터링 및 최적화 서비스를 제공.

**지원 형태:** 대쉬보드나 API를 통해 사용자의 애플리케이션과 통합 가능.

**과금 체계:** 확인 불가. Contact 필수

**구현 예제:** 웹 대시보드를 통해 프롬프트 성능, 사용량, 비용 등을 시각화하여 보여줌. 실제 사용 사례나 데모는 별도로 제공하지 않음.

**캐싱 로직:** 정확한 공개 X, 97% 이상 유사한 경우 캐시된 응답을 재사용

**파라미터:** 임베딩 모델, 캐시 저장소, 프롬프트 길이 제한, 유사도 임계값

## **대화 내용의 보존과 관리**

![스크린샷 2024-07-12 오전 12.03.49.png](LLMOps%20%E1%84%92%E1%85%AD%E1%84%8B%E1%85%B2%E1%86%AF%E1%84%92%E1%85%AA%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-12_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB_12.03.49.png)

모델과의 대화 내역중 다음 실행에 필요한 맥락을 담은 대화 턴(turn)들만을 선택적으로 저장하는 것과 같이 개별 대화 턴을 관리할 수 있다면,

다음 작업을 위해 필요한 맥락(대화 턴들)만을 모델에 전송하고 새로운 작업을 진행할 수 있기 때문에 Input 토큰 수 절감 및 Input 용량이 줄어듦에 따라 latency도 감소시킬 수 있음.

또한 어제 했던 실행들 중 필요한 맥락들만 불러와 지금의 작업에 활용할 수 있어 기억력이 뛰어난 Assistant로서 작동할 수 있게 됨.

이런 대화 내용의 보존과 관리를 구현한 기능은 OpenAI의 Assistant 속 Thread와 Memory, 혹은 LangChain과 같은 프레임워크의 Memory 기능.

### [OpenAI Thread](https://platform.openai.com/docs/api-reference/threads)

대화 내역을 저장할 수 있는 기능

**지원 형태:** Assistant API의 한 가지 기능으로서, ChatGPT와 같은 인터페이스 상에 존재하는 것은 아님

**과금 체계:** Thread는 API 실행시 Input으로 들어가게 되기 때문에 기존의 과금 체계를 그대로 따름

**주요 기능:**

1. thread_id가 존재해 개별적으로 관리할 수 있음
2. 한 개의 thread_id 안에 "message"라는 기능을 통해 싱글턴의 대화, 멀티턴의 대화들을 넣어서 thread를 편집할 수 있음
3. 모델의 처리 토큰 한도를 넘어가게 될 경우, thread API에 [truncation_strategy](https://platform.openai.com/docs/api-reference/runs/createRun#runs-createrun-truncation_strategy)라는 변수의 설정을 통해 지우는 양을 조절할 수 있음
    1. [max_prompt_tokens](https://platform.openai.com/docs/api-reference/runs/createRun#runs-createrun-max_prompt_tokens)를 통해 전체 input(prompt라고 표현)의 한도를 설정하고, [max_completion_tokens](https://platform.openai.com/docs/api-reference/runs/createRun#runs-createrun-max_completion_tokens)를 통해 답변의 한도를 설정할 수 있음truncation_strategy는 max_prompt_tokens를 넘게 되면 자동으로 일부 thread를 삭제하거나, 사용자가 설정한 n개만을 유지시키게 작동함

[**Memory**](https://openai.com/index/memory-and-new-controls-for-chatgpt/): ChatGPT 인터페이스 상에서 대화 내역을 저장하고 일부를 선택해 대화에 참고시킬 수 있는 Thread와 유사한 기능 **(한국 지원 X)**

- "Custom instructions"라는 기능을 통해 GPT가 참고해야하는 instruction과 답변의 톤앤매너를 조절하기 위한 지시 사항을 입력할 수 있음
- 대화시, browsing 진행 여부, 이미지 생성(달리) 여부, 코드 생성 여부를 설정할 수 있어 customization 기능이 향상됨
- custom instructions 기능 화면
    
    ![스크린샷 2024-06-25 오전 12.47.30.png](LLMOps%20%E1%84%92%E1%85%AD%E1%84%8B%E1%85%B2%E1%86%AF%E1%84%92%E1%85%AA%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-25_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB_12.47.30.png)
    

### [LangChain Memory](https://python.langchain.com/v0.1/docs/modules/memory/)

LangChain이 제공하는 LLM과의 대화 내역 저장 기능으로, Chain 안에서 사용 가능한 기능

- Chain: Langchain만의 구조화된 언어(LCEL)를 활용해 여러 기능을 엮어 end-to-end 시스템을 만들 수 있게하는 일종의 틀
- LangChain의 Memory 구조도
    
    ![스크린샷 2024-06-25 오전 12.48.33.png](LLMOps%20%E1%84%92%E1%85%AD%E1%84%8B%E1%85%B2%E1%86%AF%E1%84%92%E1%85%AA%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-25_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB_12.48.33.png)
    

**지원 형태:** LangChain을 통해 Chain을 구성할 때, Chain의 일부로 작동함. Memory 기능을 사용하기 위해선 답변을 할 LLM이 있어야하기 때문에, 결국 Chain 안에 Memory가 반드시 있어야함

**주요 기능:**

1. Memory는 "Chat Messages"라는 모듈을 통해 직접 사용자가 대화 내역을 추가할 수도, Chain 안에서 모델과의 input/output을 그대로 저장할 수도 있음
    1. OpenAI의 thread가 "message"라는 기능을 통해 내역을 편집할 수 있는 것과 유사한 구조
2. Chain 안에 Memory를 넣어 일련의 작업을 완성시킨 후, 이를 Agent에 넣어 customization 할 수 있음
3. 단순히 질문/응답의 Chat 형태만 기록될 뿐만 아니라 RAG의 retrieval 결과처럼 다른 Chain의 Output을 Memory의 Input으로 넣어 활용할 수도 있음
    - a. Chain의 Input을 기록 가능한 Memory
        
        ![스크린샷 2024-06-25 오전 12.49.21.png](LLMOps%20%E1%84%92%E1%85%AD%E1%84%8B%E1%85%B2%E1%86%AF%E1%84%92%E1%85%AA%20(Caching,%20Prompt%20Compression%20Optimi%2064ed1a5bc0d24aea8374f18ced61802e/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-25_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB_12.49.21.png)
        

**특이사항:** Memory 대부분의 기능은 아직 배포할 수 없고(not production ready), LCEL과 Chain이 지속적으로 업데이트 됨에 따라, 가장 최신의 Chain 문법 및 구조와 호환되지 않는다고 함
