# 모델 평가 - Newport (HCX-T04) vs Calgary(HCX-003)

Status: 시작 전

## 분석 개요

- **HCX 종합 Qualitative Analysis**
    - **평가 모델**: HCX-003, HCX-T04 모델
    - **평가 방법**: 모델 기반의 평가 + 정성 평가
    - **성능** **분석 대상**: 12개의 세부 Skils x 10개의 도메인 x 5개 레벨의 난이도
        - 예) Skill: 논리적 올바름, 사실성, 이해력, 가독성 등
        - 예) 도메인: 인문, 사회, 수학 등
    - **cf. LLM 평가 방법**
        - https://docs.google.com/spreadsheets/d/1Ax0HSqTvMGrfwpz4swMNGUdXVbc0811supIYbrf7uQ8/edit?gid=930470063#gid=930470063 GPT-4-turbo 모델을 채택하여 평가 기준을 알려주고 1~7점까지 점수를 산정하는 방식으로 진행하였습니다.

본 글은 약 380여개의 문항으로 구성된 System Prompt 평가셋을 통해 평가한 두 모델

**Calgary(HCX-003) vs Newport (HCX-T04)**중 대표성 있는 소수 문항을 선별해 요약정리해둔 문서입니다.

**현재 서빙되고 있는 HyperCLOVA X의 Calgary(HCX-003)와 차기 후보 Newport (HCX-T04)의 비교를 통해 후보 모델의 개선 정도를 파악**하기 위함입니다.

Calgary(HCX-003)와 Newport (HCX-T04)의 System Prompt 평가셋에 대한 상세 답변과 평가 결과는 |
[Calgary&Newport 모델 평가 결과](https://docs.google.com/spreadsheets/d/1OafSn645e6uXPZ_08a5EFSaoA4xhdXSHjBp5I-bX6zw/edit#gid=1471504714) 에서 확인할 수 있습니다.

![스크린샷 2024-06-14 오후 6.07.12.png](https://github.com/ejehoon/NAVER_CLOUD-CLOVA_Studio/blob/main/%EB%AA%A8%EB%8D%B8%20%ED%8F%89%EA%B0%80%20-%20Newport%20(HCX-T04)%20vs%20Calgary(HCX-003)/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-14_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_6.07.12.png)

## 평가 인사이트

**주요 내용**

### **<Newport의 개선된 점> : [점수 차이가 큰 영역](https://docs.google.com/spreadsheets/d/1OafSn645e6uXPZ_08a5EFSaoA4xhdXSHjBp5I-bX6zw/edit?gid=1735365970#gid=1735365970)**

- **Newport가 6개의 기본 영역을 포함한 대부분의 영역에서 Calgary를 상회하는 역량을 보여주었습니다.**
- 특히 구조화하기 같은 표를 생성하거나 문서를 작성하는 부분에서 크게 개선된 것을 확인했습니다.
- Newport가 Calgary보다 낮은 역량을 보이는 2가지 세부 영역(기억하기 → 정렬하기, 분석하기 → 순위 설정하기)은 evaluator인 GPT-4-turbo의 평가 오류(Calgary의 고평가)가 있었습니다.

### **<Newport의 부족한 부분> : [저품질 영역](https://docs.google.com/spreadsheets/d/1OafSn645e6uXPZ_08a5EFSaoA4xhdXSHjBp5I-bX6zw/edit?gid=253050218#gid=253050218)**

- **Calgary 대비 확실히 부족한 영역**
    - 도메인 기능 → 코딩 능력 영역에서, 들여쓰기를 하지 않아 Calgary에 비해 부족한 모습을 보였습니다.
- **Calgary를 포함해 여전히 부족한 역량을 보여주는 영역**
    - 기억하기 → 정렬하기 → 내림/오름차순의 나열, 응용하기 → 수행하기 → n개 중 m개 제외, 분석하기 → 순위설정하기 → 상위 n개 나열 등1) Input을 이해하는 지식이 필요하고 2) 이 지식을 적용해 출력의 일부를 제어하는 두 단계로 이루어진 작업에 대해선 역량의 부족함을 여전히 보였습니다.

### **<Newport의 고품질 답변> : [고품질 답변(스프레드시트에서 확인)](https://docs.google.com/spreadsheets/d/1OafSn645e6uXPZ_08a5EFSaoA4xhdXSHjBp5I-bX6zw/edit?gid=403499291#gid=403499291)**

- 7점 만점 영역에 대해선 Calgary와 동일하거나 더 우수한 답변의 품질을 보여주었습니다.

### **<Newport의 특이사항>**

- "관련해 궁금하신 점이 있으시면 말씀해 주세요.." 등의 고정 문구가 빈번하게 출력되는 현상이 있습니다. (60/387)

## **평가 영역별 대표 문항에 대한 답변 및 점수**

**System Prompt 평가셋은 Bloom's Taxonomy 기반 6개 기본 영역 + 실제 사용 사례 기반의 "도메인 기능" 영역 + 조건들의 복합체로 구성된 "복합 지시 영역" 총 8개 영역으로 구성되어있습니다. Calgary(HCX-003)와 Newport (HCX-T04)의 인퍼런스 결과 및 GPT-4-Turbo가 이를 평가한 점수입니다.**

- 평가셋은 "8개의 대분류 → 세부 분류 → 세부 분류 1개 당 여러 개의 평가 문항들"의 형태로 구성되어있습니다. 아래의 표는 세부 분류당 대표 문항 1개씩을 선정하고 Calgary와 Newport의 답변 및 GPT-4-Turbo가 이 두 모델의 답변을 평가한 결과를 정리했습니다.
    - **"Bloom's revised taxonomy 6개 분류" 이름의 그래프는, "기억하기"부터 "창조하기" 총 6개 영역을 한 눈에 표시한 자료입니다.**
- GPT-4는 각 문항의 System Prompt와 Input을 참고하여 만들어진 Rationale을 참고해 채점을 진행하며, 최저점 1점 ~ 최고점 7점으로 각 답변을 평가합니다.
- 
    
    ![스크린샷 2024-06-14 오후 6.09.00.png](https://github.com/ejehoon/NAVER_CLOUD-CLOVA_Studio/blob/main/%EB%AA%A8%EB%8D%B8%20%ED%8F%89%EA%B0%80%20-%20Newport%20(HCX-T04)%20vs%20Calgary(HCX-003)/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-14_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_6.09.00.png)
    
    ## 정성 평가
    
    **정량적인 수치만으로 포착되지 않는 모델별 답변의 세부 품질 차이 및 특징 등을 더 면밀하게 분석한 정성 평가 결과입니다. 아래 3가지 항목에 대한 상세 분석을 진행했습니다. 각 평가 문항의 답변에 대한 정성 평가 내용은, 글 서두에 첨부한 링크를 통해 원본 파일에서 확인할 수 있습니다.**
    
    **점수 차이가 큰 항목**
    
    - 한 모델이 다른 모델에 비해 3점 이상 높은 점수를 받은 문항들에 대한 비교입니다.**기존 Calgary에서 부족했던 영역들에 대해 얼마나 개선되었는지를 확실히 확인하기 위한 비교**입니다.
    
    **저품질 항목**
    
    - 7점 만점에 2점 이하를 받은 문항들에 대한 비교입니다.**Newport에서도 여전히 개선되지 않은 영역들을 살펴보며 차기 경량화 모델에서 개선시켜야할 역량들을 확인하기 위한 비교**입니다.
    
    **고품질 항목**
    
    - 7점 만점에 7점을 받은 문항들에 대한 비교입니다.**둘 다 잘하는 영역이지만 톤&매너 상 더 우수한 점이 있는지 혹은 모델별 답변 스타일에 특징이 있는지 알아보기 위한 비교**입니다.**고품질 항목에 대한 비교는**, 아래 글이 아닌 **글 서두에 언급한 평가 스프레드시트 파일 링크**에서 확인할 수 있습니다("Newport vs Calgary 둘 다 만점" 시트)
    
    ## LLM 평가 모델 (GPT-4-Turbo)의 점수
    
    **KPI 확인용 비교 (w/ GPT-4-Turbo)**
    
    GPT4 vs HCX-DASH-T02 vs HCX-T04 vs HCX-003
    
    - **Bloom’s revised taxonomy 6개 영역**
        
        ![스크린샷 2024-06-14 오후 6.37.34.png](https://github.com/ejehoon/NAVER_CLOUD-CLOVA_Studio/blob/main/%EB%AA%A8%EB%8D%B8%20%ED%8F%89%EA%B0%80%20-%20Newport%20(HCX-T04)%20vs%20Calgary(HCX-003)/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-14_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_6.37.34.png)
        
    - **도메인 기능 Domain Specific**
        
        ![스크린샷 2024-06-14 오후 6.37.51.png](https://github.com/ejehoon/NAVER_CLOUD-CLOVA_Studio/blob/main/%EB%AA%A8%EB%8D%B8%20%ED%8F%89%EA%B0%80%20-%20Newport%20(HCX-T04)%20vs%20Calgary(HCX-003)/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-14_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_6.37.51.png)
        
    - **복합 지시 Use-case based Multi Task**
        
        ![스크린샷 2024-06-14 오후 6.38.02.png](https://github.com/ejehoon/NAVER_CLOUD-CLOVA_Studio/blob/main/%EB%AA%A8%EB%8D%B8%20%ED%8F%89%EA%B0%80%20-%20Newport%20(HCX-T04)%20vs%20Calgary(HCX-003)/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-14_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_6.38.02.png)
        
    
    https://docs.google.com/spreadsheets/d/1Ax0HSqTvMGrfwpz4swMNGUdXVbc0811supIYbrf7uQ8/edit?gid=605769460#gid=605769460
