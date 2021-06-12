---
title:  "어서 와, 인프라는 처음이지?"
excerpt: "인프라 공방 1주차 첫 번째 미션 후기"

categories:
  - "Infra Workshop"
tags:
  - Blog
  - 인프라 공방
last_modified_at: 2021-06-12T21:00:00
--- 
<br>
현재 백엔드 개발자로 일하고 있으면서 이런 말 하긴 좀 창피하긴 한데...    
솔직히 애플리케이션 밖에서 무슨 일이 일어나는지에 대해서는 잘 모릅니다. 🤨  
그래서 이런 고민들을 자주 해봤는데요.  

- 왜 리눅스 명령어들은 하나같이 외계어 같은지... 
- 애플리케이션 성능이 안 나올 때 어디를 봐야 하는지...  
- 잘나가는 IT 회사들은 어떻게 서비스를 운영하는지...  

이런 고민들을 해결하기 위해서 눈여겨보던 교육 과정이 있었습니다.  
바로 넥스트스텝에서 운영하는 '[인프라 공방](https://edu.nextstep.camp/c/VI4PhjPA)' 인데요.  
이번에 강의 개설 알림이 오자마자 바로 결제해서 모집 정원에 들어갈 수 있었습니다.  
'자바지기' 박재성 대표님의 말씀으로는 **'9분'**만에 마감되었다고 하네요. 😱  
  
이번 포스트에서는 1주차 1단계 미션을 진행하면서 얻은 경험들을 적어보려고 합니다.  
<small>원래 주 단위로 정리해보려고 했는데, 아무래도 내용이 길어지면 글의 퀄리티가 떨어질 것 같아서...~~는 개뿔~~🤦‍♂️</small>

<b>
다만 아무래도 인프라에 대한 지식이 터무니없이 부족하다보니,  
여기서는 실제로 미션을 진행하며 겪었던 좌충우돌 실습 후기를 위주로 서술하며  
네트워크 개념이나 원리 등에 대한 전문적인 내용은 최대한 다루지 않음을 말씀 드립니다.  
또한 각각의 실습 과정(VPC, 서브넷, EC2 인스턴스 생성 등)은 따로 정리할 예정입니다.  
</b>

<b>
이번 교육 과정에서 인프라에 대한 모든 걸 마스터한다기보다는,  
인프라가 무엇이고 현업에서 실제로 어떻게 운영되는지를 대략적으로 경험해보고  
앞으로 인프라에 대해 스스로 학습할 수 있는 기반을 만드는 과정으로 보시면 될 것 같습니다.    
</b>

작성자의 삽질이 궁금하시다면 마우스 가운데 스크롤을 내려주시고, 😃  
전문적인 내용을 기대하셨다면 손가락 가운데를...~~🖕 아니 이건 왜 있는거야 🤷‍♂️~~  
아니, 구글神을 찾아주세요. 🔍 

**'인프라'**라는 단어를 들으면 가장 먼저 무슨 생각이 드시나요?  
저는 가장 먼저 '인프라'를 누구보다 사랑하시는 대표적인 인물이 생각나는데요.

<center>
  <img src="/assets/image/heo-kooyoun.jpg" width="70%" height="70%"/>
  <p>
    <br>
    <b>여윾씌 빽엔두 개발짜다...<br>
    지굼 보시믄 아시게찌마는, 헨재 우녕 중인 애프리케숀에스<br> 
    에라가 난 위치를 증화키 찌버내는 메카니즈믄 솽당히 조크든요?<br>
    즈응말 모니트링 자래요.<br>
    제가 그듭 말씀드리지마는, 즈른 복짜판 로지게스<br> 
    침차카게 에라 지저믈 차즐 수 잉는 개발짜가 그릏게 만치 안타...<br>
    그릏기 때무네 인뿌라가 중요흔 그에요.</b>
  </p>
</center>
<br>

~~아니 야구 말고~~ IT 인프라란, **애플리케이션을 운영하기 위해 필요한 하드웨어나 OS, 미들웨어, 네트워크 등 
IT 서비스의 기반이 되는 시스템 및 구조**를 말하는데요.  
아무래도 이렇게 말하면 너무 광범위한 설명이라 솔직히 구체적으로 확 와닿지는 않죠.  

인프라 공방의 1주차 미션은 **'그럴듯한 인프라'**를 실제로 만들어보는 과정이었고,  
그 중 1단계 미션은 바로 **'통신망을 분리'**해보는 것이었습니다.  
<br>

## 통신망 분리하기 
<br>
통신망을 왜 분리해야 할까요?  
회사에서 운영 중인 서비스의 핵심 자원들에 아무나 허가 없이 접근할 수 있으면 안 되겠죠?  
서비스를 이용하는 소중한 고객 분들의 개인정보나 기업의 핵심 기술이 유출되는 사건사고는  
다들 뉴스를 통해 많이 접해보셨으리라 생각해요.  


얼마 전에는 **[금융감독원에서 망 분리 규제를 위반한 핀테크 기업들에 과태료를 부과](http://it.chosun.com/site/data/html_dir/2021/05/20/2021052001955.html)**했다죠.  
그만큼 망 분리는 최근에, 특히 핀테크 업계에서는 핫한 이슈인데요.  
이 망 분리를 AWS의 **[VPC(Virtual Private Cloud)](https://docs.aws.amazon.com/ko_kr/vpc/latest/userguide/what-is-amazon-vpc.html)**를 통해 실습해봤습니다.  

우선 미션에서 구성한 통신망의 전체적인 구조를 볼까요?

<center>
  <img src="/assets/image/aws_vpc.png" width="80%" height="80%"/>
  <p>
    <br>
    <b>이게 다 뭔 소리래요? 🤯</b>
  </p>
</center>
<br>

우선 각 용어들에 대해 간략히 설명하자면 다음과 같습니다.
  * **Region** : 리전. AWS가 전 세계에서 데이터 센터를 클러스터링하는 물리적 위치  
  * **Availability Zone** : 가용 영역. 각 리전 안에 존재하는 물리적으로 나뉜 데이터 센터  
  * **VPC** : 하나의 서비스를 운영하기 위해 사용자가 정의한 가상의 네트워크
  * **Subnet** : IP를 효율적으로 관리하기 위해 하나의 네트워크를 부분적으로 쪼갠 단위
  * **Internet Gateway** : VPC와 외부 인터넷 간에 통신을 할 수 있게 해주는 역할
  * **Router** : 서로 다른 네트워크 간의 통신을 중계해주는 역할
  
인프라에 대해서 잘 아시거나 기존에 AWS를 사용해보신 분들이라면  
이 용어들이 익숙하시거나 저보다 훨씬 더 잘 이해하고 계실거라 생각합니다.  
그럼 이걸 가지고 어떻게 통신망을 나눌 것이냐?  
이번 미션의 요구사항은 크게 다음과 같았는데요.  

  * AWS 리전에 웹 서비스를 운영하기 위한 하나의 VPC를 생성한다.  
  * VPC 네트워크 안에서 서브넷을 **외부망**, **내부망**, 그리고 **관리망**의 용도로 생성한다.  
    * 단, 가용 영역을 나누어서 구성한다. (이유는 후술)
  * AWS EC2로 서버를 생성한다. 각각의 용도는 다음과 같다.  
    * 외부망 : 웹 서비스가 실제로 배포 및 실행될 서버
    * 내부망 : 데이터베이스 서버
    * 관리망 : 배스천 서버
  * 각 통신망에 접근할 수 있는 권한은 **보안 그룹(Security Group)**을 통해 설정한다.  
