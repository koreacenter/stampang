# 전주맛배달 고객용 로그인 API

##  HOST 정보
* 도메인 : https://stampang.com
* 호출 방식 : POST, GET 둘다 가능
* JWT 인증 방식 사용함.

## 1. 이메일 로그인(JWT 인증키 발급)
>/v2/jauth/email/auth

[REQ]
| 필드    | 필수여부 | 필드타입 |  설명         | 
| ------- | -------- | -------- | ------------- |
| id      | Y        | String   | 계정 아이디   |
| pw      | Y        | String   | 계정 패스워드 |

[RES]
| 필드    | 필수여부 | 필드타입 |  설명   | 
| ------- | ------- | -------- | -------|
| result  | Y | boolean | 성공 여부|
| msg     | Y | String | 결과 메세지 |
| token | Y | String | jwt 인증 토큰키 |


## 2. 휴대폰 로그인
### 2.1 휴대폰 인증번호 발송
>/v2/api/member/phone/send

[REQ]
| 필드    | 필수여부 | 필드타입   |   설명    | 
| ------- | ------- | ---------- |  ------- |
| app | Y |  String    | "JEONJU" 고정값 (앱 서비스 종류) |
| phone | Y |  String    |  휴대폰번호 |

[RES]
| 필드    | 필수여부 |  필드타입    |   설명    | 
| ------- | ------- | -------- | ------- |
| result | Y |  boolean    | 성공 여부 |
| msg | Y |  String  | 결과 메세지 |

### 2.2 휴대폰 인증번호 확인(JWT 인증키 발급)
>/v2/jauth/phone/auth

[REQ]
| 필드    | 필수여부 | 필드타입   |   설명    | 
| ------- | ------- | ---------- |  ------- |
| phone | Y |  String    |  휴대폰번호 |
| auth | Y |  String    | 수신받은 인증번호 4자리 |

[RES]
| 필드    | 필수여부 |  필드타입    |   설명    | 
| ------- | ------- | -------- | ------- |
| result | Y | boolean | 성공 여부 |
| msg | Y | String | 결과 메세지 |
| token | Y | String | jwt 인증 토큰키 |
| email | Y | String | 고객 이메일 주소 |

## 3. JWT token 갱신
>/v2/jauth/refresh

[REQ]
<pre>
[Header]
Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
</pre>

[RES]
| 필드    | 필수여부 |  필드타입    |   설명    | 
| ------- | ------- | -------- | ------- |
| result | Y | boolean | 성공 여부 |
| msg | Y | String | 결과 메세지 |
| token | Y | String | jwt 인증 토큰키 |