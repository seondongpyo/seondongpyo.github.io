---
title:  "[Selenium] Selenium 이란?"
excerpt: "웹 테스트 자동화 프레임워크인 Selenium 기술 정리"

categories:
  - Selenium
tags:
  - Selenium
last_modified_at: 2021-10-04
--- 

## 0. 들어가며
현업에서 Selenium이란 기술을 사용하면서 배운 내용을 정리하고 나눠보고자 합니다.  

## 1. Selenium 이란?
Selenium은 2004년 Jason Huggins에 의해 만들어지기 시작했습니다.  
그는 소트웍스에서 테스트가 자주 필요한 웹 애플리케이션을 개발하고 있었는데요.  
반복적인 수동 테스트가 점점 비효율적이라는 것을 깨닫고 브라우저의 동작을 제어하는 JavaScript 프로그램을 만들게 되는데, 
이를 JavaScriptTestRunner라고 이름 지었습니다.  
그리고 이 JavascriptTestRunner가 오픈 소스화 되어 이름이 Selenium으로 변경되었습니다.  

Selenium은 오픈 소스 테스트 자동화 프레임워크로, 다양한 웹 브라우저와 플랫폼에서 웹 애플리케이션을 테스트할 수 있는 기술입니다.
직접 사용해 본 적은 없지만, JavaScript를 기반으로 개발된 Cypress라는 기술과 동일하다고 볼 수 있는데요.  
사용할 수 있는 언어는 Java, JavaScript, C#, Python, PHP, Ruby, Perl 등 정말 다양합니다.  

이번 포스팅에서는 Selenium을 소개하는 정도로만 하고 마치려고 했지만,  
웹 테스트 자동화가 실제로 어떤 방식으로 진행되는지 궁금하실 분들을 위해서  
간단한 테스트를 작성하고 동작하는 모습을 보여드리고자 합니다.  

## 2. 간단한 테스트 작성하기
(다음 상황은 모두 가상의 시나리오입니다)  

여러분은 지금 Google에서 주니어 개발자로 일하고 있는데요.  
어느 날, 검색 페이지가 잘 동작하는지 테스트를 진행하라고 업무 지시를 받았습니다.  
그래서 Chrome 브라우저를 실행한 다음, 'https://www.google.com/' 으로 이동하여  
검색 키워드를 이것저것 입력하며 정상 동작 여부를 테스트하기 시작합니다.  

그런데 이런 테스트를 한두 번도 아니고 수십 번을 직접 수행하고 있자니, 뭔가 비효율적이란 생각이 들기 시작합니다. 
필요할 때마다 나를 대신하여 이 귀찮은 테스트를 대신해 줄 자동화 툴을 찾다가 여러분은 Selenium을 발견하게 되었고, 
Selenium으로 검색 자동화 테스트를 작성하기 시작합니다.  

Google 검색 페이지에 접근하여 검색 키워드를 입력 후 검색 결과를 확인하고자 한다면  
다음 자바 코드로도 충분하지 않을까 싶습니다(~~는 물론 거짓말입니다~~).  

```
@DisplayName("구글에서 검색어를 입력 후 검색 결과를 확인한다.")
@Test
void search() {
	WebDriverManager.chromedriver().setup();

	ChromeDriver driver = new ChromeDriver();
	driver.get("https://www.google.com");

	WebElement inputKeyword = driver.findElement(By.name("q"));
	inputKeyword.sendKeys("Selenium");
	inputKeyword.submit();

	assertThat(driver.getTitle()).contains("Selenium");
}
```
<br>
위 코드의 실제 테스트 동작 화면입니다.  
(중간에 화면을 녹화하는 코드가 있는데 무시하셔도 됩니다)

<center>
  <img src="/assets/image/google-search-test.gif" width="100%" height="100%"/>
</center>
<br>

## 3. 마치며
이번 포스팅에서는 말 그대로 Selenium이 무엇인지를 간략하게 소개하고자 했기 때문에  
자세한 API 사용법 등을 기대하고 오신 분들이라면 실망하셨을 수도 있을 것 같네요.  
다음 포스팅부터 Selenium의 기본적인 API를 사용하는 방법을 소개하고자 합니다.  
  
Selenium이 궁금하신 분들은 아래 사이트를 참고해보시면 좋을 것 같네요.  
- **[Selenium 공식 사이트](https://www.selenium.dev/)**
- **[Selenium Tutorial](https://www.guru99.com/selenium-tutorial.html)**
