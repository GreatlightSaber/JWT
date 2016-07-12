## JWT란?
IOS랑 통신을 하게되면서 기존에 웹에서만 할때는 session으로 가능했는데 다른 대처방안을 찾다가
JWT(Json Web Token)을 알게 되었다.

영어표기로는 `JWT` 읽기로는 `JOT`(~~발음주의~~)라고 읽는다. 

## 왜 JWT 인가?
CORS(Cross-origin resource sharing)문제때문에 주로 쓴다.
쿠키는 발행한 서버에서만 유효하다. site-a.com에서 발행한 쿠키는 site-b.com에서 사용할 수 없다.
토큰은 HTML Body 형태로 전송하기 때문에 다른 도메인에서 API를 호출해야 하는 서비스 구성에 유용하다.

**그럼 대체 JWT란 무엇인가?**

한마디로 쉽게말하면, JSON 형태로 인증토큰을 만들어 통신할때쓰는 인증방식이다.

JWT는 `Header`에 `Authorization` 값을 넣어서 Authorization Server로 보내서 인증을 한다.

그럼 JWT가 어떻게 진행되는지 한번 살펴보자.

Auth0에서 만든 [JWT 사이트](https://jwt.io/introduction/)

아까 Header에 값을 넣는다고 했는데 일반적으로 Header에 Json를 넣는거는 많이 불편하다.

그래서 **json을 BASE64로 인코딩하여 하나의 문자열로 변환** 한다음 그 인코딩값을 통해 JSON Web Signature(JWS)를 사용하여 디지털 서명한다. 

JWT는 마침표(`.`)를 기준으로 크게 3개로 나누어서 Encode를 한다.<br>
1. JSOE 헤더(JSON Object Signing and Encryption) - 사진의 Header<br>
2. JWT Claim Set - 사진의 Payload<br>
3. Signature - 사진의 Verify Signature<br>

![](https://gist.githubusercontent.com/LeoHeo/c9678154b1dadd85add5862b30e969f8/raw/f76b28d3633908efe0ad1777ee333592ee5efc4a/jwt2.png)

위 그림과 같이 3개를 조합해서 `token`을 만든다.

## JSOE 헤더(JSON Object Signing and Encryption)
이 헤더는 어떤식으로 JWT를 해석해야 하는지 명시한 부분이다.<br>
`typ`는 어떤 형식을 사용할건지 적는거고, `alg`는 어떤 알고리즘을 사용할지에 대한 정보이다.<br>
`HS256`은 `HMAC SHA-256`이라는 의미이고, 그 외에도 `HS512`, `RS256(RSA SHA-256)`같은 알고리즘을 사용할수도 있다.<br>

## JWT Claim Set
실제적인 데이터가 들어가는 곳이다.<br>
아까 BASE64로 encode를 했기 때문에 DB 조회없이 바로 verify한다음 사용할 수 있다.

## Signature
앞에 JSOE 헤더랑 JWT Claim Set만으로 encode를 한다면 누구나 다시 이값을 decode할수가 있다.

그렇게 되면 누구나 위변조 할수 있기 때문에 Signature부분이 있다.<br>
여기서는 아까 헤더에서 지정했던 알고리즘으로 인코딩하여 Signature를 생성한다.<br>
이렇게 3개를 합쳐서 Token을 생성하는걸 `JWT`라고 한다.<br>

## djangorestframework-jwt
그럼 django에서는 이 `jwt`를 편하게 쓸 수 있는 방법이 있을까?

찾아보니 [Djangocon 2014](https://www.youtube.com/watch?v=825hodQ61bg)에서 djangorestframework-jwt에 대해서 설명한 영상이 있다.

[djano-rest-framwork-jwt-documentation](http://getblimp.github.io/django-rest-framework-jwt/)에 나와 있는 순서대로 쭉 따라하면 된다.

예제에서는<br>
인증방법으로 `session`, `JWT`, `baseAuth(login, paswword)`이고<br>
인증된 사용자에게만 Permission이 주어진다.

그러고 나서 `urls.py`에 아래와 같이 3개를 적어준다.

```python
from rest_framework_jwt.views import obtain_jwt_token
from rest_framework_jwt.views import refresh_jwt_token
from rest_framework_jwt.views import verify_jwt_token


urlpatterns = patterns(
    '',
    # ...

    url(r'^api-token-auth/', obtain_jwt_token),
    url(r'^api-token-refresh/', refresh_jwt_token),
    url(r'^api-token-verify/', verify_jwt_token),
)
```

`postman`을 사용해서 API 테스트를 할 수가 있다.

`api-token-auth`은 `user_name`, `password`를 보매년 response로 token이 생성된다.<br>
`api-token-refresh`는 `token`이 살아있다는 전제하에 새로운 토근값을 받을 수 있습니다.<br>
`api-token-verify`는 가지고 있는 `token`에대해서 verify합니다.<br>

기본 설정을 바꿀수도 있다.
```python
import os
import datetime


JWT_AUTH = {
    'JWT_ENCODE_HANDLER':
    'rest_framework_jwt.utils.jwt_encode_handler',

    'JWT_DECODE_HANDLER':
    'rest_framework_jwt.utils.jwt_decode_handler',

    'JWT_PAYLOAD_HANDLER':
    'rest_framework_jwt.utils.jwt_payload_handler',

    'JWT_PAYLOAD_GET_USER_ID_HANDLER':
    'rest_framework_jwt.utils.jwt_get_user_id_from_payload_handler',

    'JWT_RESPONSE_PAYLOAD_HANDLER':
    'rest_framework_jwt.utils.jwt_response_payload_handler',

    'JWT_SECRET_KEY': os.environ.get('SECRET_KEY_DJANGO'),
    'JWT_ALGORITHM': 'HS256',
    'JWT_VERIFY': True,
    'JWT_VERIFY_EXPIRATION': True,
    'JWT_LEEWAY': 0,
    'JWT_EXPIRATION_DELTA': datetime.timedelta(seconds=300),
    'JWT_AUDIENCE': None,
    'JWT_ISSUER': None,

    'JWT_ALLOW_REFRESH': True,
    'JWT_REFRESH_EXPIRATION_DELTA': datetime.timedelta(days=1),

    'JWT_AUTH_HEADER_PREFIX': 'JWT',
}
```