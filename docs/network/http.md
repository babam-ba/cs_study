---
sidebar_position: 1
---

# HTTP 프로토콜은 무엇인가요?

`HTTP(Hypertext Transfer Protocol)`는 인터넷 상에서 데이터를 주고 받기 위한 서버/클라이언트 모델을 따르는 통신 프로토콜이다. 애플리케이션 레벨의 프로토콜로 TCP/IP위에서 작동하며, 웹서버는 보통 표준포트인 80번 포트로 서비스한다.

HTTP는 어떤 종류의 데이터든 전송할 수 있다. HTML문서, 이미지, 동영상, 오디오, 텍스트 문서 등

## HTTP Request & Response

HTTP 프로토콜로 데이터를 주고받기 위해서는 아래와 같이 요청(Request)을 보내고 응답(Response)을 받아야 한다.

클라이언트는 요청(Request)을 보내느 쪽을 의미하며, 일반적으로 웹 관점에서는 브라우저를 의미한다.
서버는 요청을 받고 그에 대한 응답(Response)을 보내준다.

## HTTP 특징

HTTP 프로토콜은 상태가 없는(stateless) 프로토콜이다. 여기서 상태가 없다라는 말은 데이터를 주고 받기 위한 각각의 데이터 요청이 서로 독립적으로 관리가 된다는 말이다. 좀 더 쉽게 말해서 이전 데이터 요청과 다음 데이터 요청이 서로 관련이 없다.

## HTTP 요청 메서드

### 주요 메서드

- **GET**: 존재하는 자원에 대한 **요청**
- **POST**: 새로운 자원을 **생성**
- **PUT**: 존재하는 자원에 대한 **변경**
- **DELETE**: 존재하는 자원에 대한 **삭제**
- **PATCH**: 존재하는 자원의 부분을 **변경**
  - 변경한다는 점에서 PUT 메서드와 비슷하지만, PATCH는 전체 필드 중 교체가 필요한 부분만 교체하도록 한다.

**PUT vs PATCH**

- 만약, PUT으로 전달받은 payload가 기존 정보를 대체하도록 구현했다면, payload 정보가 불완전한 상태로 전송된다면 일부 entity의 field값들은 null로 변경될 수 있는 가능성이 있다.
- PATCH는 부분 수정할 데이터만 payload로 보내기 때문에 body를 받는 DTO를 별도로 만들어 주어야 한다.

예를 들어 다음과 같이 회원 리소스가 있을 경우,

```json
{
  "name": "주꾸미",
  "age": 22
}
```

`PUT`으로 아래와 같이 데이터를 보내면

```json
{
  "name": "동꾸미",
  "age": 29
}
```

다음과 같이 변한다.

```json
{
  "name": "동꾸미",
  "age": 29
}
```

만약 나이를 빼고 이름만 보낼 경우

```json
{
  "name": "동꾸미"
}
```

age가 null로 변한다.

```json
{
  "name": "동꾸미",
  "age": null
}
```

다시 다음과 같은 리소스가 있다.

```json
{
  "name": "주꾸미",
  "age": 22
}
```

이번엔 PATCH로 이름만 보내면,

```json
{
  "name": "동꾸미"
}
```

아래와 같이 변한다.

```json
{
  "name": "동꾸미",
  "age": 22
}
```

여기서 요청에 포함되어 있는 부분만 변경이 된다는 차이점이 있다.

물론 개발자가 구현하기 나름이지만 PUT과 PATCH의 각자 의도가 있다는 것을 알아두면 좋겠다.

### 기타 메서드

- **OPTION**: 예비 요청(Preflight)에 사용되는 메서드
  - 예비 요청: 본 요청을 하기 전에 안전한지 미리 검사하는 것
  - CORS 정책을 검사하기 위한 요청
- **HEAD**: GET과 동일하지만 서버에서 Body를 Return 하지 않음 (상태 줄과 헤더만 반환)
  - Resource를 받지 않고 오직 찾기만 원할때 사용 (응답의 상태 코드만 확인할때)
  - 서버의 응답 헤더를 봄으로써 Resource가 수정 되었는지 확인

## 참고

[https://joshua1988.github.io/web-development/http-part1/](https://joshua1988.github.io/web-development/http-part1/)
[https://mangkyu.tistory.com/98](https://mangkyu.tistory.com/98)
[https://www.digicert.com/kr/what-is-ssl-tls-and-https](https://www.digicert.com/kr/what-is-ssl-tls-and-https)
[https://tecoble.techcourse.co.kr/post/2020-08-17-put-vs-patch/](https://tecoble.techcourse.co.kr/post/2020-08-17-put-vs-patch/)
[https://programmer93.tistory.com/39](https://programmer93.tistory.com/39)
