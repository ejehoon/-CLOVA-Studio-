# Search API & Chat-Completion API 차이 비교

Status: 시작 전

![스크린샷 2024-07-12 오전 12.11.40.png](Search%20API%20&%20Chat-Completion%20API%20%E1%84%8E%E1%85%A1%E1%84%8B%E1%85%B5%20%E1%84%87%E1%85%B5%E1%84%80%E1%85%AD%200b7b689e945145fc834c58c2771b62b2/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-12_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB_12.11.40.png)

<aside>
💡 **결과 정리**

국가를 대륙별로 분류하는 CLASSIFICATION 작업에서 Chat-Completion API는 높은 정확도를 보이며, 데이터 양이 증가함에 따라 분류 오류가 감소하며 튜닝의 효과가 나타났습니다.

반면, Search API를 사용한 경우 튜닝의 의미가 없을 만큼 오류 발생 비율이 매우 높았고, 데이터 증가에 따른 효과 또한 없었습니다.

원인을 찾아보고자, 학습과 평가 데이터셋의 System에 지시사항을 추가하고 테스트를 다시 진행하였습니다.

1. 학습 데이터 System_Prompt 지시사항 포함
2. 평가 데이터의 System 지시사항 포함
3. 분류 카테고리 동물 종류로 변경

테스트 진행 결과 기존 System_Prompt 지시사항이 제외된 결과와 크게 다르지 않았습니다.

결과적으로, 동일한 작업을 진행했을 때 **Chat-Completion API와 Search API 차이가 매우 큰다는 결론을 얻었습니다.**

</aside>

## **배경 및 목적**

CLASSIFICATION 튜닝 과정에서 **Chat-Completion API와 Search API를 사용했을 때의 성능 차이를 비교 분석**하기 위해 국가를 대륙에 따라 분류하는 작업을 진행했습니다.

대륙은 FIFA의 대륙별 연맹(* 2021년 기준)에 따라 아시아, 아프리카, 북중미와 카리브 지역, 남아메리카, 오세아니아로 나누어 분류 작업을 진행했습니다.

구체적으로, Chat-Completion API와 Search API의 튜닝 모델에서 발생하는 오류 비율을 비교하고, 학습 데이터 건수 증가가 분류 오류 감소에 미치는 영향을 관찰하였습니다.

테스트에 대한 자세한 결과는 아래 스트레드시트를 참고하실 수 있습니다.

[구글 스프레드시트](https://docs.google.com/spreadsheets/d/1mxFFDa80quq-E52RlIWH7L6a2_nKMEawxOsmu3kGL-g/edit?gid=970306207#gid=970306207)

## **데이터셋 만들기 & 튜닝 과정**

- **대상 태스크:** 국가를 FIFA의 대륙별 연맹에 따라 분류
- **튜닝 모델:** HCX-003
- **튜닝 세팅:**
    - method: LORA taskType: CLASSIFICATIONtrainEpochs: 4learningRate: 1e-4Top-K: 0Top-P: 0.6Max Tokens: 256Repetition Penalty: 1.2Temperature: 0.5
- **튜닝 데이터:**
    - 학습 데이터 구조 (샘플 5개)
    
    | System_Prompt | C_ID | T_ID | Text | Completion |
    | --- | --- | --- | --- | --- |
    |  | 0 | 0 | 아일랜드 | 유럽 |
    |  | 1 | 0 | 과들루프 | 북중미와 카리브 지역 |
    |  | 2 | 0 | 몰디브 | 아시아 |
    |  | 3 | 0 | 가나 | 아프리카 |
    |  | 4 | 0 | 파라과이 | 남아메리카 |
    - 평가 데이터 구조 (샘플 5개)
    
    | **system** | **user** | **content** |
    | --- | --- | --- |
    |  | 아일랜드 |  |
    |  | 과들루프 |  |
    |  | 몰디브 |  |
    |  | 가나 |  |
    |  | 파라과이 |  |
    - 학습 데이터: FIFA(국제 축구 연맹)에 가입한 220개국을 대륙 순서에 상관없이 랜덤하게 섞고, 반복하여 50, 220, 440 ... 4,400, 11,000개국으로 점진적으로 데이터 양을 증가
    - 분류 카테고리: FIFA에 속한 정회원 국가 목록을 아시아, 아프리카, 북중미와 카리브 지역, 남아메리카, 오세아니아 총 5대륙으로 분류
    - 검증 데이터: FIFA 정회원국 220개국 데이터를 검증 셋으로 사용

## **결과**

Chat-Completion API는 높은 정확도를 보이며, 데이터 양이 증가함에 따라 분류 오류가 감소하며 튜닝의 효과가 나타난 반면, Search API를 사용한 경우 오류 발생 비율이 매우 높고, 데이터 증가에 따른 튜닝 효과가 없었음.

**오류 발생 비율**

| **학습 데이터 건수** | **Train Loss** | **Chat-Completion API** | **Search API** |
| --- | --- | --- | --- |
| HCX-003 |  | 8/220 (3.6%) |  |
| 220개 | 0.32 | 1/220 (0.5%) | 179/220 (81.3%) |
| 440개 | 0.0022 | - | 179/220 (81.3%) |
| 660개 | 0.0004 | - | 180/220 (81.8%) |
| 880개 | 0.0002 | - | 175/220 (79.5%) |
| 1,100개 | 0.0002 | - | 179/220 (81.3%) |
| 2,200개 | 0 | - | 177/220 (80.4%) |
| 4,400개 | 0 | - | 179/220 (81.3%) |
| 11,000개 | 0 | - | 179/220 (81.3%) |

## **추가 진행**

**Search API의 결과가 문제가 있다 판단**하여, 학습 데이터셋과 평가 데이터셋의 구조 변화를 시도하여, 집중적으로 관찰함.

### **데이터셋 구조 변화**

학습과 평가 데이터셋의 system 컬럼에 지시사항을 추가해 테스트를 진행. (학습 데이터 220개 기준)

아래 학습 데이터와 해당 taskId의 평가 결과를 정리하였습니다.

- 학습 데이터: System_Prompt, C_ID, T_ID, Text, Completion
- 평가 결과 : system, user, content
1. **학습 데이터 System_Prompt 지시사항 포함**
    - 펼치기
        
        **기존 System_Prompt 지시사항 제외**
        
        | **학습 데이터 System_Prompt 지시사항 제외** |  |  |  |  | **평가 결과** |  |  |
        | --- | --- | --- | --- | --- | --- | --- | --- |
        | **System_Prompt** | **C_ID** | **T_ID** | **Text** | **Completion** | **system** | **user** | **content** |
        |  | 0 | 0 | 아일랜드 | 유럽 |  | 아일랜드 | 북중미와 카리브 지역 |
        |  | 1 | 0 | 과들루프 | 북중미와 카리브 지역 |  | 과들루프 | 오세아니아 |
        |  | 2 | 0 | 몰디브 | 아시아 |  | 몰디브 | 북중미와 카리브 지역 |
        |  | 3 | 0 | 가나 | 아프리카 |  | 가나 | 북중미와 카리브 지역 |
        |  | 4 | 0 | 파라과이 | 남아메리카 |  | 파라과이 | 북중미와 카리브 지역 |
        
        **변경된 학습 데이터 System_Prompt 지시사항 포함**
        
        | **학습 데이터 System_Prompt 지시사항 포함** |  |  |  |  | **평가 결과** |  |  |
        | --- | --- | --- | --- | --- | --- | --- | --- |
        | **System_Prompt** | **C_ID** | **T_ID** | **Text** | **Completion** | **system** | **user** | **content** |
        | - 사용자가 제공하는 데이터를 분류 기준에 맞춰 분류합니다.- 사용자가 제시한 분류 기준을 명확하게 따라야합니다.- 국가를 FIFA 대륙 연맹 기준으로 분류하세요.- FIFA 대륙 연맹은 남아메리카, 북중미와 카리브 지역, 아시아, 아프리카, 오세아니아, 유럽으로 나뉩니다.- 대륙명만 답변하세요 | 0 | 0 | 아일랜드 | 유럽 |  | 아일랜드 | 북중미와 카리브 지역 |
        | - 사용자가 제공하는 데이터를 분류 기준에 맞춰 분류합니다.- 사용자가 제시한 분류 기준을 명확하게 따라야합니다.- 국가를 FIFA 대륙 연맹 기준으로 분류하세요.- FIFA 대륙 연맹은 남아메리카, 북중미와 카리브 지역, 아시아, 아프리카, 오세아니아, 유럽으로 나뉩니다.- 대륙명만 답변하세요 | 1 | 0 | 과들루프 | 북중미와 카리브 지역 |  | 과들루프 | 북중미와 카리브 지역 |
        | - 사용자가 제공하는 데이터를 분류 기준에 맞춰 분류합니다.- 사용자가 제시한 분류 기준을 명확하게 따라야합니다.- 국가를 FIFA 대륙 연맹 기준으로 분류하세요.- FIFA 대륙 연맹은 남아메리카, 북중미와 카리브 지역, 아시아, 아프리카, 오세아니아, 유럽으로 나뉩니다.- 대륙명만 답변하세요 | 2 | 0 | 몰디브 | 아시아 |  | 몰디브 | 북중미와 카리브 지역 |
        | - 사용자가 제공하는 데이터를 분류 기준에 맞춰 분류합니다.- 사용자가 제시한 분류 기준을 명확하게 따라야합니다.- 국가를 FIFA 대륙 연맹 기준으로 분류하세요.- FIFA 대륙 연맹은 남아메리카, 북중미와 카리브 지역, 아시아, 아프리카, 오세아니아, 유럽으로 나뉩니다.- 대륙명만 답변하세요 | 3 | 0 | 가나 | 아프리카 |  | 가나 | 북중미와 카리브 지역 |
        | - 사용자가 제공하는 데이터를 분류 기준에 맞춰 분류합니다.- 사용자가 제시한 분류 기준을 명확하게 따라야합니다.- 국가를 FIFA 대륙 연맹 기준으로 분류하세요.- FIFA 대륙 연맹은 남아메리카, 북중미와 카리브 지역, 아시아, 아프리카, 오세아니아, 유럽으로 나뉩니다.- 대륙명만 답변하세요 | 4 | 0 | 파라과이 | 남아메리카 |  | 파라과이 | 북중미와 카리브 지역 |
2. **평가 데이터의 System 지시사항 추가**
    - 펼치기
        
        **기존 평가 데이터 System 지시사항 제외 데이터셋**
        
        | **system** | **user** | **content** |
        | --- | --- | --- |
        |  | 아일랜드 |  |
        |  | 과들루프 |  |
        |  | 몰디브 |  |
        |  | 가나 |  |
        |  | 파라과이 |  |
        
        **변경된 System 지시사항 포함 데이터셋**
        
        | **system** | **user** | **content** |
        | --- | --- | --- |
        | - 사용자가 제공하는 데이터를 분류 기준에 맞춰 분류합니다.- 사용자가 제시한 분류 기준을 명확하게 따라야합니다.- 국가를 FIFA 대륙 연맹 기준으로 분류하세요.- FIFA 대륙 연맹은 남아메리카, 북중미와 카리브 지역, 아시아, 아프리카, 오세아니아, 유럽으로 나뉩니다.- 대륙명만 답변하세요 | 아일랜드 |  |
        | - 사용자가 제공하는 데이터를 분류 기준에 맞춰 분류합니다.- 사용자가 제시한 분류 기준을 명확하게 따라야합니다.- 국가를 FIFA 대륙 연맹 기준으로 분류하세요.- FIFA 대륙 연맹은 남아메리카, 북중미와 카리브 지역, 아시아, 아프리카, 오세아니아, 유럽으로 나뉩니다.- 대륙명만 답변하세요 | 과들루프 |  |
        | - 사용자가 제공하는 데이터를 분류 기준에 맞춰 분류합니다.- 사용자가 제시한 분류 기준을 명확하게 따라야합니다.- 국가를 FIFA 대륙 연맹 기준으로 분류하세요.- FIFA 대륙 연맹은 남아메리카, 북중미와 카리브 지역, 아시아, 아프리카, 오세아니아, 유럽으로 나뉩니다.- 대륙명만 답변하세요 | 몰디브 |  |
        | - 사용자가 제공하는 데이터를 분류 기준에 맞춰 분류합니다.- 사용자가 제시한 분류 기준을 명확하게 따라야합니다.- 국가를 FIFA 대륙 연맹 기준으로 분류하세요.- FIFA 대륙 연맹은 남아메리카, 북중미와 카리브 지역, 아시아, 아프리카, 오세아니아, 유럽으로 나뉩니다.- 대륙명만 답변하세요 | 가나 |  |
        | - 사용자가 제공하는 데이터를 분류 기준에 맞춰 분류합니다.- 사용자가 제시한 분류 기준을 명확하게 따라야합니다.- 국가를 FIFA 대륙 연맹 기준으로 분류하세요.- FIFA 대륙 연맹은 남아메리카, 북중미와 카리브 지역, 아시아, 아프리카, 오세아니아, 유럽으로 나뉩니다.- 대륙명만 답변하세요 | 파라과이 |  |
3. **분류 작업 카테고리 변경 (동물 종류)**
    - System_Prompt 지시사항 제외
        
        ![스크린샷 2024-07-12 오전 12.09.13.png](Search%20API%20&%20Chat-Completion%20API%20%E1%84%8E%E1%85%A1%E1%84%8B%E1%85%B5%20%E1%84%87%E1%85%B5%E1%84%80%E1%85%AD%200b7b689e945145fc834c58c2771b62b2/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-12_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB_12.09.13.png)
        
    - System_Prompt 지시사항 포함
        
        ![스크린샷 2024-07-12 오전 12.09.40.png](Search%20API%20&%20Chat-Completion%20API%20%E1%84%8E%E1%85%A1%E1%84%8B%E1%85%B5%20%E1%84%87%E1%85%B5%E1%84%80%E1%85%AD%200b7b689e945145fc834c58c2771b62b2/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-12_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB_12.09.40.png)
        

## **최종 결과**

**결과**

Search API의 문제점을 인식하고, 학습과 평가 데이터셋의 System 컬럼에 지시사항을 추가 후 테스트를 진행하였지만, 개선되지 않았습니다.

## **사용한 코드**

**taskid**

- 국가 분류 System_Prompt 제외 → hay8telv
- 국가 분류 System_Prompt 포함 → u9iejjyb
- 동물 분류 System_Prompt 제외 → n2b7dv9r
- 동물 분류 System_Prompt 포함 → rdtxb7ro

**URL**

**url**

https://api-hyperclova.navercorp.com/v2/tasks/{TASK_ID}/search

**국가 데이터 만들기 code** 원본 

- 펼치기
    
    ```python
    import pandas as pd
    import random
     
    # FIFA 회원국을 대륙별로 분류한 데이터 총 220개
    un_countries = {
        "아시아": [
            "괌", "네팔", "대한민국", "동티모르", "라오스", "레바논", "마카오", "말레이시아", "몰디브",
            "몽골", "미얀마", "바레인", "방글라데시", "베트남", "부탄", "북마리아나 제도", "브루나이", "사우디아라비아",
            "스리랑카", "시리아", "싱가포르", "아랍에미리트", "아프가니스탄", "예멘", "오만", "오스트레일리아",
            "요르단", "우즈베키스탄", "이라크", "이란", "인도", "인도네시아", "일본", "조선민주주의인민공화국",
            "중국", "중화 타이베이", "카타르", "캄보디아", "쿠웨이트", "키르기스스탄", "타지키스탄", "태국",
            "투르크메니스탄", "파키스탄", "팔레스타인", "필리핀", "홍콩"
        ],
        "아프리카": [
            "가나", "가봉", "감비아", "기니", "기니비사우", "나미비아", "나이지리아", "남수단", "남아프리카 공화국",
            "니제르", "라이베리아", "레소토", "레위니옹", "르완다", "리비아", "마다가스카르", "말라위", "말리",
            "모로코", "모리셔스", "모리타니", "모잠비크", "베냉", "보츠와나", "부룬디", "부르키나파소", "상투메 프린시페",
            "세네갈", "세이셸", "소말리아", "수단", "시에라리온", "알제리", "앙골라", "에리트레아", "에스와티니",
            "에티오피아", "우간다", "이집트", "잔지바르", "잠비아", "적도 기니", "중앙아프리카 공화국", "지부티",
            "짐바브웨", "차드", "카메룬", "카보베르데", "케냐", "코모로", "코트디부아르", "콩고 공화국", "콩고 민주 공화국",
            "탄자니아", "토고", "튀니지"
        ],
        "북중미와 카리브 지역": [
            "가이아나", "과들루프", "과테말라", "그레나다", "니카라과", "도미니카 공화국", "도미니카 연방", "마르티니크",
            "멕시코", "몬트세랫", "미국", "미국령 버진아일랜드", "바베이도스", "바하마", "버뮤다", "벨리즈", "보네르섬",
            "생마르탱", "세인트루시아", "세인트빈센트 그레나딘", "세인트키츠 네비스", "신트마르턴", "수리남", "아루바",
            "아이티", "앤티가 바부다", "앵귈라", "엘살바도르", "영국령 버진아일랜드", "온두라스", "자메이카", "캐나다",
            "케이맨 제도", "코스타리카", "쿠바", "퀴라소", "터크스 케이커스 제도", "트리니다드 토바고", "파나마",
            "푸에르토리코", "프랑스령 기아나"
        ],
        "남아메리카": [
            "베네수엘라", "볼리비아", "브라질", "아르헨티나", "에콰도르", "우루과이", "콜롬비아", "칠레", "파라과이",
            "페루"
        ],
        "오세아니아": [
            "누벨칼레도니", "뉴질랜드", "바누아투", "사모아", "솔로몬 제도", "아메리칸사모아", "쿡 제도", "타히티",
            "통가", "파푸아뉴기니", "피지"
        ],
        "유럽": [
            "그리스", "네덜란드", "노르웨이", "덴마크", "독일", "라트비아", "러시아", "루마니아", "룩셈부르크", "리투아니아",
            "리히텐슈타인", "몬테네그로", "몰도바", "몰타", "벨기에", "벨라루스", "보스니아 헤르체고비나", "북마케도니아",
            "북아일랜드", "불가리아", "산마리노", "세르비아", "스웨덴", "스위스", "스코틀랜드", "스페인", "슬로바키아",
            "슬로베니아", "아르메니아", "아이슬란드", "아일랜드", "아제르바이잔", "안도라", "알바니아", "에스토니아",
            "오스트리아", "우크라이나", "웨일스", "이스라엘", "이탈리아", "잉글랜드", "조지아", "지브롤터", "체코",
            "카자흐스탄", "코소보", "크로아티아", "키프로스", "튀르키예", "페로 제도", "포르투갈", "폴란드", "프랑스",
            "핀란드", "헝가리"
        ]
    }
     
    # 모든 나라를 담은 리스트
    countries = []
    for continent, country_list in un_countries.items():
        countries.extend([(country, continent) for country in country_list])
     
    # 220개의 데이터셋 생성
    data = []
    while len(data) < 220:
        data.extend(countries)
        if len(data) > 220:
            data = data[:220]
     
    # 데이터셋을 랜덤하게 섞기
    random.shuffle(data)
     
    # 데이터프레임 생성
    df = pd.DataFrame(data, columns=["Text", "Completion"])
     
    # 열 추가
    df["System_Prompt"] = """
    - 제공된 데이터를 지정된 기준에 따라 정확하게 분류하세요.
    - 사용자가 제공한 분류 기준을 철저히 준수해야 합니다.
    - 국가를 FIFA 대륙 연맹 기준으로 분류하세요.
    - FIFA 대륙 연맹은 남아메리카, 북중미와 카리브 지역, 아시아, 아프리카, 오세아니아, 유럽으로 나뉩니다.
    - 대륙명만 답변하세요.
    """.strip()
     
    df["C_ID"] = range(len(df))
    df["T_ID"] = 0
     
    # 열 순서 변경
    df = df[["System_Prompt", "C_ID", "T_ID", "Text", "Completion"]]
     
     
    # CSV 파일로 저장
    df.to_csv("<파일 경로>", index=False, encoding="utf-8-sig")
    ```
    

**Search API 인퍼런스 테스트 코드** 원본

- 펼치기
    
    ```python
    import requests
    import pandas as pd
    import time
     
    CLIENT_KEY = '<client_key>'
    CLIENT_SECRET = '<client_secret>'
    TASK_ID = '<taskId>'  # 튜닝한 작업의 taskId를 입력하세요
    INPUT_FILENAME = "<평가 데이터셋 파일 경로 xlsx파일>" #.xlsx 파일.
    OUTPUT_FILENAME = "<결과 데이터셋 파일 저장 경로>" #.xlsx 파일
     
    def __send_requests(system: str, user: str, token: str):
        url = f"https://api-hyperclova.navercorp.com/v2/tasks/{TASK_ID}/search"
        headers = {
            'Authorization': f"Bearer {token}",
            'Content-Type': 'application/json'
        }
        body = {
            "text": f"{system} {user}"  # system과 user를 함께 사용 테스트에 따라 system 삭제
        }
        response = requests.post(url=url, json=body, headers=headers)
         
        if response.status_code != 200:
            print(f"Request failed: {response.status_code} {response.text}")
            return {"role": "error", "content": None}
         
        result = response.json()
        if "result" not in result or "outputText" not in result["result"]:
            print(f"Unexpected response format: {result}")
            return {"role": "error", "content": None}
         
        print(result["result"])
        return {"role": "system", "content": result["result"]["outputText"]}
     
     
    def __get_auth():
        url = "https://api-hyperclova.navercorp.com/v1/auth/token"
        response = requests.get(url=url, auth=(CLIENT_KEY, CLIENT_SECRET))
         
        if response.status_code != 200:
            raise Exception(f"Auth request failed: {response.status_code} {response.text}")
         
        result = response.json()
        return (result["result"]["accessToken"], result["result"]["expiresIn"])
     
     
    if __name__ == '__main__':
        token, tokenExpiresIn = __get_auth()
     
        df = pd.read_excel(INPUT_FILENAME)
        results_role = []
        results_content = []
     
        for i in df.itertuples():
            currentTimestamp = int(time.time() * 1000)
            if tokenExpiresIn < currentTimestamp + 60000:
                token, tokenExpiresIn = __get_auth()
            result = __send_requests(i.system, i.user, token)  # 'system'과 'user' 열을 사용
            results_role.append(result['role'])
            results_content.append(result['content'])
         
        df.insert(2, "role", results_role, True)
        df.insert(3, "content", results_content, True)
     
        df.to_excel(OUTPUT_FILENAME, index=False)
     
    print(f"Results saved to {OUTPUT_FILENAME}")
    ```
    

**Search API 단일 실행 코드** 원본 펼치기

- 펼치기
    
    ```python
    # -*- coding: utf-8 -*-
     
    import base64
    import requests
    from http import HTTPStatus
     
    class TaskSearchExecutor:
        def __init__(self, host, client_id, client_secret, access_token=None):
            self._host = host
            self._client_id = client_id
            self._client_secret = client_secret
            self._encoded_secret = base64.b64encode('{}:{}'.format(self._client_id, self._client_secret).encode('utf-8')).decode('utf-8')
            self._access_token = access_token
     
        def _refresh_access_token(self):
            headers = {
                'Authorization': 'Basic {}'.format(self._encoded_secret)
            }
     
            response = requests.get(self._host + '/v1/auth/token?existingToken=true', headers=headers)
             
            if response.status_code != HTTPStatus.OK:
                print("Failed to refresh access token:", response.status_code, response.text)
                response.raise_for_status()
     
            token_info = response.json()
            self._access_token = token_info.get('result', {}).get('accessToken')
            if not self._access_token:
                raise ValueError("Access token is not found in the response")
     
        def execute(self, task_id, text):
            if self._access_token is None:
                self._refresh_access_token()
     
            headers = {
                'Content-Type': 'application/json; charset=utf-8',
                'Authorization': 'Bearer {}'.format(self._access_token)
            }
     
            request_data = {
                'text': text
            }
     
            response = requests.post(self._host + f'/v2/tasks/{task_id}/search', headers=headers, json=request_data)
     
            if response.status_code == HTTPStatus.UNAUTHORIZED:
                # Token might be expired, refresh and retry
                self._access_token = None
                return self.execute(task_id, text)
            elif response.status_code == HTTPStatus.OK:
                return response.json()
            else:
                print(f"Error: {response.status_code} - {response.text}")
                return 'Error'
     
    if __name__ == '__main__':
        task_search_executor = TaskSearchExecutor(
            host='https://api-hyperclova.navercorp.com',
            client_id='<client_key>',
            client_secret='<client_secret>'
        )
     
        task_id = 'hay8telv' #taskID 예제는 시스템 프롬프트 삭제한 국가 분류 taskid
        text = '대한민국'
     
        response = task_search_executor.execute(task_id, text)
        print(response)
    ```