---
layout: post
title: GET과 POST의 차이
date: '2017-08-02 02:11'
tags:
- HTTP METHOD
- GET
- POST
categories:
- Web

---

# HTTP

HTTP는 웹상에서 클라이언트와 서버 간에 요청/응답으로 정보를 주고 받을 수 있는 프로토콜입니다. <br/>
웹브라우저(클라이언트)가 HTTP 프로토콜을 통해 서버에게 웹페이지 또는 이미지를 요청하면 서버는 사용자에게 응답하기 위해 필요한 정보를 전달하게 됩니다.

HTTP에서 서버에게 요청하기 위한 메소드 중에서 가장 많이 쓰이는 GET과 POST에 대해서 각각의 특징과 차이점은 무엇인지 알아보겠습니다.
<br/><br/>


## GET
GET은 URL의 끝에 `?`가 붙고 서버로 요청하고자 하는 파라미터가 이름과 값으로 쌍을 이루어 붙게 됩니다. 파라미터가 여러 개일 경우에는 `&`로 구분합니다. <br/>
GET을 사용할 경우 다음과 같이 URL이 나타나게 됩니다.

> www.tseturl.com/get_test?name1=value1&name2=value2

GET의 특징은 다음과 같습니다.
* URL에 요청 파라미터를 붙여서 전송.
* URL로 파라미터를 전송하기 때문에 대용량 데이터를 전송하기 힘듦.
* 요청 파라미터를 사용자가 쉽게 눈으로 확인할 수 있음.

HTTP/1.1 스펙인 [RFC2616의 Section9.3](https://tools.ietf.org/html/rfc2616#section-9.3)의 GET메소드 설명에 따르면
GET은 **정보를 조회하기 위한 메소드**라고 되어있습니다. 정보 조회를 위한 메소드이기 때문에 GET을 사용하여 서버로 요청하여 응답을 받게 되면, 브라우저에서 해당 요청에 대한 응답을 캐시 하므로 사용자의 불필요한 네트워크 이용을 줄여서 빠르게 조회할 수 있게 해줍니다.
<br/><br/>

## POST
POST는 **서버로 데이터를 전송하기 위해 설계**되었기 때문에 GET과 달리 파라미터가 URL로 넘어가지 않고 HTTP 패킷의 Body에 담아서 파라미터를 전송합니다. Body에 담아서 서버에게 요청하므로 전송하는 길이에 제한 없이 대용량 데이터를 전송하는데 적합합니다. <br/>

POST로 요청할 때, Request header의 Content-Type에 해당 데이터 타입이 표현되며, 전송하고자 하는 데이터 타입을 적어주어야 합니다. 타입을 적어주지 않는다면 서버에서 내용이나 URI의 이름의 확장명등으로 타입을 유추하거나 알 수 없는 경우에는 `application/octet-stream`로 처리합니다. <br/>

데이터가 Body로 전송되기 때문에 GET보다 보안적인 면에서 안전하다고 할 수 있으나,
POST도 Fiddler와 같은 툴로 확인이 가능하기 때문에 반드시 암호화하여 전송하여야 합니다. <br/><br/>


## GET과 POST의 차이
GET은 Idempotent, POST는 Non-idempotent하게 설계되었습니다. <br/>
Idempotent(멱등)은 수학적 개념으로 다음과 같이 나타낼 수 있습니다.

> 수학이나 전산학에서 연산의 한 성질을 나타내는 것으로, 연산을 여러 번 적용하더라도 결과가 달라지지 않는 성질

즉, 멱등이라는 것은 **여러 번 연산을 수행하더라도 동일한 결과**가 나타나야 합니다. <br/>

GET이 Idempotent하도록 설계되었다는 것은 GET으로 **서버에게 여러 번 요청을 하더라도 동일한 응답이 돌아와야 한다는 것**을 의미합니다. <br/>
반대로 POST는 Non-idempotent하기 때문에 서버에게 여러 번 요청을 한다면 응답이 항상 동일하다고 볼 수 없습니다.

이에 따라 GET은 설계원칙에 따라 서버의 데이터나 상태를 변경시키지 않아야 Idempotent하기 때문에 **주로 조회를 할 때에 사용**됩니다. 웹페이지를 열어보거나 게시글을 읽을 때 등 조회를 하는 행위는 GET으로 요청하게 됩니다. <br/>
조회로 사용되어야 하는 또 다른 이유는 웹페이지를 조회할 때, 원하는 페이지로 바로 이동하거나 이동시키기 위해서는 해당 링크 정보가 필요한데 POST의 경우에는 파라미터가 HTTP패킷의 Body에 있기 때문에 링크 정보를 가져올 수 없습니다. <br/>
GET은 URL에 파라미터를 가지고 있기 때문에 링크를 걸 때, URL에 해당 파라미터를 붙여준다면 추가적인 정보를 붙여 더 상세한 링크를 걸 수가 있습니다. <br/>

POST는 서버의 상태나 데이터를 변경시킬 때 사용됩니다. 게시글을 쓰면 서버에 게시글이 저장이 되고, 게시글을 삭제하면 해당 데이터가 없어지는 등 POST로 요청을 하게 되면 서버의 무언가는 변경되도록 사용됩니다.

GET과 POST는 이처럼 큰 차이가 있기 때문에 설계원칙에 따라 적절한 위치에서 사용되어야 문제가 발생하지 않을 것입니다.


--------------------------------
<br/>

### 참고문서
* [RFC2616 - HTTP/1.1](https://tools.ietf.org/html/rfc2616)
* [위키백과 - 멱등법칙](https://ko.wikipedia.org/wiki/%EB%A9%B1%EB%93%B1%EB%B2%95%EC%B9%99)