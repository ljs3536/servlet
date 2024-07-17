# /24-03-18

## 웰 - HTTP 기반
### 클라이언트 - 인터넷 - 서버 (HTTP)

### 모든것이 HTTP
#### HTTP 메시지에 모든 것을 전송
- HTML, TEXT
- IMAGE, 음성, 영상, 파일
- JSON, XML (API)
- 거의 모든 형태의 데이터 전송 가능
- 서버간에 데이터를 주고 받을 때도 대부분 HTTP 사용

## 웹서버 (Web Server)
- HTTP 기반으로 동작
- 정적 리소스 제공, 기타 부가기능
- 정적(파일) HTML, CSS, JS, 이미지, 영상
- 예) NGINX, APACHE

## 웹 애플리케이션 서버 (WAS- Web Application Server)
- HTTP 기반으로 동작
- 웹 서버 기능 포함 + (정적 리소스 제공 가능)
- 프로그램 코드를 실행해서 애플리케이션 로직 수행
  - 동적 HTML, HTTP API(JSON)
  - 서블릿, JSP, 스프링 MVC
- 예) 톰캣(Tomcat), Jetty, Undertow

## 웹 서버, 웹 애플리케이션 서버(WAS) 차이
- 웹 서버는 정적 리소스(파일), WAS는 애플리케이션 로직
- 사실은 둘의 용어도 경계도 모호함
  - 웹 서버도 프로그램을 실행하는 기능을 포함하기도 함
  - 웹 애플리케이션 서버도 웹 서버의 기능을 제공함
- 자바는 서블릿 컨테이너 기능을 제공하면 WAS
  - 서블릿 없이 자바 코드를 실행하는 서버 프레임워크도 있다
- WAS는 애플리케이션 코드를 실행하는데 더 특화

## 웹 시스템 구성 - WAS, DB
- WAS, DB 만으로 시스템 구성 가능
- WAS는 정적 리소스, 애플리케이션 로직 모두 제공 가능
- WAS가 너무 많은 역할을 담당, 서버 과부하 우려
- 가장 비싼 애플리케이션 로직이 정적 리소스 때문에 수행이 어려울 수 있음
- WAS 장애 시 오류 화면도 노출 불가능

## 웹 시스템 구성 - WEB, WAS, DB
- 정적 리소스는 웹 서버가 처리
- 웹 서버는 애플리케이션 로직같은 동적인 처리가 필요하면 WAS에 요청을 위임
- WAS는 중요한 애플리케이션 로직 처리 전담
- 효율적인 리소스 관리
  - 정적 리소스가 많이 사용되면 WEB 서버 증설
  - 애플리케이션 리소스가 많이 사용되면 WAS 증설
- 정적 리소스만 제공하는 웹 서버는 잘 죽지 않음
- 애플리케이션 오직이 동작하는 WAS 서버는 잘 죽음
- WAS, DB 장애 시 WEB 서버가 오류 화면 제공 가능

# /24-03-19
## Servlet
### HTML Form 데이터 전송
#### POST 전송 - 저장

### 서버에서 처리해야 하는 업무
#### 웹 애플리케이션 서버 직접 구현
- 서버 TCP/IP 연결 대기, 소켓 연결
- HTTP 요청 메시지를 파싱해서 읽기
- POST 방식, /save URL 인지
- Content-Type 확인
- HTTP 메시지 바디 내용 파싱
  - usertname, age 데이터를 사용할 수 있게 파싱
- 저장 프로세스 실행
- 비즈니스 로직 실행
  - 데이터 베이스에 저장 요청
- HTTP 응답 메시지 생성 시작
  - HTTP 시작 라인 생성
  - Header 생성
  - 메시지 바디에 HTML 생성에서 입력
- TCP/IP에 응답 전달, 소켓 종료

### Servlet은 위 처리에서 비즈니스로직을 제외한 부분을 다 지원해준다.

### 서블릿 특징
- urlPatterns(/hello)의 URL이 호출되면 서블릿 코드가 실행
- HTTP 요청 정보를 편리하게 사용할 수 있는 HttpServletRequest
- HTTP 응답 정보를 편리하게 제공할 수 있는 HttpServletResponse
- 개발자는 HTTP 스펫을 매우 편리하게 사용

### 서블릿 HTTP 요청, 응답 흐름
- HTTP 요청 시
  - WAS는 Request, Response 객체를 새로 만들어서 서블릿 객체 호출
  - 개발자는 Request 객체에서 HTTP 요청 정보를 편리하게 꺼내서 사용
  - 개발자는 Response 객체에 HTTP 응답 정보를 편리하게 입력
  - WAS는 Response 객체에 담겨있는 내용으로 HTTP 응답 정보를 생성

### 서블릿 컨테이너
- 톰캣처럼 서블릿을 지원하는 WAS를 서블릿 컨테이너라고 함
- 서블릿 컨테이너는 서블릿 객체를 생성, 초기화, 호출, 종료하는 생명주기 관리
- 서블릿 객체는 싱글톤으로 관리
  - 고객의 요청이 올 때 마다 계속 객체를 생성하는 것은 비효율
  - 최초 로딩 시점에 서블릿 객체를 미리 만들어두고 재활용
  - 모든 고객 요청은 동일한 서블릿 객체 인스턴스에 접근
  - 공유 변수 사용 주의
  - 서블릿 컨테이너 종료 시 함께 종료
- JSP도 서블릿으로 변환 되어서 사용
- 동시 요청을 위한 멀티 쓰레드 처리 지원

# /24-03-20
## 동시요청 - 멀티 쓰레드
### 쓰레드
- 애플리케이션 코드를 하나하나 순차적으로 실행하는 것은 쓰레드
- 자바 메인 메서드를 처음 실행하면 main이라는 이름의 쓰레드가 실행
- 쓰레드가 없다면 자바 애플리케이션 실행이 불가능
- 쓰레드는 한번에 하나의 코드 라인만 수행
- 동시 처리가 필요하면 쓰레드를 추가로 생성

### 단일요청 - 쓰레드 하나 사용

### 요청마다 쓰레드 생성 장단점
- 장점
  - 동시 요청을 처리할 수 있다.
  - 리소스(CPU, 메모리)가 허용할 떄 까지 처리가능
  - 하나의 쓰레드가 지연 되어도, 나머지 쓰레드는 정상 작동한다.
- 단점
  - 쓰레드의 생성 비용은 매우 비싸다.
    - 고객의 요청이 올 때 마다 쓰레드를 생성하면, 응답 속도가 늦어진다.
  - 쓰레드는 컨텍스트 스위칭 비용이 발생한다.
  - 쓰레드 생성에 제한이 없다.
    - 고객 요청이 너무 많이 오면, CPU, 메모리 임계점을 넘어서 서버가 죽을 수 있다.

### 쓰레드 풀
#### 요청마다 쓰레드 생성의 단점 보완
- 특징
  - 필요한 쓰레드를 쓰레드 풀에 보관하고 관리한다.
  - 쓰레드 풀에 생성 가능한 쓰레드의 최대치를 관리한다. 톰캣은 최대 200개 기본 설정(변경 가능)
- 사용
  - 쓰레드가 필요하면, 이미 생성되어 있는 쓰레드를 쓰레드 풀에서 꺼내서 사용한다.
  - 사용을 종료하면 쓰레드 풀에 해당 쓰레드를 반납한다.
  - 최대 쓰레드가 모두 사용중이어서 쓰레드 풀에 쓰레드가 없으면?
    - 기다리는 요청은 거절하거나 특정 숫자만큼만 대기하도록 설정할 수 있다.
- 장점
  - 쓰레드가 미리 생성되어 있으므로, 쓰레드를 생성하고 종료하는 비용이 절약되고, 응답 시간이 빠르다.
  - 생성 가능한 쓰레드의 최대치가 있으므로 너무 많은 요청이 들어와도 기존의 요청은 안전하게 처리할 수 있다.

### 쓰레드 풀 실무 팁
- WAS의 주요 튜닝 포인트는 최대 쓰레드(max thread) 수이다.
- 이 값을 너무 낮게 설정하면?
  - 동시 요청이 많으면, 서버 리소스는 여유롭지만, 클라이언트는 금방 응답 지연
- 이 값을 너무 높게 설정하면?
  - 동시 요청이 많으면, CPU, 메모리 리소스 임계점 초과로 서버 다운
- 장애 발생 시?
  - 클라우드면 일단 서버부터 늘리고, 이후에 튜닝
  - 클라우드가 아니면 열심히 튜닝

- 적정 숫자는 어떻게 찾나요?
- 애플리케이션 로직의 복잡도, CPU, 메모리, IO 리소스 상황에 따라 모두 다름
- 성능 테스트
  - 최대한 실제 서비스와 유사하게 성능 테스트 시도
  - 툴 : 아파치 ab, 제이미터, nGrinder

### WAS의 멀티 쓰레드 지원 핵심
- 멀티 쓰레드에 대한 부분은 WAS가 처리
- 개발자가 멀티 쓰레드 관련 코드를 신경쓰지 않아도 됨
- 개발자는 마치 싱글 쓰레드 프로그래밍을 하듯이 편리하게 소스 코드를 개발
- 멀티 쓰레드 환경이므로 싱글톤 객체(서블릿, 스프링 빈)는 주의해서 사용

# /24-03-22
## HTML, HTTP API, CSR, SSR
### 정적 리소스
- 고정된 HTML 파일, CSS, JS, 이미지, 영상 등을 제공
- 주로 웹 브라우저

### HTML 페이지
- 동적으로 필요한 HTML 파일을 생성해서 전달
- 웹 브라우저: HTML 해석

### HTTP API
- HTML이 아니라 데이터를 전달
- 주로 JSON 형식 사용
- 다양한 시스템에서 호출
- 데이터만 주고 받음, UI 화면이 필요하면, 클라이언트가 별도 처리
- 앱, 웹 클라이언트, 서버 to 서버

- 주로 JSON 형태로 데이터 통신
- UI 클라이언트 접점
  - 앱 클라이언트(아이폰, 안드로이드, PC 앱)
  - 웹 브라우저에서 자바스크립트를 통한 HTTP API 호출
  - React, Vue.js 같은 웹 클라이언트
- 서버 to 서버
  - 주문 서버 -> 결제 서버
  - 기업간 데이터 통신

### 서버사이드 렌더링, 클라이언트 사이드 렌더링
#### SSR - 서버 사이드 렌더링 : 서버에서 최종 HTML을 생성해서 클라이언트에 전달
- HTML 최종 결과를 서버에서 만들어서 웹 브라우저에 전달
- 주로 정적인 화면에 사용
- 관련 기술 : JSP, 타임리프 -> 백엔드 개발자

### CSR - 클라이언트 사이드 렌더링
- HTML 결과를 자바스크립트를 사용해 웹 브라우저에서 동적으로 생성해서 적용
- 주로 동적인 홤녀에 사용, 웹 환경을 마치 앱처럼 필요한 부분부분 변경할 수 있음
- 예) 구글 지도, Gmail, 구글 캘린더
- 관련기술 : React, Vue.js -> 웹 프론트엔드 개발자

### 참고 
- React, Vue.js를 CSR + SSR 동시에 지원하는 웹 프레임워크도 있음
- SSR을 사용하더라도, 자바스크립트를 사용해서 화면 일부를 동적으로 변경 가능

# /24-03-23
## 자바 백엔드 웹 기술 역사
### 자바 웹 기술 역사
- 애노테이션 기반의 스프링 MVC 등장
  - @Controller
- 스프링 부트의 등장
  - 스프링 부트는 서버를 내장
  - 과거에는 서버에 WAS를 직접 설치하고, 소스는 War파일을 마들어서 설치한 WAS에 배포
  - 스프링 부투는 빌드 결과 (Jar)에 WAS 서버 포함 -> 빌드 배포 단순화

### 최신기술 - 스프링 웹 기술의 분화
- Web Servlet - Spring MVC
- Web Reactive - Spring WebFlux

### 최신기술 - 스프링 웹 플럭스(Web Flux)
- 특징
  - 비동기 넌 블러킹 처리
  - 최소 쓰레드로 최대 성능 - 쓰레드 컨텍스트 스위칭 비용 효율화
  - 함수형 스타일로 개발 - 동시처리 코드 효율화
  - 서블릿 기술 사용 X
- 그런데
  - 웹 플럭스는 기술적 난이도 매우 높음
  - 아직은 RDB 지원 부족
  - 일반 MVC의 쓰레드 모델도 충분히 빠르다
  - 실무에서 아직 많이 사용하지는 않음 (전체 1% 이하)

### 자바 뷰 템플릿 역사 - HTML을 편리하게 생성하는 뷰 기능
- JSP
  - 속도 느림, 기능 부족
- 프리마커(Freemarker), Velocity(벨로시티)
  - 속도 문제 해결, 다양한 기능
- 타임리프(Thymeleaf)
  - 내추럴 템플릿 : HTML의 모양을 유지하면서 뷰 템플릿 적용 가능
  - 스프링 MVC와 강력한 기능 통합
  - 최선의 선택, 단 성능은 프리마커, 벨로시티가 더 빠름

## Hello 서블릿
### 참고
서블릿은 톰캣 같은 웹 어플리케이션 서버를 직접 설치하고, 그 위에 서블릿 코드를 클래스 파일로 빌드해서 올린 다음, 톰캣 서버를 실행하면 된다.
하지만 이 과정은 매우 번거롭다. 스프링 부트는 톰캣 서버를 내장하고 있으므로, 톰캣 서버 설치 없이 편리하게 서블릿 코드를 실행할 수 있다.

- @WebServlet : 서블릿 애노테이션
  - name : 서블릿 이름
  - urlPatterns: URL 매핑
  HTTP 요청을 통해 매핑된 URL이 호출되면 서블릿 컨테이너는 다음 메서드를 실행한다.
  protected void service(HttpServletRequest request, HttpServletResponse response)

# /24-03-24
## HttpServletRequest - 개요
### HttpServletRequest 역할
HTTP 요청 메시지를 개발자가 직접 파싱해서 사용해도 되지만, 매우 불편할 것이다. 
서블릿은 개발자가 HTTP 요청 메시지를 편리하게 사용할 수 있도록 개발자 대신에 HTTP 요청 메시지를 파싱한다. 
그리고 겨과를 HttpServletRequest 객체에 담아서 제공한다.

### Http 요청 메시지
POST /save HTTP/1.1
Host: localhost:8080
Content-Type: application/x-www-form-urlencoded

- START LINE
  - HTTP 메소드
  - URL
  - 쿼리 스트링
  - 스키마, 프로토콜
- 헤더
  - 헤더 조회
- 바디
  - form 파라미터 형식 조회
  - message body 데이터 직접 조회

### 임시 저장소 기능
- 해당 HTTP 요청이 시작부터 끝날 때 까지 유지되는 임시 저장소 기능
  - 저장 : request.setAttribute(name, value)
  - 조회 : request.getAttribute(name)

### 세션 관리 기능
- request.getSession(create:true);

### 중요
HttpServletRequest, HttpServletResponse를 사용할 때 가장 중요한 점은 이 객체들이 HTTP 요청 메시지, HTTP 응답 메시지를 편리하게 사용하도록 도와주는 객체라는 점이다.
따라서 이 기능에 대해서 깊이있는 이해를 하려면 HTTP 스펙이 제공하는 요청, 응답 메시지 자체를 이해해야한다.

# /24-03-25
## HttpServletRequest - 기본 사용법

# /24-03-26
## HTTP 요청 데이터 - 개요
HTTP 요청 메시지를 통해 클라이언트에서 서버로 데이터를 전달하는 방법을 알아보자

### 주로 다음 3가지 방법을 사용한다.
### GET - 쿼리 파라미터
- /url?username=hello&age=20
- 메시지 바디 없이, URL의 쿼리 파라미터에 데이터를 포함해서 전달
- 예) 검색, 필터, 페이징등에서 많이 사용하는 방식

### POST - HTML Form
- content-type: applications/x-www-form-urlencoded
- 메시지 바디에 쿼리 파라미터 형식으로 전달 username=hello&age=20
- 예) 회원가입, 상품 주문, HTML Form 사용

### HTTP message body에 데이터를 직접 담아서 요청
- HTTP API에서 주로 사용, JSON, XML, TEXT
- 데이터 형식은 주로 JSON 사용
- POST, PUT, PATCH

# /24-03-27
## HTTP 요청 데이터 - GET 쿼리 파라미터
다음 데이터를 클라이언트에서 서버로 전송해보자
전달 데이터
- username = hello
- age = 20
메시지 바디 없이, URL의 쿼리 파라미터를 사용해서 데이터를 전달하자
예) 검색, 필터, 페이징등에서 많이 사용하는 방식

### 복수 파라미터에서 단일 파라미터 조회
username=hello&username=kim 과 같이 파라미터 이름은 하나인데, 값이 중복이면 어떻게 될까?
request.getParameter()는 하나의 파라미터 이름에 대해서 단 하나의 값만 있을 때 사용해야한다.
지금처럼 중복일 때는 request.getParameterValues()를 사용해야 한다.
참고로 이렇게 중복일 때 request.getParameter()를 사용하면 request.getParameterValues()의 첫번째 값을 반환한다.

# /24-03-28
## HTTP 요청 데이터 - POST HTML Form
이번에는 HTML의 Form을 사용해서 클라이언트에서 서버로 데이터를 전송해보자
주로 회원가입, 상품 주문등에서 사용하는 방식이다.

### 특징
- content-type: application/x-www-form-urlencoded
- 메시지 바디에 쿼리 파라미터 형식으로 데이터를 전달한다.

### 주의
웹 브라우저가 결과를 캐치하고 있어서, 과거 작성했던 html결과가 보이는 경우도 있다.
이때는 웹 브라우저의 새로 고침을 직접 선택해주면 된다. 물론 서버를 재시작 하지 않아서 그럴 수도 있다.

POST의 HTML Form을 전송하면 웹 브라우저는 다음 형식으로 HTTP 메시지를 만든다. (웹 브라우저 개발자 모드 확인)
- 요청 URL : http://localhost:8080/request-param
- content-type : application/x-www-form-urlencoded
- message body : username=hello&age=20

application/x-www-form-urlencoded 형식은 앞서 GET에서 살펴본 쿼리 파라미터 형식과 같다. 
따라서 쿼리 파라미터 조회 메서드를 그래도 사용하면 된다.
클라이언트(웹 브라우저) 입장에서는 두 방식에 차이가 있지만, 서버 입장에서는 둘의 형식이 동일하므로,
request.getParameter()로 편리하게 구분없이 조회할 수 있다.

정리하면 request.getParameter()는 GET URL 쿼리 파라미터 형식도 지원하고, POST HTML Form 형식도 둘 다 지원한다.

### 참고
content-type은 HTTP 메시지 바디의 데이터 형식을 지정한다.
GET URL 쿼리 파라미터 형식으로 클라이언트에서 서버로 데이터를 전달할 때는 HTTP 메시지 바디를 사용하지 않기 때문에 content-type이 없다.
POST HTML Form 형식으로 데이터를 전달하면 HTTP 메시지 바디에 해당 데이터를 포함해서 보내기 때문에 바디에 포함된 데이터가 어떤 형식인지
content-type을 꼭 지정해야 한다. 이렇게 폼으로 데이터를 전송하는 형식을 application/x-www-form-urlencoded라 한다.

# /24-03-29
## HTTP 요청 데이터 - API 메시지 바디 - 단순 텍스트
- HTTP message body에 데이터를 직접 담아서 요청
  - HTTP API에서 주로 사용, JSON, XML, TEXT
  - 데이터 형식은 주로 JSON 사용
  - POST, PUT, PATCH

- 먼저 가장 단순한 텍스트 메시지를 HTTP 메시지 바디에 담아서 전송하고, 읽어보자
- HTTP 메시지 바디의 데이터를 InputStream을 사용해서 직접 읽을 수 있다.

# /24-03-30
## HTTP 요청 데이터 - API 메시지 바디 - JSON

### 참고
JSON 결과를 파싱해서 사용할 수 있는 자바 객체로 변환하려면 Jackson, Gson 같은 JSON 변환 라이브러리를 추가해서 사용해야 한다.
스프링 부트로 Spring MVC를 선택하면 기본으로 Jackson 라이브러리(ObjectMapper)를 함꼐 제공한다.

### 참고
HTML form 데이터도 메시지 바디를 통해 전송되므로 직접 읽을 수 있다.
하지만 편리한 파라미터 조회 기능을 이미 제공하기 때문에 파라미터 조회 기능을 사욯하면 된다.

# /24-03-31
## HttpServletResponse - 기본 사용법
### HttpServletResponse 역할
#### HTTP 응답 메시지 생성
- HTTP 응답코드 지정
- 헤더 생성
- 바디 생성

#### 편의 기능 제공
- Content-Type, 쿠키, Redirect

# /24-04-01
## HTTP 응답 데이터 - 단순 텍스트, HTML
HTTP 응답 메시지는 주로 다음 내용을 담아서 전달한다.

- 단순 텍스트 응답
  - 앞에서 사용했던 (writer.println("ok");)
- HTML 응답
- HTTP API, MessageBody JSON 응답

# /24-04-02
## HTTP 응답 데이터 - API JSON

# /24-04-03
## 회원 관리 웹 애플리케이션 요구사항
### 회원정보
- 이름 : username
- 나이 : age

### 기능 요구사항
- 회원 저장
- 회원 목록 조회

# /24-04-04
## 서블릿으로 회원 관리 웹 애플리케이션 만들기

# /24-04-05
### 템플릿 엔진으로
지금까지 서블릿과 자바 코드만으로 HTML을 만들어보았다. 서블릿 덕분에 동적으로 원하는 HTML을 마음껏 만들 수 있다.
정적인 HTML 문서라면 화면이 계속 달라지는 회원의 저장 결과라던가, 회원 목록 같은 동적인 HTML을 만드는 일은 불가능 할 것이다.
그런데, 코드에서 보듯이 이것은 매우 복잡하고 비효율 적이다.
자바 코드로 HTML을 만들어 내는 것 보다 차라리 HTML 문서에 동적으로 변경해야 하는 부분만 자바 코드를 넣을 수 있다면 더 편리할 것이다.
이것이 바로 템플릿 엔진이 나온 이유이다. 
템플릿 엔진을 사용하면 HTML 문서에서 필요한 곳만 코드를 적용해서 동적으로 변경할 수 있다.
템플릿 엔진에는 JSP, Thymeleaf, Freemarker, Velocity등이 있다.

### 참고 
JSP는 성능과 기능면에서 다른 템플릿 엔진과의 경쟁에서 밀리면서, 점점 사장되어 가는 추세이다.
템플릿 엔진들은 각각 장단점이 있는데, 가으이에서는 JSP는 앞부분에서 잠깐 다루고, 스프링과 잘 통합되는 Thymeleaf를 사용한다.

# /24-04-06
## JSP로 회원관리 웹 애플리케이션 만들기

### JSP 라이브러리 추가

# /24-04-07
## MVC 패턴 - 개요
### 너무 많은 역할
하나의 서블릿이나 JSP만으로 비즈니스 로직과 뷰 렌더링까지 모두 처리하게 되면, 너무 많은 역할을 하게되고, 결과적으로 유지보수가 어려워진다.
비즈니스 로직을 호출하는 부분에 변경이 발생해도 해당 코드를 손대야 하고, UI를 변경할 일이 있어도 비즈니스 로직이 함께 있는 해당 파일을 수정해야 한다.
HTML 코드 하나 수정해야 하는데, 수백줄의 자바 코드가 함께 있다고 상상해보라!
또는 비즈니스 로직을 하나 수정해야하는데 수백 수천줄의 HTML 코드가 함께 있다고 상상해보라

### 변경의 라이프 사이클
사실 이게 정말 중요한데, 진짜 문제는 둘 사이에 변경의 라이프 사이클이 다르다는 점이다.
예를 들어서 UI를 일부 수정하는 일과 비즈니스 로직을 수정하는 일은 각각 다르게 발생할 가능성이 매우 높고 대부분 서로에게 영향을 주지 않는다.
이렇게 변경의 라이프 사이클이 다른 부분을 하나의 코드로 관리하는 것은 유지보수하기 좋지 않다.(물론 UI가 많이 변하면 함께 변경될 가능성도 있다.)

### 기능 특화
특히 JSP 같은 뷰 템플릿은 화면을 렌더링 하는데 최적화 되어 있기 때문에 이 부분의 업무만 담당하는 것이 가장 효과적이다.

### Model View Controller
MVC 패턴은 지금까지 학습한 것 처럼 하나의 서블릿이나, JSP로 처리하던 것을 컨트롤러(Controller)와 뷰(View)라는 영역으로 서로 역할을 나눈것을 말한다.
웹 애플리케이션ㄴ은 보톤 이 MVC 패턴을 사용한다.

- 컨트롤러: HTTP 요청을 받아서 파라미터를 검증하고, 비즈니스 로직을 실행한다. 그리고 뷰에 전달할 결과 데이터를 조회해서 모델에 담는다.
- 모델 : 뷰에 출력할 데이터를 담아둔다. 뷰가 필요한 데이터를 모두 모델에 담아서 전달해주는 덕분에 뷰는 비즈니스 로직이나 데이터 접근을 몰라도 되고, 화면을 렌더링 하는 일에 집중할 수 있다.
- 뷰 : 모델에 담겨있는 데이터를 사용해서 화면을 그리는 일에 집중한다. 여기서는 HTML을 생성하는 부분을 말한다.

### 참고
컨트롤러에 비즈니스 로직을 둘 수도 있지만, 이렇게 되면 컨트롤러가 너무 많은 역할을 담당한다. 
그래서 일반적으로 비즈니스 로직은 서비스라는 계층을 별도로 만들어서 처리한다. 
그리고 컨트롤러는 비즈니스 로직이 있는 서비스를 호출하는 담당을 한다. 
참고로 비즈니스 로직을 변경하면 비즈니스 로직을 호출하는 컨트롤러의 코드도 변경될 수 있다.
앞에서는 이해를 돕기 위해 비즈니스 로직을 호출한다는 표현 보자는 비즈니스 로직이라 설명했다.

# /24-04-08
## MVC패턴 - 적용 1

dispatcher.forward() : 다른 서블릿이나 JSP로 이동할 수 있는 기능이다. 서버 내부에서 다시 호출이 발생한다.

### /WEB-INF
이 경로안에 JSP가 있으면 외부에서 직접 JSP를 호출할 수 없다. 
우리가 기대하는 것은 항상 컨트롤러를 통해서 JSP를 호출하는 것이다.

### redirect vs forward
리다이렉트는 실제 클라이언트(웹 브라우저)에 응답이 나갔다가, 클라이언트가 redirect 경로로 다시 요청한다. 
따라서 클라이언트가 인지할 수 있고, URL 경로도 실제로 변경된다. 반면에 포워드는 서버 내부에서 일어나는 호출이기 때문에 클라이언트가 전혀 인지하지 못한다.

# /24-04-09
## MVC패턴 - 적용 2
### 회원 등록 폼 - 뷰
여기서 form의 action을 보면 절대경로(/로 시작)이 아니라 상대경로(/없이 시작)하는 것을 확인할 수 있다.
이렇게 상대경로를 사용하면 폼 전송 시 현재 URL이 속한 계층 경로 + save가 호출된다.
현재 계층 경로 : /servlet-mvc/members/
결과 : /servlet-mvc/members/save

# /24-04-10
## MVC패턴 - 한계
MVC 패턴을 적용한 덕분에 컨틀롤러의 역할과 뷰를 렌더링 하는 역할을 명확하게 구분할 수 있다.
특히 뷰는 화면을 그리는 역할에 충실한 덕분에, 코드가 깔끔하고 직관적이다.
단순하게 모델에서 필요한 데이터를 꺼내고, 화면을 만들면 된다.
그런데 컨트롤러는 딱 봐도 중복이 많고, 필요하지 않은 코드들도 많이 보인다.

### MVC 컨트롤러의 단점
#### 포워드 중복
View로 이동하는 코드가 항상 중복 호출되어야 한다. 
물론 이 부분을 메서드로 공론화해도 되지만, 해당 메서드도 항상 직접 호출해야한다.

#### ViewPath에 중복
- prefix : /WEB-INF/views/
- suffix : .jsp
그리고 만약 jsp가 아닌 thymeleaf같은 다른 뷰로 변경한다면 전체 코드를 다 변경해야 한다.

#### 사용하지 않는 코드
다음 코드를 사용할 때도 있고, 사용하지 않을 때도 있다.
특히 response는 현재 코드에서 사용되지 않는다.
HttpServletRequest request, HttpServletResponse response
그리고 이런 위 코드를 사용하는 코드는 테스트 케이스를 작성하기도 어렵다.

#### 공통 처리가 어렵다.
기능이 복잡해질 수록 컨트롤러에서 공통으로 처리해야 하는 부분이 점점 더 많이 증가할 것이다.
단순히 공통 기능을 메서드로 뽑으면 될 것 같지만, 결과적으로 해당 메서드를 항상 호출해야 하고,
실수로 호출하지 않으면 문제가 될 것이다. 
그리고 호출하는 것 자체도 중복이다.

#### 정리하면 공통 처리가 어렵다는 문제가 있다.
이 문제를 해결하려면 컨틀롤러 호출 전에 먼저 공통 기능을 처리해야 한다.
소위 수문장역할을 하는 기능이 필요하다.
프론트 컨트롤러(Front Controller)패턴을 도입하면 이런 문제를 깔끔하게 해결할 수 있다.
스프링 MVC의 핵심도 바로 이 프론트 컨트롤러에 있다.

# /24-04-11
## 프론트 컨트롤러 패턴 소개
### FrontController 패턴 특징
- 프론트 컨트롤러 서블릿 하나로 클라이언트의 요청을 받음
- 프론트 컨트롤러가 요청에 맞는 컨트롤러를 찾아서 호출
- 입구를 하나로!
- 공통 처리 가능
- 프론트 컨트롤러를 제외한 나머지 컨트롤러는 서블릿을 사용하지 않아도 됨

### 스프링 웹 MVC와 프론트 컨트롤러
스프링 웹 MVC의 핵심도 바로 FrontController
스프링 웹 MVC의 DispatcherServlet이 FrontController 패턴으로 구현되어 있음

# /24-04-12
## 프론트 컨트롤러 도입 - V1
프론트 컨트롤러를 단계적으로 도입해보자.
이번 목표는 기존 코들르 최대한 유지하면서, 프론트 컨트롤러를 도입하는 것이다.
먼저 구조를 맞추어두고 점진적으로 리펙토링 해보자.

서블릿과 비슷한 모양의 컨트롤러 인터페이스를 도입한다. 
각 컨트롤러들은 이 인터페이스를 구현하면 된다.
프론트 컨트롤러는 이 인터페이스를 구현한 컨트롤러를 만들어보자.
지금 단계에서는 기존 로직을 최대한 유지하는게 핵심이다.

# /24-04-13
내부 로직은 기존 서블릿과 거의 같다.
이제 프론트 컨트롤러를 만들어보자.

### 프론트 컨트롤러 부석
#### urlPatterns
- urlPatterns = "/front-controller/v1/*" : /front-controller/v1 를 포함한 하위 모든 요청은 이 서블릿에서 받아들인다.

#### controllerMap
- key : 매핑URL
- value : 호출된 컨트롤러

#### service()
먼저 requestURI를 조회해서 실제 호출할 컨트롤러를 controllerMap에서 찾는다.
만약 없다면 404(SC_NOT_FOUND)상태 코드를 반환한다.
컨트롤러를 찾고 controller.process(request, response)를 호출해서 해당 컨트롤러를 실행한다.

#### JSP
JSP는 이전 MVC에서 사용했던 것을 그대로 사용한다.

# /24-04-14
## View 분리 - V2
모든 컨트롤러에서 뷰로 이동하는 부분에 중복이 있고, 깔끔하지 않다.
이 부분을 깔끔하게 분리하기 위해 별도로 뷰를 처리하는 객체를 만들자.

### MyView
뷰 객체는 이후 다른 버전에서도 함께 사용하므로 패키지 위치를 frontcontroller에 두었다.

ControllerV2의 반환 타입이 MyView이므로 프론트 컨트롤러는 컨트롤러의 호출 결과로 MyView를 반환 받는다.
그리고 view.render()를 호출하면 forward로직을 수행해서 JSP가 실행된다.

프론트 컨트롤러의 도입으로 MyView객체의 render()를 호출하는 부분을 모두 일관되게 처리할 수 있다.
각각의 컨트롤러는 MyView객체를 생성만 해서 반환하면 된다.

# /24-04-15
## Model 추가 - V3
### 서블릿 종속성 제거
컨트롤러 입장에서 HttpServletRequest, HttpServletResponse이 꼭 필요할까?
요청 파라미터 정보는 자바의 Map으로 대신 넘기도록 하면 지금 구조에서는 컨트롤러가 서블릿 기술을 몰라도 동작할 수 있다.
그리고 request 객체를 Model로 사용하는 대신에 별도의 Model 객체를 만들어서 반환하면 된다.
우리가 구현하는 컨트롤러가 서블릿 기술을 전혀 사용하지 않도록 변경해보자.
이렇게 하면 구현 코드도 매우 단순해지고, 테스트 코드 작성이 쉽다.

### 뷰 이름 중복 제거
컨트롤러에서 지정하는 뷰 이름에 중복이 있는 것을 확인할 수 있다.
컨트롤러는 뷰의 논리 이름을 반환하고, 실제 물리 위치의 이름은 프론트 컨트롤러에서 처리하도록 단순화 하자.
이렇게 해두면 향후 뷰의 폴더 위치가 함께 이동해도 프론트 컨트롤러만 고치면 된다.
- /WEB-INF/views/new-form.jsp -> new-form
- /WEB-INF/views/save-result.jsp -> save-result
- /WEB-INF/views/members.jsp -> members

### V3구조

### ModelView
지금까지 컨트롤러에서 서블릿에 종속적인 HttpServletRequest를 사용했다. 
그리고 Model도 request.setAttribute()를 통해 데이터를 저장하고 뷰에 전달했다.
서블릿의 종속성을 제거하기 위해 Model을 직접 만들고, 추가로 View 이름까지 전달하는 객체를 만들어보자.
(이번 버전에서는 컨트롤러에서 HttpServletRequest를 사용할 수 없다. 
따라서 직접 request.setAttribute()를 호출할 수도 없다. 따라서 Model이 별도로 필요하다.)

참고로 ModelView 객체는 다른 버전에서도 사용하므로 패키지를 frontcontroller에 둔다.

# /24-04-16
뷰의 이름과 뷰를 렌더링할 때 필요한 model 객체를 가지고 있다. model은 단순히 map으로 되어 있으므로 컨트롤러에서 뷰에 필요한 데이터를 key, value로 넣어주면 된다.

### ControllerV3
이 컨트롤러는 서블릿 기술을 전혀 사용하지 않는다. 
따라서 구현이 매우 단순해지고, 테스트 코드 작성시 테스트 하기 쉽다.
HttpServletRequest가 제공하는 파라미터는 프론트 컨트롤러가 paramMap에 담아서 호출해주면 된다.
응답 결과로 뷰 이름과 뷰에 전달할 Model 데이터를 포함하는 ModelView 객체를 반환하면 된다.

### 뷰 리졸버
컨트롤러가 반환한 노리 뷰 이름을 실제 물리 뷰 경로로 변경한다.
그리고 실제 물리 경로가 있는 MyView 객체를 반환한다.

# /24-04-17
## 단순하고 실용적인 컨트롤러 - V4
앞서 만든 v3 컨트롤러는 서블릿 종속성을 제거하고 뷰 경로의 중복을 제거하는 등, 잘 설계된 컨트롤러이다.
그런데 실제 컨트롤러 인터페이스를 구현하는 개발자 입장에서 보면, 항상 ModelView 객체를 생성하고 반환해야 하는 부분이 조금은 번거롭다.
좋은 프레임워크는 아키텍처도 중요하지만, 그와 더불어 실제 개발하는 개발자가 단순하고 편리하게 사용할 수 있어야 한다.
소위 실용성이 있어야 한다.

이번에는 v3를 조금 변경해서 실제 구현하는 개발자들이 매우 편리하게 개발할 수 있는 v4버전을 개발해보자.

- 기본적인 구조는 V3와 같다. 대신에 컨트롤러가 ModelView를 반환하지 않고, ViewName만 반환한다.

### 모델 객체 전달
Map<String, Object> model = new HashMap<>();
모델 객체를 프론트 컨트롤러에서 생성해서 넘겨준다. 컨트롤러에서 모델 객체에 값을 담으면 여기에 그대로 담겨있게 된다.

### 뷰의 논리 이름을 직접 반환
컨트롤러가 직접 뷰의 논리 이름을 반환하므로 이 값을 사용해서 실제 물리 뷰를 찾을 수 있다.

### 정리
이번 버전의 컨트롤러는 매우 단순하고 실용적이다.
기존 구조에서 모델을 파라미터로 넘기고, 뷰의 논리 이름을 반환한다는 작은 아이디어를 적용했을 뿐인데, 컨트롤러를 구현하는 개발자 입장에서 보면 이제 군더더기 없는 코드를 작성할 수 있다.
또한 중요한 사실은 여기까지 한번에 온 것이 아니라는 점이다.
프레임워크가 점진적으로 발전하는 과정 속에서 이런 방법도 찾을 수 있었다.

### 프레임 워크나 공통 기능이 수고로워야 사용하는 개발자가 편리해진다.

# /24-04-18
## 유연한 컨트롤러1 - V5
만약 어떤 개발자는 ControllerV3 방식으로 개발하고 싶고, 어떤 개발자는 ControllerV4 방식으로 개발하고 싶다면 어떻게 해야할까?

### 어댑터 패턴
지금까지 우리가 개발한 프론트 컨트롤러는 한가지 방식의 컨트롤러 인터페이스만 사용할 수 있다.
ControllerV3, ControllerV4는 완전히 다른 인터페이스이다. 
따라서 호환이 불가능하다. 
마치 v3는 110v이고, v4는 220v 전기 콘센트 같은 것이다. 
이럴 때 사용하는 것이 바로 어댑터이다.
어댑터 패턴을 사용해서 프론트 컨트롤러가 다양한 방식의 컨트롤러를 처리할 수 있도록 변경해보자.

### V5 구조
- 핸들러 어댑터 : 중간에 어댑터 역할을 하는 어댑터가 추가되었는데 이름이 핸들러 어댑터이다.
  여기서 어댑터 역할을 해주는 덕분에 다양한 종류의 컨트롤러를 호출할 수 있다.
- 핸들러 : 컨트롤러의 이름을 더 넓은 범위인 핸들러로 변경했다.
  그 이유는 이제 어댑터가 있기 때문에 꼭 컨트롤러의 개념 뿐만 아니라 어떠한 것이든 해당하는 종류의 어댑터만 있으면 다 처리할 수 있기 때문이다.

# /24-04-19
### MyHandler Adapter
- boolean supports(Object handler)
  - handler는 컨트롤러를 말한다.
  - 어댑터가 해당 컨트롤러를 처리할 수 있는지 판단하는 메서드다.
- ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler)
  - 어댑터는 실제 컨트롤러를 호출하고, 그 결과로 ModelView를 반환해야 한다.
  - 실제 컨트롤러가 ModelView를 반환하지 못하면, 어댑터가 ModelView를 직접 생성해서라도 반환해야 한다.
  - 이전에는 프론트 컨트롤러가 실제 컨트롤러를 호출했지만 이제는 이 어댑터를 통해서 실제 컨트롤러가 호출된다.


# /24-04-25
## 유연한 컨트롤러2 -V5

### 어댑터 변환
어댑터에서 이 부분이 단순하지만 중요한 부분이다.
어댑터가 호출하는 ControllerV4는 뷰의 이름을 반환한다. 그런데 어댑터는 뷰의 이름이 아니라 ModelView를 만들어서 반환해야 한다.
여기서 어댑터가 꼭 필요한 이유가 나온다.
ControllerV4는 뷰의 이름을 반환했지만, 어댑터는 이것을 ModelView로 만들어서 형식을 맞추어 반환한다. 


# /24-07-15
## 정리
- V1 : 프론트 컨트롤러 도임
  - 기존 구조를 최대한 유지하면서 프론트 컨트롤러 도입
- V2 : View 분류
  - 단순 반복되는 뷰 로직 분리
- V3 : Model 추가
  - 서블릿 종속성 제거
  - 뷰 이름 중복 제거
- V4 : 단순하고 실용적인 컨트롤러
  - V3와 거의 비슷
  - 구현 입장에서 ModelView를 직접 생성해서 반환하지 않도록 편리한 인터페이스 제공
- V5 : 유연한 컨트롤러
  - 어댑터 도임
  - 어댑터를 추가해서 프레임워크를 유연하고 확장성 있게 설계
여기에 애노테이션을 사용해서 컨트롤러를 더 편리하게 발전시킬 수도 있다. 만약 애노테이션을 사용해서 컨트롤러를 편리하게 사용할 수 있게 하려면 어떻게 해야할까?
바로 애노테이션을 지원하는 어댑터를 추가하면 된디!
다형성과 어댑터 덕분에 기존 구조를 유지하면서, 프레임 워크의 기능을 확장할 수 있다.

### 스프링 MVC
여기서 더 발전시키면 좋겠지마느, 스프링 MVC의 핵심 구조를 파악하는데 필요한 부분은 모두 만들어 보았다.
사실은 지금까지 작성한 코드는 스프링 MVC 프레임워크의 핵심 코드의 축약 버전이고, 구조도 거의 같다.

스프링 MVC에는 지금까지 우리가 학습한 내용과 거의 같은 구조를 가지고 있다.

## 스프링 MVC - 구조 이해

### DispatcherServlet 서블릿 등록
- DispatcherServlet도 부모 클래스에서 HttpServlet을 상속받아서 사용하고, 서블릿으로 동작한다.
  - DispatcherServlet -> FrameworkServlet -> HttpServletBean -> HttpServlet
- 스프링 부트는 DispatcherServlet을 서블릿으로 자동으로 등록하면서 모든 경로 (urlPatterns="/")에 대해서 매핑한다.
  - 참고 : 더 자세한 경로가 우선순위가 높다. 그래서 기존에 등록한 서블릿도 함께 동작한다.

### 요청 흐름
- 서블릿이 호출되면 HttpServlet이 제공하는 service()가 호출된다.
- 스프링 MVC는 DispatcherServlet의 부모인 FrameworkServlet에서 service()를 오버라이드 해두었다.
- FrameworkServlet.service()를 시작으로 여러 메서드가 호출되면서 DispatcherServlet.doDispatch()가 호출된다.

지금부터 DispacherServlet의 핵심인 doDispatch() 코드를 분석해보자. 
최대한 간단히 설명하기 위해 예외처리, 인터셉터 기능은 제외했다.

## Spring MVC 구조
1. 핸들러 조회 : 핸들러 매핑을 통해 요청 URL에 매핑된 핸들러(컨트롤러)를 조회한다.
2. 핸들러 어댑터 조회 : 핸들러를 실행할 수 있는 핸들러 어댑터를 조회한다.
3. 핸들러 어댑텉 실행 : 핸들러 어댑터를 실행한다. 
4. 핸들러 실행 : 핸들러 어댑터가 실제 핸들러를 실행한다. (실제 컨트롤러 실행)
5. ModelAndView 반환 : 핸들러 어댑터는 핸들러가 반환하는 정보를 ModelAndView로 변환해서 반환한다.
6. viewResolver호출 : 뷰 리졸버를 찾고 실행한다.
   - JSP의 경우 : InternalResourceViewResolver가 자동 등록되고, 사용된다.
7. view 반환 : 뷰 리졸버는 뷰의 논리 이름을 물리 이름으로 바꾸고, 렌더링 역할을 담당하는 뷰 객체를 반환한다.
   - JSP의 경우 : InternalResourceView(JstlView)를 반환하는데, 내부에 forward()로직이 있다.
8. 뷰 렌더링 : 뷰를 통해서 뷰를 렌더링 한다.

### 인터페이스 살펴보기
- 스프링 MVC의 큰 강점은 DispatcherServlet코드의 변경 없이, 원하는 기능을 변경하거나 확장할 수 있다는 점이다.
  지금까지 설명한 대부분을 확장 가능할 수 있게 인터페이스로 제공한다.
- 이 인터페이스들만 구현해서 DispatcherServlet에 등록하면 여러분만의 컨트롤러를 만들 수도 있다.

### 주요 인터페이스 목록
- 핸드러 매핑 : org.springframework.web.servlet.HandlerMapping
- 핸들러 어댑터 : org.springframeowrk.web.servlet.HandlerAdapter
- 뷰 리졸버 : org.springframeworkl.web.servlet.ViewResolver
- 뷰 : org.springframework.web.servlet.View

## 정리
스프링 MVC는 코드 분량도 매우 많고, 복잡해서 내부 구조를 다 파악하는 것은 쉽지 않다.
사실 해당 기능을 직접 확장하거나 나만의 컨트롤러를 만드는 일은 없으므로 걱정하지 않아도 된다.
왜냐하면 스프링 MVC는 전세계 수 많은 개발자들의 요구사항에 맞추어 기능을 계속 확장해왔고,
그래서 여러분이 웹 애플리케이션을 만들 때 필요로 하는 대부분의 기능이 이미 다 구현되어 있다.
그래도 이렇게 핵심 동작방식을 알아두어야 향후 문제가 발생했을 때 어떤 부분에서 문제가 발생했는지 쉽게 파악하고, 문제를 해결할 수 있다.
그리고 확장 포인트가 필요할 때, 어떤 부분을 확장해야 할지 감을 잡을 수 있다.
실제 다른 컴포넌트를 제공하거나 기능을 확장하는 부분들은 강의를 진행하면서 조금씩 알 수 있다.
지금은 전체적인 구조가 이렇게 되어 있구나 하고 이해하면 된다.

# /24-07-16
## 핸들러 매핑과 핸들러 어댑터
핸들러 매핑과 핸들러 어댑터가 어떤 것들이 어떻게 사용되는지 알아보자.
지금은 전혀 사용하지 않지만, 과거에 주로 사용했던 스프링이 제공하는 간단한 컨트롤러로 핸들러 매핑과 어댑터를 이해해보자.

### Controller 인터페이스

### 참고
Controller 인터페이스는 @Controller 애노테이션과는 전혀 다르다.

#### 이 컨트롤러는 어떻게 호출될 수 있을 까?
이 컨트롤러가 호출되려면 다음 2가지가 필요하다.
- HandlerMapping(핸들러 매핑)
  - 핸들러 매핑에서 이 컨트롤러를 찾을 수 있어야 한다.
  - 예) 스프링 빈의 이름으로 핸들러를 찾을 수 있는 핸들러 매핑이 필요하다.
- HandlerAdapter(핸들러 어댑터)
  - 핸들러 매핑을 통해서 찾은 핸들러를 실행할 수 있는 핸들러 어댑터가 필요하다.
  - 예) Controller인터페이스를 실행할 수 있는 핸들러 어댑터를 찾고 실행해야 한다.
스프링은 이미 필요한 핸들러 매핑과 핸들러 어댑터를 대부분 구현해두었다. 개발자가 직접 핸들러 매핑과 핸들러 어댑터를 만드는 일은 거의 없다.

### 스프링 부트가 자동등록하는 핸들러 매핑과 핸들러 어댑터
#### HndlerMapping
0 = RequestMappingHandlerMapping : 애노테이션 기반의 컨트롤러인 @RequestMapping에서 사용
1 = BeanNameUrlHandlerMapping : 스프링 빈의 이름으로 핸들러를 찾는다.

#### HandlerAdapter
0 = RequestMappingHandlerAdapter : 애노테이션 기반의 컨트롤러인 @RequestMapping에서 사용
1 = HttpRequestHandlerAdapter : HttpRequestHandler 처리
2 = SimpleControllerHandlerAdapter : Controller 인터페이스(애노테이션X, 과거에 사용) 처리

핸들러 매핑도, 핸들러 어댑터도 모두 순서대로 찾고 만약 없으면 다음 순서로 넘어간다.

1. 핸들러 매핑으로 핸들러 조회
   1. HandlerMapping을 순서대로 실행해서, 핸들러를 찾는다.
   2. 이 경우 빈 이름으로 핸들러를 찾아야 하기 때문에 이름 그대로 빈 이름으로 핸들러를 찾아주는 BeanNameUrlHandlerMapping이 실행에 성공하고 핸들러인 OldController를 반환한다.
2. 핸들러 어댑터 조회
   1. HandlerAdapter의 supports()를 순서대로 호출한다.
   2. SimpleControllerHandlerAdapter가 Controller 인터페이스를 지원하므로 대상이 된다.
3. 핸들러 어댑터 실행
   1. 디스패쳐 서블릿이 조회한 SimpleControllerHandlerAdapter를 실행하면서 핸들러 정보도 함께 넘겨준다.
   2. SimpleControllerHandlerAdapter는 핸들러인 OldController를 내부에서 실행하고, 그 결과를 반환한다.

### 정리- OldController, 핸들러매핑, 어댑터
OldController를 실행하면서 사용된 객체는 다음과 같다.
HandlerMapping = BeanNameUrlHandlerMapping
HandlerAdapter = SimpleControllerHandlerAdapter


### HttpRequestHandler
핸들러 매핑과, 어댑터를 더 잘 이해하기 위해 Controller인터페이스가 아닌 다른 핸들러를 알아보자
HttpRequestHandler 핸들러(컨트롤러)는 서블릿과 가장 유사한 형태의 핸들러이다.

1. 핸들러 매핑으로 핸들러 조회
   1. HandlerMapping을 순서대로 실행해서, 핸들러를 찾는다.
   2. 이 경우 빈 이름으로 핸들러를 찾아야 하기 때문에 이름 그대로 빈 이름으로 핸들러를 찾아주는 BeanNameUrlHandlerMapping이 실행에 성공하고 핸들러인 MyHttpRequestHandler를 반환한다.
2. 핸들러 어댑터 조회
   1. HandlerAdapter의 supports()를 순서대로 호출한다.
   2. HttpRequestHandlerAdapter가 HttpRequestHandler 인터페이스를 지원하므로 대상이 된다.
3. 핸들러 어댑터 실행
   1. 디스패처 서블릿이 조회한 HttpRequestHandlerAdapter를 실행하면서 핸들러 정보도 함께 넘겨준다.
   2. HttpRequestHandlerAdapter는 핸들러인 MyHttpRequestHandler를 내부에서 실행하고, 그 결과를 반환한다.

### 정리 - HttpRequestHandler 핸들러 매핑, 어댑터
MyHttpRequestHandler를 실행하면서 사용된 객체는 다음과 같다.
HandlerMapping = BeanNameUrlHandlerMappint
HandlerAdapter = HttpRequestHandlerAdapter

### @RequestMapping
조금 뒤에서 설명하겠지만, 가장 우선순위가 높은 핸들러 매핑과 핸들러 어댑터는 RequestMappingHandlerMapping, RequestMappingHandlerAdapter이다.
@RequestMapping의 앞글자를 따서 만든 이름인데 이것이 지금 스프링에서 주로 사용하는 애노테이션 기반의 컨트롤ㄹ러를 지원하는 매핑과 어댑터이다.
실무에서는 99.9% 이 방식의 컨트롤러를 사용한다.

# /24-07-17
## 뷰 리졸버
### 뷰리졸버 - InternalResourceViewResolver
스프링 부트는 InternalReourceViewResolver라는 뷰 리졸버를 자동으로 등록하는데, 이때
application.properties에 등록한 spring.mvc.view.prefix, spring.mvc.view.suffix 설정 정보를 사용해서 등록한다.

### 뷰 리졸버 동작 방식
스프링 부트가 자동으로 등록하는 뷰 리졸버
1 = BeanNameViewResolver : 빈 이름으로 뷰를 찾아서 반환한다.
2 = InternalResouceViewResolver : JSP를 처리할 수 있는 뷰를 반환한다.

1. 핸들러 어댑터 호출
  핸들러 어댑터를 통해 new-form이라는 논리 뷰 이름을 획득한다.
2. ViewResolver 호출
- new-form이라는 뷰 이름으로 viewResolver를 순서대로 호출한다.
- BeanNameViewResolversms new-form이라는 이름의 스플이 빈으로 등록된 뷰를 찾아야 하는데 없다.
- InternalResouceViewResolver가 호출된다.
3. InternalResourceViewResolver
  이 뷰 리졸버는 InternalResourceView를 반환한다.
4. 뷰 - InternalResourceView
  JSP처럼 포워드 forward()를 호출해서 처리할 수 있는 경우에 사용한다.
5. view.render()
  view.render()가 호출되고 InternalResourceView는 forward()를 사용해서 JSP를 실행한다.

### 참고
InternalResourceViewResolver는 만약 JSTL 라이브러리가 있으면 InternalResourceView를 상속받은 JstlView를 반환한다.
JstlView는 JSTL 태그 사용시 약간의 부가 기능이 추가된다.

### 참고
다른 뷰는 실제 뷰를 랜더링하지만, JSP의 경우 forward()를 통해서 해당 JSP로 이동(실행)해야 렌더링 된다.
JSP를 제외한 나머지 뷰 템플릿들은 forward() 과정 없이 바로 렌더링된다.

### 참고
Thymelear 뷰 템플릿을 사용하면 ThymeleafViewResolver를 등록해야 한다.
최근에는 라이브러리만 추가하면 스프링 부트가 이런 작업도 모두 자동화해준다.


## 스프링 MVC - 시작하기
스프링이 제공하는 컨트롤러는 애노테이션 기반으로 동작해서, 매우 유연하고 실용적이다.
과거에는 자바 언어에 애노테이션이 없기도 했고, 스프링도 처음부터 이런 유연한 컨트롤러를 제공한 것은 아니다.

### @RequestMapping
스프링은 애노테이션을 활용한 매우 유연하고, 실용적인 컨트롤러를 만들었는데 이것이 바로 @RequestMapping 애노테이션을 사용하는 컨트롤러이다.
다들 한번쯤 사용해 보았을 것이다. 여담이지만 과거에는 스프링 프레임워크가 MVC 부분이 약해서 스프링을 사용하더라도 MVC 웹 기술은 스트릿츠 같은 다른 
프레임워크를 사용했었다. 그런데 @RequestMapping 기반의 애노테이션 컨트롤러가 등장하면서, MVC 부분도 스프링의 완승으로 끝이났다.
