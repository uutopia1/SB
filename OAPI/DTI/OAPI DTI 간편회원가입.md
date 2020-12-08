[TOC]

# 간편 회원 가입 연동이란?

스마트빌의 Open API 서비스를 사용하기 위해서는 스마트빌 포탈에 회원가입 후 인증코드, 인증토큰, 인증서등록, 요금제변경 등, 연동등록 등 사전절차가 필요합니다.

이런 과정을 고객사가 스마트빌 홈페이지에 접속하거나 스마트빌 담당자를 통하지 않고도 ERP시스템 내에서 링크를 통해서 팝업형태로 회원가입페이지를 호출하여 처리할 수 있도록 도와주는 서비스 입니다.

![img](https://gblobscdn.gitbook.com/assets%2F-MMmhRTm3PcrZFZCUXBE%2F-MMnQsmlkiuTEDUSdyiD%2F-MMnTgVVO1YHbAC6VSSd%2Fimage.png?alt=media&token=c5381df7-0739-4e3d-8628-13f45f83c818)

‌

# 간편 회원 가입 연동을 위한 사전 절차



## **방화벽 오픈**

### **회원가입 정보 전달 (default)**

| **프로토콜** | **출발지 소스**    | **허용 포트** | **목적지 소스**                                              | **메모** |
| ------------ | ------------------ | ------------- | ------------------------------------------------------------ | -------- |
| TCP          | Application Server | 80            | [ht](http://demo.smartbill.co.kr/xMain/mb/shar_transfer/info_inpt01.aspx)tp://demo.smartbill.co.kr/xMain/mb/shar*transfer/info_inpt*01.aspx | 데모     |
| TCP          | Application Server | 80            | http://www.smartbill.co.kr/xMain/mb/shar*transfer/info_*inpt01.aspx | 운영     |



### 회원가입 결과 call back(동기화)을 원할 경우 (option)

| **프로토콜** | **출발지 소스** | **허용 포트** | **목적지 소스**    | **메모** |
| ------------ | --------------- | ------------- | ------------------ | -------- |
| TCP          | 58.181.27.5     | Content       | Application Server | 데모     |
| TCP          | 183.111.188.210 | Content       | Application Server | 운영     |

운영 시 고객사 ERP의 referrer URL과 call back URL 정보를 스마트빌 담당자에게 전달이 필요합니다. 전달 받은 정보로 스마트빌 서버에서도 방화벽 작업을 진행 합니다.

‌

# 연동 프로세스

![img](https://gblobscdn.gitbook.com/assets%2F-MMmhRTm3PcrZFZCUXBE%2F-MMnQsmlkiuTEDUSdyiD%2F-MMnX8hiTW6pwmXIlucH%2Fimage.png?alt=media&token=2e075017-f82e-456a-8725-d9c587771f26)



## REQUEST

### URL

**POST** /xMain/mb/shar*transfer/info*inpt01.aspx **HTTP/1.1**

**Host** **:** demo.smartbill.co.kr

**Content-type :** application/x-www-form-urlencoded; charset=utf-8



### Parameter

| Name           | Type(max)   | Description    | Required |
| -------------- | ----------- | -------------- | -------- |
| pComRegNo      | String(10)  | 사업자번호     | O        |
| pComName       | String(200) | 회사명         | X        |
| pComPresident  | String(100) | 대표자명       | X        |
| pComBizStatus  | String(100) | 업태           | X        |
| pComBizClass   | String(100) | 종목           | X        |
| pZipCode       | String(5)   | 우편번호       | X        |
| pComAddress    | String(300) | 주소           | X        |
| pMIGRATIONFLAG | String(50)  | 회원연계코드   | O        |
| pMemName       | String(100) | 이름           | X        |
| pMemTel        | String(40)  | 연락처(-포함)  | X        |
| pMemHP         | String(40)  | 휴대폰(-포함)  | X        |
| pMemFax        | String(40)  | 팩스(-포함)    | X        |
| pMemEmail      | String(100) | 이메일         | X        |
| pMasterYN      | String(1)   | 기업관리자여부 | X        |
| pID            | String(50)  | 사용자 ID      | X        |

### Sample

```html
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=euc-kr">
</head>
<body>
<form id="frmMain" method="post" target="_blank" action="http://demo.smartbill.co.kr/xMain/mb/shar_transfer/info_inpt01.aspx">
	<input tyep="text" id="pComRegNo" name="pComRegNo"  value="0000000000" /><br>
	<input type="text" id="pMIGRATIONFLAG" name="pMIGRATIONFLAG"  value="TEST11" /><br>
	<input type="text" id="pComName" name="pComName"  value="회사명" /><br>
	<input type="text" id="pComPresident" name="pComPresident"  value="대표자명" /><br>
	<input type="text" id="pComBizStatus" name="pComBizStatus"  value="업태" /><br>
	<input type="text" id="pComBizClass" name="pComBizClass"  value="종목" /><br>
	<input type="text" id="pZipCode" name="pZipCode"  value="69062" /><br>
	<input type="text" id="pComAddress" name="pComAddress"  value="주소" /><br>
	<input type="text" id="pMemName" name="pMemName"  value="이름" /><br>
	<input type="text" id="pMemTel" name="pMemTel"  value="02-1111-1111" /><br>
	<input type="text" id="pMemHP" name="pMemHP"  value="010-1111-1111" /><br>
	<input type="text" id="pMemFax" name="pMemFax"  value="02-2222-2222" /><br>
	<input type="text" id="pMemEmail" name="pMemEmail"  value="test@test.com" /><br>
	<input type="text" id="pMasterYN" name="pMasterYN"  value="Y" /><br>
	<input type="text" id="pID" name="pID"  value="사용자ID" /><br>	
	<button type="submit">전송</button><br>
</form>
</body>
</html>
```



## RESPONSE

**HTTP/1.1** 200 OK

**Contet-Type :** text/html; charset=utf-8



### ① 회사정보 입력, 본인 인증, 약관 동의

데모 : http://demo.smartbill.co.kr/xMain/mb/shar_transfer/info_inpt01.aspx

운영 : http://www.smartbill.co.kr/xMain/mb/shar_transfer/info_inpt01.aspx

![img](https://gblobscdn.gitbook.com/assets%2F-MMmhRTm3PcrZFZCUXBE%2F-MMnQsmlkiuTEDUSdyiD%2F-MMnaFO154gsUM-vGO14%2Fimage.png?alt=media&token=353d3406-4386-4a9e-881a-5dd3ab9f2c3b)



데모 : http://demo.smartbill.co.kr/xMain/mb/shar_transfer/info_inpt02.aspx

운영 : http://www.smartbill.co.kr/xMain/mb/shar_transfer/info_inpt02.aspx

![img](https://gblobscdn.gitbook.com/assets%2F-MMmhRTm3PcrZFZCUXBE%2F-MMnQsmlkiuTEDUSdyiD%2F-MMnfcv4Fau7h-OCAohM%2Fimage.png?alt=media&token=e8cddc5b-2bbd-4b1b-9b43-5f75895d0454)

### **기회원 여부** (Y or N)

> ### Y -> 스마트빌 아이디 로그인
>
> 데모 : http://demo.smartbill.co.kr/xMain/mb/shar_transfer/info_inpt03.aspx
>
> 운영 : http://www.smartbill.co.kr/xMain/mb/shar_transfer/info_inpt03.aspx
>
> ![img](https://gblobscdn.gitbook.com/assets%2F-MMmhRTm3PcrZFZCUXBE%2F-MMnQsmlkiuTEDUSdyiD%2F-MMnh72P8lNs-Fya2epk%2Fimage.png?alt=media&token=877d0eef-71b1-4929-b807-5ad40b2847b7)
>
> 
>
> ### N -> ②신규 회원 가입
>
> 데모 : http://demo.smartbill.co.kr/xMain/mb/shar_transfer/info_inpt03.aspx 
>
> 운영 : http://www.smartbill.co.kr/xMain/mb/shar_transfer/info_inpt03.aspx 
>
> ![img](https://gblobscdn.gitbook.com/assets%2F-MMmhRTm3PcrZFZCUXBE%2F-MMnQsmlkiuTEDUSdyiD%2F-MMnm2J3P-wIzaziqtUS%2Fimage.png?alt=media&token=ced5b8dd-4109-492e-b764-9e4c548d2366)
>



### 동기화 여부 (call back URL 연동) (Y or N)

> ### Y -> 동기화
>
> #### 회원가입 정보, 인증코드, 인증토큰 call back (to call back URL)
>
> **HTTP/1.1** 200 OK
>
> **Contet-Type :** application/json; charset=utf-8
>
> 
>
> #### Parameter
>
> | Name          | Type(max) | Description    | Required |
> | ------------- | --------- | -------------- | -------- |
> | USER_SBID     | String    | 스마트빌 ID    | O        |
> | USER_NAME     | String    | 담당자명       | X        |
> | USER_EMAIL    | String    | 담당이메일주소 | X        |
> | USER_PHONE    | String    | 담당핸드폰번호 | X        |
> | COM_REGNO     | String    | 사업자번호     | O        |
> | COM_NAME      | String    | 회사명         | X        |
> | COM_PRESIDENT | String    | 회대표자명     | X        |
> | COM_ADDRESS   | String    | 회사주소       | X        |
> | regYN         | String    | 관리자여부     | X        |
> | AUTH_CODE     | String    | 인증코드       | O        |
> | AUTH_TOKEN    | String    | 인증토큰       | O        |
>
> 
>
> #### Sample
>
> ```
> {"COM_REGNO":"1111111111", 
>  "AUTH_TOKEN":"cnV0Qnp1VjlrZUJuS0OteFZCanU3MkR2bUhRelFTR0hdNVJ5YnZCS0pZdWhpVURBMFQ0R2hkc0FUMVJFelV6MAo=", 
>  "USER_PHONE":"", 
>  "AUTH_CODE":"13F89DFEK99D4474A67EABE8F7258AA5", 
>  "COM_NAME":"", 
>  "USER_EMAIL":"", 
>  "USER_SBID":"test1", 
>  "COM_ADDRESS":"", 
>  "USER_NAME":"", 
>  "regYn":"", 
>  "COM_PRESIDENT":""}
> ```
>
> 
>
> #### ④공인인증서 등록
>
> 데모 : http://demo.smartbill.co.kr/xMain/mb/mb_join/vendor_join_cplt.aspx
>
> 운영 : http://www.smartbill.co.kr/xMain/mb/mb_join/vendor_join_cplt.aspx
>
> ![img](https://gblobscdn.gitbook.com/assets%2F-MMmhRTm3PcrZFZCUXBE%2F-MMnQsmlkiuTEDUSdyiD%2F-MMniQsVP5MYp3Y61nAL%2Fimage.png?alt=media&token=5bbc4c21-7bac-43d2-bdce-d339c87abd0e)
>
> Enter a caption for this image (optional)
>
> 
>
> 
>
> ### N ->직접발급
>
> #### ③인증코드 생성, 인증토큰 발급
>
> #### ④공인인증서 등록
>
> 데모 : http://demo.smartbill.co.kr/xMain/mb/mb_join/vendor_join_cplt.aspx
>
> 운영 : http://www.smartbill.co.kr/xMain/mb/mb_join/vendor_join_cplt.aspx
>
> ![img](https://gblobscdn.gitbook.com/assets%2F-MMmhRTm3PcrZFZCUXBE%2F-MMnQsmlkiuTEDUSdyiD%2F-MMniQsVP5MYp3Y61nAL%2Fimage.png?alt=media&token=5bbc4c21-7bac-43d2-bdce-d339c87abd0e)
>





‌

> 인증코드, 인증토큰 동기화 과정에서 예기치 못한 오류가 발생하거나 인증서 등록, 갱신이 필요한 경우 아래 방법으로 별도로 진행해야 합니다.‌
>

# 1. 인증코드 생성

데모 : http://demo.smartbill.co.kr/xMain/openapi/get_auth_code.aspx‌

운영 : http://www.smartbill.co.kr/xMain/openapi/get_auth_code.aspx ‌

※ 로그인 필요

![img](https://gblobscdn.gitbook.com/assets%2F-MMmhRTm3PcrZFZCUXBE%2F-MMt3Rej6fQcHONjj2fr%2F-MMtEswK0kYjTDlFpUAr%2Fimage.png?alt=media&token=015e4c89-d8db-448c-910d-fcb8b717be8b)

‌

# 2. 인증토큰 발급

데모 : http://demo.smartbill.co.kr/xMain/openapi/get_auth_token.aspx

운영 : http://www.smartbill.co.kr/xMain/openapi/get_auth_token.aspx

※ 로그인 필요

![img](https://gblobscdn.gitbook.com/assets%2F-MMmhRTm3PcrZFZCUXBE%2F-MMt3Rej6fQcHONjj2fr%2F-MMtFbUFUjuMZOVJoqfn%2Fimage.png?alt=media&token=d38a5c73-b474-4df3-b767-e6156d0e0426)

‌

# 3. 인증서 등록

데모 : http://demo.smartbill.co.kr/xMain/openapi/set_cert_info.aspx‌

운영 : http://www.smartbill.co.kr/xMain/openapi/set_cert_info.aspx‌

※ 로그인 필요

![img](https://gblobscdn.gitbook.com/assets%2F-MMmhRTm3PcrZFZCUXBE%2F-MMt3Rej6fQcHONjj2fr%2F-MMtGODP4ELQ02_6sXst%2Fimage.png?alt=media&token=4b19895d-e9c6-48cd-af77-a04c5e467905)
