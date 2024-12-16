# 전주맛배달 고객용 로그인 API

##  HOST 정보
* 도메인 : https://stampang.com
* GQL 도메인 : https://order.stampang.com
* 호출 방식 : POST, GET 둘다 가능
* JWT 인증 방식, GQL 사용함

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


## 4. GQL
>https://order.stampang.com/gql

* JWT 인증 필수

### 4.1 고객 정보 GQL
* Query
<pre>
{
  member {
    pk # 고객 고유번호 (Int)
    phone # 휴대폰번호 (String)
    email # 이메일 (String)
    nick # 닉네임 (String)
  }
}
</pre>
### 4.2 주소록 GQL
* Query
<pre>
{
  memberAddressList {
    edges {
      node {
        pk # 고유번호
        isMain # 대표주소 여부 (Boolean)
        addr1 # 주소
        addr2 # 상세주소
      }
    }
  }
}
</pre>

### 4.3 주소록 추가 GQL
* Query(mutation)
<pre>
mutation addAddress(
  $addr1: String!
  $addr2: String!
  $isMain: Boolean
  $lat: Float
  $lng: Float
  $region: String!
) {
  memberAddressAdd(
    addr1: $addr1 # 주소
    addr2: $addr2 # 상세주소
    isMain: $isMain # 대표주소 여부
    lat: $lat # 위치정보 latitude
    lng: $lng # 위치정보 longitude
    region: $region # 지역 코드
  ) {
    msg
    result
  }
}
</pre>
* Variables
<pre>
{
  "addr1": "서울 금천구 가산디지털2로 53 (한라시그마밸리)",
  "addr2": "5층",
  "lat": 37.47330519112502,
  "lng": 126.88102684601252,
  "isMain": true,
  "region": "1154510100"
}
</pre>
