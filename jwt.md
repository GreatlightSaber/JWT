## JWT란?
IOS랑 통신을 하게되면서 기존에 웹에서만 할때는 session으로 가능했는데 다른 대처방안을 찾다가
JWT(Json Web Token)을 알게 되었다.

영어표기로는 `JWT` 읽기로는 `JOT`(~발음주의~)라고 읽는다. 

**그럼 대체 JWT란 무엇인가?**

한마디로 쉽게말하면, JSON 형태로 인증토큰을 만들어 통신할때쓰는 인증방식이다.

JWT는 `Header`에 `Authorization` 값을 넣어서 Authorization Server로 보내서 인증을 한다.
![](https://gist.githubusercontent.com/LeoHeo/c9678154b1dadd85add5862b30e969f8/raw/fc142d497f9b267c80c9e14d77ceead6cdb02be7/jwt.png)

그럼 JWT가 어떻게 진행되는지 한번 살펴보자.

Auth0에서 만든 [JWT 사이트](https://jwt.io/introduction/)에 들어가보면 설며이 되어있다.

아까 Header에 값을 넣는다고 했는데 일반적으로 Header에 Json를 넣는거는 많이 불편하다.

그래서 **json을 BASE64로 인코딩하여 하나의 문자열로 변환** 한다음 그 인코딩값을 통해 JSON Web Signature(JWS)를 사용하여 디지털 서명한다. 

JWT는 크게 3개로 나누어서 Encode를 한다.