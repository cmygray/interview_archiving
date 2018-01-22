# Internet browser 에 주소를 입력하고 엔터를 쳤을 때부터 화면이 뜨기까지의 과정은?

## 일단 대답해본다면...?

브라우저에 입력한 주소는 전송 규약을 나타내는 scheme, 메인 도메인과 호스트, 패스, 쿼리 등으로 구성됩니다. 여기서 호스트 URL 은 도메인 주소라고도 하는데 사람이 인식하기 쉬운, 흔히 사용하는 영문 주소를 뜻합니다. 이 주소는 도메인 네임 서버에 대한 요청/응답을 거쳐 IP 주소로 변환되고 비로소 HTML 문서를 요청할 주소를 갖게됩니다.
     정해진 주소로부터 HTML 문서를 요청/응답받기 위해 HTTP 에서 제공하는 GET 메소드가 사용됩니다. 이때 웹 서버에 요청이 무사히 도달한 상태를 "200" 코드를 통해 확인할 수 있습니다. 이어서 웹 서버는 HTML 문서를 문자열 데이터로 변환, 8Kb 단위로 쪼개서 전송함으로써 응답합니다. 전송이 완료되면 브라우저는 문자열 상태인 데이터를 HTML 형식으로 변환하는데, 이를 파싱이라고 합니다. 파싱이 완료되면 HTML 문서를 읽고 `link`, `script` 등의 태그가 존재하는지 확인하며 추가 파일 요청여부를 결정합니다. 일반적인 경우 css 와 javascript 문서는 각각 HTML 문서의 `head`, 그리고 `body` 최하단에서 호출합니다. 파싱이 완료된 HTML 문서는 위에서부터 순차적으로 읽히기 때문에 일반적인 경우라면 추가적인 파일 요청은 css -> javascript 의 순서로 발생합니다.
     각 파일의 전송, 파싱이 끝남에 따라 HTML 은 DOM, CSS 는 CSSOM, JS 는 Syntax Tree 로 변환됩니다. 각 구조는 서로 연동되어 문서객체의 형태, 동작을 정의합니다.

모 회사에서는 5 분 이상 설명할 수 있는 능력을 기본 조건으로 걸어뒀는데, 이를 기준삼는다면 아직 한참 모자라다. 그리고 내용이 정확하지도 않고.

## 무엇을 보충할 것인가

배운 기억은 있지만 정확하지 않아 덧붙이지 못한 내용들

* 네트워크의 각 레이어에서 일어나는 일
* IP 주소만을 가지고 네트워크를 널뛰며 목적지에 도달하는 과정
* LAN, WAN 을 거쳐 이동할 수 있는 원리(맥 주소..?)

배운적은 없는데 공부해서 추가할 내용

* 브라우저 엔진별 차이점
* 최초 HTML 요청 시 연결 경로를 재사용하는 코드가 있었는데..(소켓 처럼)

아는줄 알았는데 몰랐던 것

* HTML 문서 파싱과 추가 소스 요청의 순서, 즉 브라우저 동작의 정확한 절차

## 5 분을 채워랏

https://friendlybit.com/css/rendering-a-web-page-step-by-step/

1. You type an URL into address bar in your preferred browser.
1. The browser parses the URL to find the protocol, host, port, and path.
1. **It forms a HTTP request (that was most likely the protocol)** => this step repeats. see step 15.
1. To reach the host, it first needs to translate the human readable host into an IP number, and it does this by doing a **DNS lookup** on the host
1. Then a **socket** needs to be opened from the user’s computer to that IP number, on the port specified (most often port 80)
1. When a connection is open, the HTTP request is sent to the host
1. The host forwards the request to the server software (most often Apache) configured to listen on the specified port
1. The server inspects the request (most often only the path), and launches the server plugin needed to handle the request (corresponding to the server language you use, PHP, Java, .NET, Python?)
1. The plugin gets access to the full request, and starts to prepare a HTTP response.
1. To construct the response a database is (most likely) accessed. A database search is made, based on parameters in the path (or data) of the request
1. Data from the database, together with other information the plugin decides to add, is combined into a long string of text (probably HTML).
1. The plugin combines that data with some meta data (in the form of HTTP headers), and sends the HTTP response back to the browser.
1. The browser receives the response, and parses the HTML (which with **95% probability is broken**) in the response
1. A DOM tree is built out of the broken HTML
1. New requests are made to the server for each new resource that is found in the HTML source (typically images, style sheets, and JavaScript files). Go back to step 3 and repeat for each resource.
1. Stylesheets are parsed, and the rendering information in each gets attached to the matching node in the DOM tree
1. Javascript is parsed and **executed**, and DOM nodes are moved and style information is updated accordingly
1. The browser renders the page on the screen according to the DOM tree and the style information for each node
1. You see the page on the screen
1. You get annoyed the whole process was too slow.
