> [JWT 대충 쓰면 님들 코딩인생 끝남](https://www.youtube.com/watch?v=XXseiON9CV0) 영상을 보고 정리한 내용입니다

인터넷에 jwt를 이용해서 회원 기능을 구현하는 강의가 많은데
아무생각없이 강의를 따라쳐서 구현하면 코딩인생이 끝날 수 있다
왜냐하면 JWT를 그냥 쓰면 보안상의 허점이 많기때문


## 세션방식 vs jwt를 이용한 토큰방식 

두가지 방식 모두 동작은 유사하다

### 회원 인증 기능의 기본적인 동작방식

- 로그인한 사람만 동영상을 볼 수있는 사이트가 있다고 하자
- 유저가 로그인하면 서버가 입장권을 발급해준다
- 사이트에서 뭐 할 때마다 입장권을 제시하게 만든다
- 입장권에 문제가 없으면 유저에게 동영상을 볼 수 있다
- 세션방식과 jwt 방식은 입장권에 써있는 정보만 좀 다를뿐이다
<img width="447" alt="image" src="https://user-images.githubusercontent.com/37561621/210347972-f4eee380-d623-411e-be38-3583b4e8f83d.png">

- 세션방식은 입장권에 발급번호만 적혀있다고 보면 된다
- 입장권을 제시했을때 서버가 메모리나 db에 따로 만들어 둔 입장권발급목록(세션스토어)을 확인해서 이 입장권을 발급했었는지 조회를 해서 문제가 있는지 확인한다.

<img width="438" alt="image" src="https://user-images.githubusercontent.com/37561621/210348025-912e7be1-f863-4c8a-9e7b-88077c3bf6a3.png">

- jwt방식에는 입장권에 써있는 정보들이 많다(유저들의 이메일, 이름, 입장권의 발급일, 유효기간 등)
- 서버는 입장권만 보고 유효기간등이 문제가 있는지 확인한다

<img width="463" alt="image" src="https://user-images.githubusercontent.com/37561621/210348528-a82809d7-3f49-452b-b5bb-7ba1310c0097.png">

- 세션방식은 회원이 입장권을 제시할 때마다 입장권발급목록을 조회해봐야하기때문에 동시에 접속하는 회원이 많으면 힘들어진다.
- jwt의 장점은 stateless 하다는 것 입장권 자체만 확인하기때문에 회원이 매우 많아질수록 서버에 부담이 덜 하다

![image](https://user-images.githubusercontent.com/37561621/210349308-b7df7e41-e595-4b77-a211-7bd722efcc02.png)

- jwt는 HEADER(기본셋팅), PAYLOAD(입장권에 적을 정보), VERIFY SIGNATURE(시크릿키) => base64로 문자압축 (짧은 문자로 인코딩) 한것
- 기존 값이 바뀌면 압축된 문자열도 달라지기때문에 위조 여부도 쉽게 알 수 있다


## jwt 허점들

<img width="471" alt="image" src="https://user-images.githubusercontent.com/37561621/210349257-003a148f-8f01-4afe-81c0-fc49526e639b.png">

1. alg(알고리즘) 값을 none으로 설정해서 jwt를 만들면 안됨
  - alg를 none으로 만든 jwt 입장권을 서버로 보내는 해커가 있을 수 있음
  - none으로 만든 jwt를 거절하는 기능이 있는지 확인해아함
  - 최신 라이브러리들은 걱정하지 않아도 된다고 함
2. jwt는 디코딩이 쉬움
  - 민감한 유저정보를 넣으면 안된다
  - 최소한의 정보만 넣기
3. 시크릿키를 대충 적으면 안된다
  - 매우 짧고 간단한 문자열 X
  - 유명한 강의나 블로그에서 사용한 시크릿키 사용 X
  - 대충 적으면 `brute force attack` 에 당할 수 있음
  - 시크릿키가 털리면 jwt를 맘대로 발행할 수 있음
  -> 키를 매우 길게 적고 공유하지 않기/ 생성용키,검증용키 2개 사용 (라이브리러에서 지원되는지 확인해보기)

<img width="449" alt="image" src="https://user-images.githubusercontent.com/37561621/210350808-2253fa22-74e3-43ec-8c60-40aeef0cccfb.png">

4. jwt를 탈취할 수 있음
  - 이건 잃어버린사람 잘못이긴 함
  - 신용카드를 잃어버렸을때 정지시키는것 처럼 jwt입장권을 정지시킬 수 있게는 해줘야함
  - 구조상 입장권을 회수하거나 사용정지시키기 어려움
  - 한번 입장권을 훔치면 유효기간이 지나기전까지 쓸 수 있음
  솔루션
  1. `HttpOnly cookie` 를 사용하면 1차적으로 훔치기 어려움
  2. 입장권 블랙리스트 같은걸 만들어서 제시할때마다 검사하는 방법도 있지만 이 방법은 jwt를 쓰는 장점이 없어지고 세션방식이랑 똑거ㅏㅌ아짐 
  3. 입장권 유효기간을 짧게한다
     - `refresh token`을 발급해줘야 함
     - 근데 `refresh token`도 탈취당하는 경우가 있음
     - `refresh token rotation` 이라는 방법을 쓰는게 안전하다(refresh token을 언제나 1회용으로 사용)
     - 라이브러리를 찾아보면 있다
   <img width="442" alt="image" src="https://user-images.githubusercontent.com/37561621/210351263-7d8a5303-e87b-46dd-a3c0-bee27dba079f.png">
   <img width="469" alt="image" src="https://user-images.githubusercontent.com/37561621/210351692-398a5508-1ad5-4562-84b8-814dd76d2948.png">

<img width="448" alt="image" src="https://user-images.githubusercontent.com/37561621/210351787-2c197126-04e5-4a0b-879e-b5e82398de73.png">

구글에 검색하면 나오는 `passport-jwt`는 보안기능이 딱히 없다고 한다


## 결론

- 대부분의 서비스는 세션 방식으로 하는게 간단하다. 별일 없으면 역사와 전통의 세션 방식이 좋다.
- 회원이 1억명이라 입장권 조회가 매우 힘이 드는 경우나 마이크로 서비스가 많다 jwt가 훨씬 더 나을 수 있다.
- 보안이 중요한 사이트는 jwt를 대충 쓰는 곳은 없을 것이다. 이것 저것 보안장치가 많거나 세션방식과 비슷하게 운영하고 있을 것이다
- 아니면 카카오계정로그인이나 auth0 + okta , 파이어베이스 인증 등 다른업체의 인증서비스를 쓴다
- jwt 쓰려면 이런 점들을 한 번 생각해보고 쓰자

<img width="448" alt="image" src="https://user-images.githubusercontent.com/37561621/210352355-ca9bd456-81de-40b0-a930-192b05ffcafc.png">
