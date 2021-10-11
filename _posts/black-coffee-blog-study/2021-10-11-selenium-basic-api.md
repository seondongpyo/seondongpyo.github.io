---
title:  "[Selenium] Selenium 시작하기"
excerpt: "웹 테스트 자동화 프레임워크인 Selenium 시작하기"

categories:
  - Selenium
tags:
  - Selenium
last_modified_at: 2021-10-11
--- 

## 0. 들어가며
지난 포스팅에서는 간략하게 Selenium이 무엇인지에 대해 정리해보았는데요.  
이번 포스팅에서는 프로젝트를 하나 생성하여 간단한 예제를 진행해보면서  
Selenium의 기본적인 API 사용법을 정리해보겠습니다.  

## 1. Selenium 프로젝트 생성
프로젝트를 생성하여 실습을 진행해볼텐데요.  
여기서는 통합 개발 환경(IDE)은 IntelliJ IDEA Ultimate 버전을 사용하겠습니다.  

IntellJ IDEA에서 [New Project]를 선택하고 Maven 프로젝트를 생성합니다.  
Java 버전은 1.8 이상으로 선택하시면 되겠습니다.  

<center>
  <img src="/assets/image/new_project.jpg" width="70%" height="70%"/>
</center>
<br>

프로젝트 이름, GroupId, ArtifactId 등은 편하신대로 지어주시면 됩니다.  
여기서는 **'selenium-tutorial'**로 하겠습니다.  

<center>
  <img src="/assets/image/artifact.jpg" width="70%" height="70%"/>
</center>
<br>

Selenium을 사용하기 위해서 pom.xml에 dependency를 추가해줍니다.  
현재 가장 안정적인 버전은 **3.141.59**입니다.  

```
<dependency>
    <groupId>org.seleniumhq.selenium</groupId>
    <artifactId>selenium-java</artifactId>
    <version>3.141.59</version>
</dependency>
```

여기서 추가해줘야 할 의존성이 하나 더 있는데, 바로 **'WebDriverManager'**입니다.  
Selenium 시작 전 WebDriver를 세팅하는 번거로운 과정을 하나 거쳐야 하는데  
여기서는 해당 라이브러리가 이 역할을 대신 해준다고 보시면 됩니다.  
WebDriverManager에 대해서는 추후에 따로 내용을 정리해보도록 하겠습니다.  
아래 코드를 pom.xml에 작성하여 의존성을 추가해줍시다.  

```
<dependency>
    <groupId>io.github.bonigarcia</groupId>
    <artifactId>webdrivermanager</artifactId>
    <version>5.0.3</version>
</dependency>
```

## 2. Selenium으로 수행해볼 예제는?  
이제 Selenium을 사용하기 위한 준비를 마쳤습니다.  
Selenium을 통한 테스트는 대부분 다음과 같은 과정을 거쳐 진행하는데요.  
1. **브라우저를 실행하여 특정 동작을 테스트할 페이지로 이동합니다.**
2. **HTML DOM에서 특정 엘리먼트에 접근하여 특정 동작을 수행합니다. (클릭, 입력 등)**
3. **모든 동작이 끝나면 브라우저를 종료합니다.**

여기서는 이전 포스팅에서 잠깐 보여드린 것처럼 브라우저로 Google 페이지를 연 다음,  
검색 창에 ‘Selenium’을 입력하여 검색 결과를 확인하는 예제를 수행해볼텐데요.    
해당 과정을 위처럼 정리하면 다음과 같습니다.

1. **Chrome 브라우저를 실행하여 Google 페이지로 이동합니다.**
2. **HTML 엘리먼트에 접근하여 검색어 입력 후 검색 버튼을 눌러 검색 결과를 확인합니다.**
3. **검색 결과를 확인한 후 브라우저를 종료합니다.** 

그럼 처음부터 차근차근 과정을 진행해보겠습니다.  

## 3. 새로운 브라우저 창을 실행하여 Google 페이지로 이동하기

Selenium 테스트를 실행하려면 위에서 언급한 것처럼 먼저 WebDriver를 세팅해야 하는데요.  
미리 추가해둔 WebDriverManager 덕분에 다음 코드 한 줄이면 쉽게 세팅이 가능합니다.  

```java
WebDriverManager.chromedriver().setup(); // WebDriver 중 ChromeDriver를 현재 디바이스에 설정합니다.
```
<br>
WebDriver 세팅이 끝났으니 이제 브라우저를 실행해보겠습니다.  
Chrome 브라우저를 사용할 것이기 때문에 WebDriver 중 ChromeDriver를 생성합니다.  

```java
ChromeDriver driver = new ChromeDriver(); // ChromeDriver를 생성합니다.
```
<br> 
이제 Google 페이지로 이동해야겠죠? 다음 메서드를 호출해봅시다.  

```java
driver.get("https://www.google.com"); // 해당 URL로 이동합니다.
```
<br>
```driver.get('이동할 URL')``` 은 매개변수로 전달한 URL로 페이지를 이동하는 메서드입니다.  
브라우저 실행 및 Google 페이지까지 이동하였으니, 이제 검색어를 입력해보겠습니다.  

## 4. Google 페이지에서 검색어를 입력하여 검색 결과 확인하기    
이제 검색어를 입력하여 검색 결과를 확인할 차례인데요.  
검색어를 입력하려면 HTML 엘리먼트 중 input 태그를 찾으면 되겠죠?  
특정 엘리먼트에 접근하기 위해서 브라우저의 개발자 도구를 열어봅시다.  

<center>
  <img src="/assets/image/dev_tool.jpg" width="100%" height="100%"/>
</center>
<br>

위의 그림처럼 개발자 도구를 통해 특정 엘리먼트를 선택하면 해당 엘리먼트에 적용된 속성(attribute)들을 확인할 수 있는데요.    
Selenium은 이러한 HTML 엘리먼트의 속성들을 이용하여 특정 엘리먼트에 접근할 수 있도록 다양한 API를 제공합니다.  
그럼 특정 엘리먼트를 타겟팅하여 찾을 수 있는 가장 좋은 방법은 무엇일까요? 🤔  
바로 'id' 속성을 이용하면 됩니다. id 속성은 엘리먼트마다 고유한 값을 가지기 때문이죠.  

하지만 아쉽게도 검색어를 입력하는 엘리먼트에 id 속성값이 없네요. 😭  
그럼 차선책으로 name 속성값을 이용해볼텐데, 개발자 도구를 통해 name 속성값이 '**q**'인 것을 확인할 수 있네요.  
다음 메서드를 호출하여 해당 엘리먼트를 DOM에서 찾아봅시다.  

```java
WebElement inputElement = driver.findElement(By.name("q"));
```

```driver.findElement()``` 메서드는 말 그대로 HTML 엘리먼트를 찾는 메서드인데,  
전달하는 매개변수를 보면 ```By.name('속성값')```, 즉 name 속성을 통해 찾겠다는 의미입니다.
코드를 왼쪽에서 오른쪽으로 읽다보면 무슨 의미인지 쉽게 파악이 가능할 정도인데요.      
다음은 Selenium이 제공하는, 다양한 속성들을 이용하여 엘리먼트를 찾는 방법입니다.  

```java
driver.findElement(By.id("엘리먼트 ID 속성값"));
driver.findElement(By.name("엘리먼트 name 속성값"));
driver.findElement(By.className("엘리먼트 class 속성값"));
driver.findElement(By.tagName("엘리먼트 태그 이름"));
driver.findElement(By.cssSelector("선택자"));
driver.findElement(By.xpath("xpath 경로"));
driver.findElement(By.linkText("링크 텍스트"));
driver.findElement(By.partialLinkText("링크 텍스트 일부"));
```

정말 다양한 속성을 통해 HTML 요소를 찾을 수 있는데요.  
위에서 말씀드린 것처럼, id 속성을 이용하는 편이 특정 엘리먼트를 타겟팅하기 유용합니다.  
개인적으로는 먼저 id 속성이 있는지 찾아보고, 없으면 xpath를 사용하는 것을 추천합니다.  
(xpath란, 마크업 언어에서 특정 요소나 속성에 접근하기 위한 경로를 지정하는 방법입니다.)

검색어를 입력하는 HTML 엘리먼트를 찾았으니, 이제 검색어를 입력해야겠죠?  
Selenium에서는 HTML 엘리먼트들을 '**WebElement**'라는 공통 인터페이스로 추상화했습니다.  
해당 인터페이스의 메서드를 호출함으로써 HTML 엘리먼트의 동작을 수행할 수 있는데요.  
특정 문자를 입력하려면 ```sendKey('입력할 문자열')``` 메서드를 호출하면 됩니다.  

```java
WebElement inputElement = driver.findElement(By.name("q"));
inputElement.sendKeys("Selenium"); // 'Selenium'이란 문자열을 입력합니다.
```
<br>

검색어도 입력했으니, 이제 검색 버튼만 누르면 됩니다.  
그럼 개발자 도구를 이용하여 검색 버튼 역할을 하는 엘리먼트를 찾아서 클릭하면 되겠죠?  
그래도 되지만 여기서는 다른 방법을 사용해볼 건데요.    
Selenium은 제어하고자 하는 HTML 엘리먼트가 form 또는 form 내부에 존재할 경우  
HTML form을 원격 서버로 제출하는 방법으로 ```submit()``` 메서드를 제공합니다.  
검색 버튼을 찾아서 클릭까지 제어하는 것보다 과정이 단순하겠죠?  

```java
WebElement inputElement = driver.findElement(By.name("q"));
inputElement.sendKeys("Selenium");
inputElement.submit(); // form을 제출합니다 (= 검색 버튼을 클릭합니다)
```
<br>

지금까지 작성한 코드는 다음과 같습니다.  

```java
public static void main(String[] args) {
    WebDriverManager.chromedriver().setup();
    ChromeDriver driver = new ChromeDriver();
    driver.get("https://www.google.com");

    WebElement inputElement = driver.findElement(By.name("q"));
    inputElement.sendKeys("Selenium");
    inputElement.submit();
}
```
<br>

실행해보면 우리가 원했던 테스트 과정이 정상적으로 수행됨을 확인할 수 있습니다.  
마지막으로 브라우저를 종료하려면 다음 메서드를 호출하면 됩니다.  

```java
driver.quit();  // 브라우저 창을 닫고 WebDriver를 종료합니다.  
```
<br>

그럼 마지막으로 전체 과정을 눈으로 확인해보고 마무리하겠습니다.  

<center>
  <img src="/assets/image/google-search-example.gif" width="100%" height="100%"/>
</center>
<br>

## 5. 마치며
간단한 프로젝트를 생성한 다음 예제를 통해 기본적인 API 사용법에 대해 알아보았습니다.  
정말 간단한 예제라 WebDriver가 가진 모든 기능을 소개해드리진 않았는데요.  
다음 포스팅에서는 WebDriver의 다른 유용한 기능들에 대해서 정리해보겠습니다.  
