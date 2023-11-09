<<<<<<< HEAD
# 건우네반찬
## 식자재 객체인식으로 레시피 추천 서비스
멀티캠퍼스 KDT 데이터사이언스/엔지니어링 과정 최종 프로젝트

오늘뭐먹조: 김건우,계은서,고기원,백지헌,이은평,이지원,정현식
=======
# <div align="center">Project : 건우네반찬</div>
### <div align="center">도마 위의 식재료를 객체 인식 후, <br>추출된 식재료 리스트를 기반으로 적절한 레시피를 추천 하는 서비스</div>

### <div align="right">Team Name <br> 오늘뭐먹조</div>
#### <div align="right">Team Member <br> 고기원(DS), 백지헌(DS) 이은평(DS), 이지원(DS) <br> 김건우(DE/조장), 계은서(DE), 정현식(DE)</div>
___

### <div align="center">발표자료 및 자세한 내용은 아래 프레젠테이션 PDF 참고
<div align="center">https://github.com/rjs6418/KDT_FinalProject_1/blob/master/KDT-MC-FinalProject-1%EC%A1%B0.pdf</div>
<br>
<div align="center">서비스 시연 영상</div>
<div align="center">https://kuno-techblog.tistory.com/4</div>

<br><br>
## <div align="center">프로젝트 기획 배경 및 목표</div>
> <div align="center">코로나19 이후 비대면 및 재택근무 추이가 증가하기도 하면서 집에 머무는 시간이 크게 증가하였고,</div> 
> <div align="center">여러가지 사회 이슈로 인한 소비자 물가 상승과 외식물가가 크게 올랐다.</div> 
> <br>
> <div align="center">동시에,</div>
> <div align="center">사람들의 요리 레시피 컨텐츠에 대한 관심도 증가와</div>
> <div align="center">'무소비', '무지출', '냉장고파먹기'라는 단어의 언급 빈도가 증가하면서</div>
> <div align="center">이와 같은 서비스가 소비자들에게 필요로 할 것이라 판단되었다.</div>
> <div align="center">현재, 비슷한 서비스 중 가장 높은 점유율을 차지하고 있는 '만개의 레시피'에서 </div>
> <div align="center">부족하거나 더 필요로 하는 기능을 기획하였다.</div>
<br><br>


### 서비스 중심의 전체 파이프라인 구성도
![image](https://user-images.githubusercontent.com/101792115/190885374-eec94646-0a68-4753-82b0-8f6c1da2df11.png)
> 프로젝트를 진행하면서 중심이 되었던 구조도이다.
>
> 구성도 중 중심에서 가장 길게 뻗어있는 파이프가 메인서비스의 데이터 파이프라인인데,<br>
> 사용자로 부터 받은 식재료 사진을 받아 객체인식을 한 후, 인식된 식재료 리스트에 필요한 식재료를 추가하여 다시 전송,<br>
> 조리가능한 레시피를 추천 순으로 정렬하고, 해당 레시피 정보를 응답한다.<br>
> (이때, 응답하는 레시피는 완전히 요리가 가능한 레시피와, 식재료가 1~2개 부족한 레시피를 나누어 추천한다.)
>
> 가장 하단의 라인은 부가적인 서비스로, 부족한 식재료를 유저간 교환할 수 있게끔 매칭기능을 구현 하였다.  
> 
> 메인 파이프라인 위쪽에 해당하는 영역은 서비스 이용에 필요하거나 서비스 이용 시 발생하는 데이터를 관리하는 파이프라인이다.
<br><br>

## 메인서비스
> 메인서버의 전체 흐름은 카프카로 흐르게 구현하였다.

### 객체인식 모델

![image](https://user-images.githubusercontent.com/101792115/191442571-aad74ff0-124d-4802-bea5-e303617ec471.png)

![image](https://user-images.githubusercontent.com/101792115/191442639-fc916833-777c-49b1-b526-ca4b27712381.png)
<br>
> 객체인식 모델은 Yolo v5 모델을 사용했다.<br> 
> 현재, 객체인식 모델 중 가장 뛰어난 성능을 보이며,<br>
> 비교군이었던 EfficientDet보다도 높은 성능을 보이는 것으로 확인되었다.
<br><br>

### 데이터 수집 및 전처리
![image](https://user-images.githubusercontent.com/101792115/191443688-11fcc908-7260-40d9-b435-e12a7de01c24.png)
> 캐글에서 다운로드, 구글의 검색 이미지 크롤링으로 데이터를 수집 한 후,

![image](https://user-images.githubusercontent.com/101792115/191444097-07c46a04-538a-4f53-ab10-48c0f5ebe9da.png)<br>
> 모델 학습을 위한 라벨링 작업을 실시하였다.(수작업)
>
> **이미지 하나당 하나의 객체만이 들어가 있는 단일 객체 이미지의 경우, 자동으로 라벨링을 해주는 코드를 작성하여 사용하였다.**

![image](https://user-images.githubusercontent.com/101792115/191446167-f5e5c582-6a30-4bec-8b80-e49d0b1b741c.png)
> 추가적으로 이미지 Augmentation을 적용하여, 데이터의 양을 늘리고 정확도를 향상시켰다.(이미지 회전, 반전, 노이즈 등)
<br><br>

### 레시피 DB 관리
![image](https://user-images.githubusercontent.com/101792115/191446687-d5960956-42e8-4a27-a4b2-b9f4b74c4115.png)
> 데이터는 가장 많은 레시피를 가지고 있는 '만개의 레시피'에서 크롤링 하였으며, 레시피 데이터 저장 및 검색을 위한 DB로는 elasticsearch를 사용하였다.<br> 
> MongoDB나, MySQL로도 검색기능을 구현 할 수 있으나, 필터링 해야할 검색조건이 많아 해당 SQL을 활용할 경우<br>
> 다중으로 검색을 해야하거나, content 내에 키워드가 포함된 여부를 찾는 검색 방식은 성능 발휘에 적합하지 않다고 판단하였다.<br>
>
> ElasticSearch의 경우 **역색인**의 개념으로, 포함된 키워드 중심으로 데이터를 역으로 정렬하고 있어 빠른 검색이 가능하며,
> 포함된 키워드의 복잡한 검색 방식을 간단한 코드로도 구현할 수 있어 채택하였다.
>
> 다만, Score를 활용한 검색을 방식을 적용하고 싶었으나, 현 상황에선 머신러닝의 추천이 더욱 적합하다 판단하여, 이번 프로젝트에서는 적용하지 않았다.

### 레시피 추천

#### 레시피 추천을 위한 사용자 데이터
> 사용자 기반 협업필터링 모델을 활용하였다.<br>
> 협업필터링 모델은 사용자의 데이터가 필요하다.<br>
> 그에 필요한 데이터론, 사용자가 레시피 상세정보 보기를 클릭한 정보를 데이터를 활용하기로 했다.


#### 레시피 추천 모델
![image](https://user-images.githubusercontent.com/101792115/191453058-512e1db1-a0be-4e1a-9129-baf70b5c4448.png)
> 검색된 레시피 중 사용자에게 추천할 모델로는 Spark의 ALS 모델을 채택하였다.(사용자 기반 협업필터링 추천 모델)<br>
> 다른 협업필터링 추천 모델과 ALS의 큰 차이점 중 하나는 선호도행렬을 나타내는 함수에 있다.<br>
> 레시피 상세정보를 보았다라는 것을 관심이 있다는 가정 하에 알고리즘을 적용 시킨다. <br>
> 다만 ALS 모델은 한 번만을 클릭한 레시피에 대해서는 긍정을 적용시키지 않을 수 있고, 옵션에 따라 부정 기능을 넣을 수도 있다.<br>
> 앞서 말한 상황을 고려하여 ALS 모델을 채택하였다.
>
> 최종 추천은 검색 후 현 식재료로 요리 가능한 레시피를 검색 후, 추천 모델에서 나오는 순서대로 재정렬하여 추천한다. 

## 부족식재료 교환 서비스

### 부족식재료에 대한 전반적인 서비스
![image](https://user-images.githubusercontent.com/101792115/191454890-38115974-ac3e-4710-87c3-514fb4bfa1e4.png)
> 하단, '무', '된장'을 클릭 할 시, 해당 상품을 구매할 수 있는 링크로 넘어가게 된다.
> 맨아래, 식재료 교환요청 버튼을 클릭하면, 자신이 교환가능한 식재료와 필요한 식재료 데이터를 전송하고 그것과 매칭이 되는 유저를 찾는다.
> 여기선 KSQL을 활용하였으나, 이또한 MySQL로도 구현은 가능 할 것이다. 
> 다만, 현재 이 서비스를 이용하기 원하는 사람들 간의 실시간 매칭이며, 데이터가 넘어 올 때마다 새롭게 명령을 내려야하기 때문에 속도면에서 큰 차이가 발생하게 된다.
> 또한 타임윈도우 개념을 적용해야하는데 KSQL의 경우 간단한 쿼리로 손쉽게 적용이 가능하고, 두 사용자간의 양방향 응답이 훨씬 용이해진다.
>
> 해당 구현의 자세한 방식은 다소 복잡하여 위의 pdf를 참고 해주시기 바랍니다.
<br>



### 서비스 이용 시, 수집할 수 있는 데이터를 활용한 분석 및 시각화
![image](https://user-images.githubusercontent.com/101792115/191456991-c7ce2590-2c1a-423a-b60c-671115a0817d.png)
> 사용자가 서비스 이용 시, 요청한 식재료 데이터와 레시피 상세보기 데이터를 수집할 수 있게 해놓았다.
> 해당 데이터는 향후, 사용자들의 파악과 추가적인 개선 등에 용이하게 사용될 수 있다 판단되어 구현하였다.
> 데이터는 임의로 생성하여 필요한 데이터가 수집되었다는 가정하에 구축하였다.
> Tableau를 사용하였다. 마케팅 데이터 분석 및 시각화에서 현재 많은 기업에서 다양하게 활용 되고 있는 플랫폼으로 
> 간단한 동작으로 높은 수준의 시각화가 가능하기에 채택하였다.

## 향후 추가 개선계획

> 웹구현-Flask의 경우 다소 짜임새 없이 작성되었음 -> 추가 공부 후 개선 

> 설계상 카프카에서 몇가지 문제가 확인 되었다 HTTP 프로토콜의 방식이 request, response 방식인 걸 알지 못했다.
> 사용자가 요청한 데이터를 카프카에 흐르게 한 후 그 데이터를 다시 받아와 사용자에게 전송하는 방식은 사용자의 인터넷상황에 따라 reponse가 막힐 경우,
> 데이터 밀리는 현상이 발견되었다. -> 카프카를 들어내고 사용자 데이터를 적재하는 용도로만 남겨놔야 할 듯 하다. 

> 매칭기능의 경우 매칭까지의 시간이 걸릴 수 밖에 없다. 
> 그 동안의 다른 행동이 되지 않는다는건 서비스에 다소 무리가 있으며, 비동기처리 및 AJAX를 활용한 데이터 처리로 각 영역을 처리할 필요가 있다.
> 또한 매칭 후 알맞게 각 유저에게 전송해야한다면, 데이터를 받을 때, 받은 곳의 정보를 가지고 다시 돌려 줄 필요가 있다. 
> 이에 메시징 기능을 구현하여, 해당 아이디에 전송하는 방식으로 구현 할 필요가 있다. 해당 부분은 구현하지 못하였고 단순히 클라이언트에 명령어까지만 뜨게 구현하였다.

> 기술 및 시간 상의 문제로 원하는 방식과 수준의 추천시스템을 구현하지 못하였다.
> 검색과 추천을 필터링 하는 방식은 정말 큰 대용량의 상황에선 최적합한 방식이라고 생각되진 않는다.
> 산출값이 희미해질 수 있으며, 속도 저하가 우려된다.
> 이에, 협업필터링 방식을 그래프 알고리즘으로 해결할 수있는 방식을 모색하였다.
> 곧 원리를 설명하는 내용을 블로그에 기획안 형식으로 게재해 보겠다.
>>>>>>> 4c3588b3c3fc0ab08d5ccb5ab9435bf271773055
