---
title:  "git을 활용하여 중요한 설정 파일을 관리해보기"
excerpt: "git 서브모듈 사용기"

categories:
  - "BlackCoffee"
tags:
  - Blog
--- 

## 0. 들어가며
몇 주 전부터 진행하던 사이드 프로젝트가 아직 끝나질 않았는데요. 😥  
~~(TDD & Clean Code 과정 미션 진행하느라...는 핑계)~~  
다른 서비스 업체의 API를 하나 둘씩 연동하다보니 한 가지 고민거리가 생겼습니다.  
바로 **'API 키'**나 **'DB 계정 정보'** 등의 중요한 설정들을 어떻게 관리할 것이냐는 거였죠.  

현재 소스 코드는 github 저장소에 있는데, public으로 열려있는 상태입니다.  
👺 **'그럼 private으로 바꾸면 되잖아?'** 라고 하실 수 있는데,  
나중에 다른 분들과 같이 코드를 보며 프로젝트를 더 발전시킬 수 있겠다란 생각이 들어서  
public 저장소에서도 중요한 정보를 숨길 수 있는 방법이 없을까 찾아보게 되었고,  
저번에 수강했던 '인프라 공방' 교육과정에서 그 답을 얻을 수 있었습니다.  
바로 git의 **서브모듈(submodule)**을 이용한 방법인데요, 그 소소한 적용기를 적어볼까 합니다.  

## 1. 변경 전
현재 진행 중인 프로젝트의 백엔드는 스프링 부트로 만들어져 있는데요.  
기존에 작성하여 사용 중인 설정 파일(```application.yml```)은 다음과 같았습니다.  
(물론 실제 API 설정들은 아니고 예시를 위해 살짝 변경해봤습니다.)

```
spring:
  datasource:  # 연동할 데이터베이스 접근 정보
    url: jdbc:mariadb://'운영DB'.ap-northeast-2.rds.amazonaws.com/'
    username: admin
    password: 1234
    driver-class-name: org.mariadb.jdbc.Driver   

service1:
  token: '토큰'  # 서비스1의 API 연동에 필요한 토큰
  api:
    base-url: 'https://service1.co.kr'
    insure-money-uri: ${service1.api.base-url}/scrap/common/zoom/InsureMoney
    captcha-uri: ${service1.api.base-url}/scrap/captcha

service2:
  bs-info:  # 서비스2의 API 연동에 필요한 정보
    bsid: '접근 아이디'
    passwd: '접근 비밀번호'
    send-profile-key: '프로필 키'
  api:
    base-url: 'https://service2.co.kr'
    user-token-uri: ${service2.api.base-url}/v2/auth/getToken
    send-message-uri: ${service2.api.base-uri}/v2/kko/sendMessage
```

위의 API 정보들은 API 서비스 업체와의 계약을 통해서 얻은 회사의 중요한 자산이죠.  
함부로 노출됐다간 API 호출 건당 부과되는 요금 폭탄을 맞을 위험성이 있습니다.  
DB 접근 정보도 노출됐다간 회사 내부의 고객 정보가 쏙쏙 털리는 개인정보 맛집이 되겠죠?  

그럼 이 설정 정보들을 숨기기 위해 git의 서브모듈을 활용해보겠습니다.  
서브모듈에 대해 자세한 내용을 기대하고 오신 분들께는 정말 죄송합니다만,  
중요한 설정 정보를 이런 식으로도 관리할 수 있다 정도로만 참고해주셨으면 좋겠습니다.  

## 2. 변경 시작
예제용 스프링 프로젝트를 하나 생성하여 저장소에 올려놓습니다.    
현재 설정 파일(```application.properties```)에는 아무 설정도 없는 상태입니다.  

<center>
  <img src="/assets/image/submodule/properties_nothing.png" width="50%" height="50%"/>
</center>
<br>


**(1) 이제 GitHub에서 설정 파일을 관리할 private 저장소를 하나 생성합니다.**  

<center>
  <img src="/assets/image/submodule/create_private_repository.png" width="100%" height="100%"/>
</center>
<br>

**(2) 실제 프로젝트에서 관리할 설정 파일을 작성하여 저장소에 올립니다.**  

```
# private 저장소에 올라갈 application.properties
server.port=9090  # 내장 톰캣 서버 포트를 기본 포트 8080이 아니라 9090으로 변경
```

<center>
  <img src="/assets/image/submodule/private_repository_properties.png" width="50%" height="50%"/>
  <p>
    <br>
    <b>private 저장소에 설정 파일을 업로드합니다</b>
  </p>
</center>
<br>

**(3) git의 서브모듈을 활용하여 특정 경로에 private 저장소를 참조하도록 설정합니다.**  
```
# '지정할 경로' 하위에서 private 저장소의 파일들을 참조    
$ git submodule add [private 저장소] '지정할 경로'
```

저는 ```./src/main/resources/config``` 경로에 private 저장소에 있는 설정파일을 가져왔습니다.  

<center>
  <img src="/assets/image/submodule/git_add_submodule.png" width="100%" height="100%"/>
  <p>
    <br>
    <b>짜잔! 지정한 경로에 private 저장소의 설정파일이 생겼습니다!</b>
  </p>
</center>
<br>

**(4) 실제 프로젝트 설정 파일에서 private 저장소의 설정 파일을 참조하도록 설정합니다.**
```
# 'config/' 하위에 있는 설정을 참조하도록 설정
spring.config.additional-location=config/
```

<center>
  <img src="/assets/image/submodule/link_properties.png" width="100%" height="100%"/>
</center>
<br>

## 3. 변경 후
자 이제 실제로 public 저장소의 Spring 프로젝트가 private 저장소의 설정 파일을 참조하는지 
테스트 코드를 작성하여 확인해보겠습니다.  

<center>
  <img src="/assets/image/submodule/test_result.png" width="70%" height="70%"/>
  <p>
    <br>
    <b>서버 포트 번호가 8080이 아닌 9090 포트로 실행됨을 확인했습니다!</b>
  </p>
</center>
<br>

지금까지의 내용을 원격 저장소에 올린 다음, GitHub에서 로그아웃 후 커밋 내역을 살펴보면, 
<center>
  <img src="/assets/image/submodule/git_commits.png" width="100%" height="100%"/>
</center>
<br>

여기서 ```config/``` 하위 내용을 보려고 클릭하면,
<center>
  <img src="/assets/image/submodule/github_404.png" width="100%" height="100%"/>
</center>
<br>

private 저장소에 대한 접근 권한이 없기 때문에 404 에러 페이지로 이동하게 됩니다.  
이제 public 저장소에서도 마음 편히 설정 정보를 추가할 수 있겠네요.  

**그럼 private 저장소의 설정 파일 내용이 변경되면 어떻게 해야 할까요?**  
다음 명령어를 통해 private 저장소에 변경 내역을 업데이트할 수 있습니다.  

```
# 변경된 파일들을 추가
$ git submodule foreach git add .

# 변경 내역을 commit 
$ git submodule foreach git commit -m "commit message"

# commit 내역을 push
$ git submodule foreach git push origin master
```
<br>

단, 설정 이후 실제 프로젝트 clone 시 서브모듈까지 clone 해야하니 주의해주세요.
```
$ git clone --recurse-submodules [프로젝트 저장소]
```

<br>

## 4. 마치며
이 글은 사실 **[git의 서브모듈](https://git-scm.com/book/ko/v2/Git-%EB%8F%84%EA%B5%AC-%EC%84%9C%EB%B8%8C%EB%AA%A8%EB%93%88)**에 대해 자세히 이해하고 작성한 글은 아닙니다.  
(자세한 내용은 위의 링크를 참조해주세요😢)

다만 public 저장소에서 설정 파일을 따로 관리할 수 있는 방법이 필요한 상황이었고,  
마침 전에 수강했던 '인프라 공방' 교육과정에서 해당 내용을 다루었던 기억이 갑자기 나서  
헐레벌떡 적용해보고 잊어버리기 전에 기억하기 위해 작성해보았습니다.  

물론 문제를 해결해서 기쁘기도 했지만, 이렇게 글로 작성해봄으로써   
한 번 해보고 잊어버릴 수 있는 내용을 장기기억화 할 수 있어서 더 기쁜 것 같습니다.  

혹시 더 좋은 방법에 대해서 알고 계시다면 지식을 공유해주세요!  
