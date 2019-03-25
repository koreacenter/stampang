# 스탬프팡 OPEN API
종이 쿠폰을 대신하여, 온라인에서 스탬프(포인트) 적립 기능을 제공하는 서비스입니다!

소상공인들을 위한 서비스! 이용료는 무료입니다!

스탬프팡을 이용해 주시는 가맹점 점주 분들을 위해 OPEN API를 제공합니다.

스탬프팡 적립/차감하기 및 보유 스탬프 수(포인트) 조회, 적립/차감 내역 조회 API를 만들어 보았습니다.

기존에 적립/차감할 수 있는 API를 원하셨던 점주분의 요청에 따라,

고객의 휴대폰 번호는 물론이고, 스탬프팡 고객의 고유번호로도 API를 사용할 수 있도록 만들었습니다.

API 사용에 불편함이 있으시면 스탬프팡으로 문의하세요!

- 스탬프팡 이메일 : help@stampang.com
- 스탬프팡 홈페이지 : https://stampang.com



##  스탬프팡 OPEN API 사용 전 필독 해주세요! 
* 도메인 : https://stampang.com
* 호출 방식 : POST, GET 둘다 가능
* 스탬프팡 개발자에게 인증받은 상점만 이용 가능합니다. (스탬프팡 개발팀에 요청하여 인증키를 받아야 합니다.)
* 하루 호출 횟수 제한이 있습니다.




#  API 종류
| 구분 코드  | 설명 | 호출 주소 |
| :---: | ------------- | ------------- |
| 001  | 스탬프(포인트) 적립 API | /api/open/save |
| 001  | 스탬프(포인트) 차감 API | /api/open/use |
| 002 | 적립(차감) 내역 API | /api/open/history |
| 003 | 보유 스탬프(포인트) 조회 API | /api/open/balance |


# API 설명

## 001. 적립 / 차감 API
    고객의 휴대폰 번호로 스탬프(포인트)를 원하는 개수만큼 적립/차감할 수 있습니다.
    휴대폰 번호 혹은 스탬프팡 고객번호로도 적립/차감 가능합니다.

### 호출 파라메타 정의 (request)

 | 필드명 | 설명 | 비고 | 필수 여부|
 | ------------- | ------------- | ------------- | :---: |
 | id | 상점 고유번호 |  | O |
 | auth_code | 인증키 |  개발팀한테 받은 인증키 |  O |
 | device | 디바이스 정보 | 'openapi' 고정값 | O |
 | type | 적립방식(휴대폰번호 or 스탬프팡 고객번호) | 'phone' : 휴대폰번호, 'no' : 고객번호  (기본값 phone) | X |
 | number | 적립/차감 휴대폰번호(혹은 스탬프팡 고객번호) |  | O |
 | count | 적립/차감 스탬프수(포인트) | 스탬프의 경우 최대 100까지 가능 | O |
 


### 호출 결과 정의 (response)
 
 | 필드명 | 설명 | 비고 |
 | ------------- | ------------- | ------------- |
 | result | 적립/차감 성공 여부 | true, false |
 | msg | 결과 메세지 | 성공시 "ok" , 실패시 실패사유 |
 | total_point | 보유 스탬프(포인트) |  result가 true일 경우만 있음 |


### ruby 호출 예시
```ruby
# ruby 호출 예시 (post방식)

require 'net/http'
url = "https://stampang.com/api/open/save"  #  차감시 "https://stampang.com/api/open/use"

data = Hash.new
data['id'] = '상점 고유번호'
data['auth_code'] = '인증키'
data['device']= 'openapi'
data['type']='phone'
data['number'] = '고객 휴대폰 번호'
data['count'] = 2

uri = URI.parse(url)
request = Net::HTTP::Post.new(uri)
request.set_form_data(data)

req_options = {
	use_ssl: uri.scheme == "https",
}

response = Net::HTTP.start(uri.hostname, uri.port, req_options) do |http|
	http.request(request)
end

if response.code =="200"
	jdata = Json.parse(response.body)  #  JSON 결과
end

```

### python 호출 예시

```python
# python 호출 예시 (get방식)
import requests

url = "https://stampang.com/api/open/save"  #  차감시 "https://stampang.com/api/open/use"

data = Hash.new
data['id'] = '상점 고유번호'
data['auth_code'] = '인증키'
data['device']= 'openapi'
data['type']='phone'
data['number'] = '고객 휴대폰 번호'
data['count'] = 2

res=requests.post(url, data=data)

jdata = res.json()  #  JSON 결과
```


## 002. 적립/차감 내역 조회 API
	고객의 휴대폰 번호로 적립/차감한 내역을 볼 수 있습니다.
	1회 호출에 최대 100개의 내역까지 조회 가능합니다.
	
### 호출 파라메타 정의 (request)

 | 필드명 | 설명 | 비고 | 필수 여부 |
 | ------------- | ------------- | ------------- | :---: |
 | id | 상점 고유번호 |  | O |
 | auth_code | 인증키 |  개발팀한테 받은 인증키 |  O |
 | device | 디바이스 정보 | 'openapi' 고정값 | O |
 | type | 적립방식(휴대폰번호 or 스탬프팡 고객번호) | 'phone' : 휴대폰번호, 'no' : 고객번호  (기본값 phone) | X |
 | number | 적립/차감 휴대폰번호(혹은 스탬프팡 고객번호) |  | O |
 | page |  페이지번호, 1부터 시작 |  | X |
 | count | 한 페이지에 노출되는 데이터 수 (1~100) | 최대 100개까지만 가능  | X |

### 호출 결과 정의 (response)
result가 false 인 경우 list 자체가 없음
<table>
	<tr>
		<td colspan="2">필드명</td>
		<td>설명</td>
		<td>비고</td>
	</tr>
	<tr>
		<td colspan="2">result</td>
		<td>적립 성공 여부</td>
		<td>true, false </td>
	</tr>
	<tr>
		 <td colspan="2">msg</td>
		<td>결과 메세지</td>
		<td>성공시 "ok" , 실패시 실패사유</td>
	</tr>
  <tr>
    <td rowspan="7">list<Br/>(Array)</td>
  </tr>
	<tr>
		<td> id </td>
		<td> 고유번호 </td>
		<td> </td>
  </tr>
	<tr>
		<td> login_id </td>
		<td> 적립/한 상점의 login_id </td>
		<td> </td>
  </tr>
  <tr>
		<td> place </td>
		<td> 지점명 </td>
		<td> 지점명이 빈값일 경우 상점명이 리턴됨 </td>
	</tr>
	<tr>
		<td> type  </td>
		<td> 적립/차감 구분값 </td>
		<td> 0: 적립, 1: 차감 </td>
	</tr>
	<tr>
		<td> content </td>
		<td> 내용 </td>
		<td> "적립","차감","양도" 등 </td>
	</tr>
	<tr>
		<td> date </td>
		<td> 적립/차감한 날짜 </td>
		<td> UTC 포맷 </td>
	</tr>
</table>


```ruby
# ruby 호출 예시 (post방식)

require 'net/http'
url = "https://stampang.com/api/open/history"

data = Hash.new
data['id'] = '상점 고유번호'
data['auth_code'] = '인증키'
data['device']= 'openapi'
data['type']='phone'
data['number'] = '고객 휴대폰 번호'
data['page']=1
data['count'] = 50

uri = URI.parse(url)
request = Net::HTTP::Post.new(uri)
request.set_form_data(data)

req_options = {
	use_ssl: uri.scheme == "https",
}

response = Net::HTTP.start(uri.hostname, uri.port, req_options) do |http|
	http.request(request)
end

if response.code =="200"
	jdata = Json.parse(response.body)  #  JSON 결과
end

```

### python 호출 예시

```python
# python 호출 예시 (get방식)
import requests

url = "https://stampang.com/api/open/history"

data = Hash.new
data['id'] = '상점 고유번호'
data['auth_code'] = '인증키'
data['device']= 'openapi'
data['type']='phone'
data['number'] = '고객 휴대폰 번호'
data['page']=1
data['count'] = 50

res=requests.post(url, data=data)

jdata = res.json()  #  JSON 결과
```


## 003. 보유 스탬프(포인트) 조회
	고객의 휴대폰 번호로 현재 총 보유 스탬프(포인트)를 조회할 수 있습니다.
	
### 호출 파라메타 정의 (request)

 | 필드명 | 설명 | 비고 | 필수 여부|
 | ------------- | ------------- | ------------- | :---: |
 | id | 상점 고유번호 |  | O |
 | auth_code | 인증키 |  개발팀한테 받은 인증키 |  O |
 | device | 디바이스 정보 | 'openapi' 고정값 | O |
 | type | 적립방식(휴대폰번호 or 스탬프팡 고객번호) | 'phone' : 휴대폰번호, 'no' : 고객번호  (기본값 phone) | X |
 | number | 적립/차감 휴대폰번호(혹은 스탬프팡 고객번호) |  | O |

### 호출 결과 정의 (response)
 
 | 필드명 | 설명 | 비고 |
 | ------------- | ------------- | ------------- |
 | result | 적립 성공 여부 | true, false |
 | msg | 결과 메세지 | 성공시 "ok", 실패시 실패 사유 |
 | total_count | 보유 스탬프(포인트) | |

### ruby 호출 예시
```ruby
# ruby 호출 예시 (post방식)

require 'net/http'
url = "https://stampang.com/api/open/balance"

data = Hash.new
data['id'] = '상점 고유번호'
data['auth_code'] = '인증키'
data['device']= 'openapi'
data['type']='phone'
data['number'] = '고객 휴대폰 번호'

uri = URI.parse(url)
request = Net::HTTP::Post.new(uri)
request.set_form_data(data)

req_options = {
	use_ssl: uri.scheme == "https",
}

response = Net::HTTP.start(uri.hostname, uri.port, req_options) do |http|
	http.request(request)
end

if response.code =="200"
	jdata = Json.parse(response.body)  #  JSON 결과
end

```

### python 호출 예시

```python
# python 호출 예시 (get방식)
import requests

url = "https://stampang.com/api/open/balance"

data = Hash.new
data['id'] = '상점 고유번호'
data['auth_code'] = '인증키'
data['device']= 'openapi'
data['type']='phone'
data['number'] = '고객 휴대폰 번호'

res=requests.post(url, data=data)

jdata = res.json()  #  JSON 결과
```

