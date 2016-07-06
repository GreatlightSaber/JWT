## JWT란?
IOS랑 통신을 하게되면서 기존에 웹에서만 할때는 session으로 가능했는데 다른 대처방안을 찾다가
JWT(Json Web Token)을 알게 되었다.

영어표기로는 `JWT` 읽기로는 `JOT`(~발음주의~)라고 읽는다. 

**그럼 대체 JWT란 무엇인가?**

한마디로 쉽게말하면, JSON 형태로 인증토큰을 만들어 통신할때쓰는 인증방식이다.

JWT는 `Header`에 `Authorization` 값을 넣어서 Authorization Server로 보내서 인증을 한다.

그럼 JWT가 어떻게 진행되는지 한번 살펴보자.

Auth0에서 만든 [JWT 사이트](https://jwt.io/)를 보면 어떤식으로 흘러가는지 알 수 있다.

