[TOC]





# 간편 회원 가입 연동이란?

스마트빌의 Open API 서비스를 사용하기 위해서는 스마트빌 포탈에 회원가입 후 인증코드, 인증토큰, 인증서등록, 요금제변경 등, 연동등록 등 사전절차가 필요합니다.

이런 과정을 고객사가 스마트빌 홈페이지에 접속하거나 스마트빌 담당자를 통하지 않고도 ERP시스템 내에서 링크를 통해서 팝업형태로 회원가입페이지를 호출하여 처리할 수 있도록 도와주는 서비스 입니다.

![img](https://gblobscdn.gitbook.com/assets%2F-MMmhRTm3PcrZFZCUXBE%2F-MMnQsmlkiuTEDUSdyiD%2F-MMnTgVVO1YHbAC6VSSd%2Fimage.png?alt=media&token=c5381df7-0739-4e3d-8628-13f45f83c818)

‌





# 간편 회원 가입 연동을 위한 사전 절차

### **방화벽 오픈**

#### **회원가입 정보 전달 (default)**

| **프로토콜** | **출발지 소스**    | **허용 포트** | **목적지 소스**                                              | **메모** |
| ------------ | ------------------ | ------------- | ------------------------------------------------------------ | -------- |
| TCP          | Application Server | 80            | http://demo.smartbill.co.kr/xMain/mb/shar_transfer/info_inpt01.aspx | 데모     |
| TCP          | Application Server | 80            | http://www.smartbill.co.kr/xMain/mb/shar_transfer/info_inpt01.aspx | 운영     |

#### 회원가입 결과 call back(동기화)을 원할 경우 (option)

| **프로토콜** | **출발지 소스** | **허용 포트** | **목적지 소스**    | **메모** |
| ------------ | --------------- | ------------- | ------------------ | -------- |
| TCP          | 58.181.27.5     | Content       | Application Server | 데모     |
| TCP          | 183.111.188.210 | Content       | Application Server | 운영     |

운영 시 고객사 ERP의 referrer URL과 call back URL 정보를 스마트빌 담당자에게 전달이 필요합니다. 전달 받은 정보로 스마트빌 서버에서도 방화벽 작업을 진행 합니다.

‌





# 연동 프로세스

![image-20201230092746146](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201230092746146.png)



## 1.기본정보 전송

스마트빌 간편회원가입 연동 페이지로 사업자번호, 연계코드, ERP사용자ID 등 정보를 전송합니다.

#### URL

**POST** /xMain/mb/shar*transfer/info*inpt01.aspx **HTTP/1.1**

**Host** **:** demo.smartbill.co.kr

**Content-type :** application/x-www-form-urlencoded; charset=utf-8

#### Parameter

| Name           | Type(max)   | Description    | Required |
| -------------- | ----------- | -------------- | -------- |
| pComRegNo      | String(10)  | 사업자번호     | O        |
| pComName       | String(200) | 회사명         | O        |
| pComPresident  | String(100) | 대표자명       | O        |
| pComBizStatus  | String(100) | 업태           | O        |
| pComBizClass   | String(100) | 종목           | O        |
| pZipCode       | String(5)   | 우편번호       | O        |
| pComAddress    | String(300) | 주소           | O        |
| pMIGRATIONFLAG | String(50)  | 회원연계코드   | O        |
| pMemName       | String(100) | 이름           | O        |
| pMemTel        | String(40)  | 연락처(-포함)  | O        |
| pMemHP         | String(40)  | 휴대폰(-포함)  | X        |
| pMemFax        | String(40)  | 팩스(-포함)    | X        |
| pMemEmail      | String(100) | 이메일         | O        |
| pMasterYN      | String(1)   | 기업관리자여부 | O        |
| pID            | String(50)  | ERP 사용자 ID  | O        |

#### Sample

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



## 2.기본정보 확인, 본인인증, 약관동의

전송한 정보가 자동 입력된 상태로 회사정보 확인 페이지가 호출됩니다. 회사정보를 확인 후 본인인증, 약관동의를 진행합니다.

**HTTP/1.1** 200 OK

**Contet-Type :** text/html; charset=utf-8

데모 : http://demo.smartbill.co.kr/xMain/mb/shar_transfer/info_inpt01.aspx

운영 : http://www.smartbill.co.kr/xMain/mb/shar_transfer/info_inpt01.aspx

![img](https://gblobscdn.gitbook.com/assets%2F-MMmhRTm3PcrZFZCUXBE%2F-MMnQsmlkiuTEDUSdyiD%2F-MMnaFO154gsUM-vGO14%2Fimage.png?alt=media&token=353d3406-4386-4a9e-881a-5dd3ab9f2c3b)

데모 : http://demo.smartbill.co.kr/xMain/mb/shar_transfer/info_inpt02.aspx

운영 : http://www.smartbill.co.kr/xMain/mb/shar_transfer/info_inpt02.aspx

![img](https://gblobscdn.gitbook.com/assets%2F-MMmhRTm3PcrZFZCUXBE%2F-MMnQsmlkiuTEDUSdyiD%2F-MMnfcv4Fau7h-OCAohM%2Fimage.png?alt=media&token=e8cddc5b-2bbd-4b1b-9b43-5f75895d0454)



> ### **기회원 여부** (Y or N)
> ### Y ->

## 3-1.스마트빌 로그인

기존 회원인 경우 별도의 신규 가입 절차 없이 기존 ID로 로그인 후 연동이 가능합니다.

(관리자 ID로 로그인 해야만 요금제가 정상적으로 변경 됩니다.) 

데모 : http://demo.smartbill.co.kr/xMain/mb/shar_transfer/info_inpt03.aspx

운영 : http://www.smartbill.co.kr/xMain/mb/shar_transfer/info_inpt03.aspx

![img](https://gblobscdn.gitbook.com/assets%2F-MMmhRTm3PcrZFZCUXBE%2F-MMnQsmlkiuTEDUSdyiD%2F-MMnh72P8lNs-Fya2epk%2Fimage.png?alt=media&token=877d0eef-71b1-4929-b807-5ad40b2847b7)

> ### N -> 

## 3-2.스마트빌 회원 가입

신규 회원인 경우 회원가입을 진행합니다. 전송한 정보가 자동으로 입력됩니다.

데모 : http://demo.smartbill.co.kr/xMain/mb/shar_transfer/info_inpt03.aspx 

운영 : http://www.smartbill.co.kr/xMain/mb/shar_transfer/info_inpt03.aspx 

![img](https://gblobscdn.gitbook.com/assets%2F-MMmhRTm3PcrZFZCUXBE%2F-MMnQsmlkiuTEDUSdyiD%2F-MMnm2J3P-wIzaziqtUS%2Fimage.png?alt=media&token=ced5b8dd-4109-492e-b764-9e4c548d2366)



> ### 동기화 여부 (Y or N)
> ### Y -> 

## 4-1.인증코드생성, 인증토큰발급 call back

동기화 할 경우 회원가입이 완료되면 미리 정의한 고객사의 call back page로 정보가 전송됩니다.

고객사는 정보를 수신받을 callback page를 미리 개발하여 리스너 역할로 동작하고 있어야 하고, 전송받은 정보는 별도로 관리가 필요합니다.

**HTTP/1.1** 200 OK

**Contet-Type :** application/json; charset=utf-8

#### Parameter

| Name       | Type(max) | Description|
| ---------- | --------- | ---------------- |
| pID        | String    | ERP 사용자ID|
| SBID       | String    | 스마트빌 ID|
| Email      | String    | 담당자이메일주소|
| COM_REGNO  | String    | 사업자번호|
| regYN      | String    | 동기화여부|
| AUTH_CODE  | String    | 인증코드|
| AUTH_TOKEN | String    | 인증토큰|

#### Sample

```
{
"COM_REGNO":"1111111111",
"AUTH_TOKEN":"cnV0Qnp1VjlrZUJuS0OteFZCanU3MkR2bUhRelFTR0hdNVJ5YnZCS0pZdWhpVURBMFQ0R2hkc0FUMVJFelV6MAo=",
"AUTH_CODE":"13F89DFEK99D4474A67EABE8F7258AA5",
"Email":"test@test.com",
"pID":"test",
"SBID":"test1",
"regYn":"Y"
}
```



## 5.공인인증서 등록

callback 후 인증서를 등록하는 페이지가 호출됩니다.

**HTTP/1.1** 200 OK

**Contet-Type :** application/json; charset=utf-8

데모 : http://demo.smartbill.co.kr/xMain/mb/shar_transfer/info_inpt04.aspx

운영 : http://www.smartbill.co.kr/xMain/mb/shar_transfer/info_inpt04.aspx

![image-20201230084249171](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201230084249171.png)



> ### N ->

## 4-2.인증코드 생성, 인증토큰 발급

## 5.공인인증서 등록

동기화하지 않을 경우 인증코드생성, 인증토큰발급, 공인인증서등록이 가능한 페이지가 호출됩니다.

해당 페이지에서 정상적으로 처리하지 못한 경우 추가 프로세스의 진행이 필요합니다.

데모 : http://demo.smartbill.co.kr/xMain/mb/mb_join/vendor_join_cplt.aspx

운영 : http://www.smartbill.co.kr/xMain/mb/mb_join/vendor_join_cplt.aspx

![img](https://gblobscdn.gitbook.com/assets%2F-MMmhRTm3PcrZFZCUXBE%2F-MMnQsmlkiuTEDUSdyiD%2F-MMniQsVP5MYp3Y61nAL%2Fimage.png?alt=media&token=5bbc4c21-7bac-43d2-bdce-d339c87abd0e)









# ‌추가 프로세스

인증코드, 인증토큰 동기화 과정에서 예기치 못한 오류가 발생하거나 등록 페이지에서 정상적으로 처리하지 못한경우, 혹은 인증서 등록, 갱신이 필요한 경우 스마트빌 홈페이지에 로그인 후 별도로 페이지를 호출하여 진행해야 합니다.



## 1.인증코드 생성

데모 : http://demo.smartbill.co.kr/xMain/openapi/get_auth_code.aspx‌

운영 : http://www.smartbill.co.kr/xMain/openapi/get_auth_code.aspx ‌

※ 로그인 필요

![img](https://gblobscdn.gitbook.com/assets%2F-MMmhRTm3PcrZFZCUXBE%2F-MMt3Rej6fQcHONjj2fr%2F-MMtEswK0kYjTDlFpUAr%2Fimage.png?alt=media&token=015e4c89-d8db-448c-910d-fcb8b717be8b)

‌

## 2.인증토큰 발급

데모 : http://demo.smartbill.co.kr/xMain/openapi/get_auth_token.aspx

운영 : http://www.smartbill.co.kr/xMain/openapi/get_auth_token.aspx

※ 로그인 필요

![img](https://gblobscdn.gitbook.com/assets%2F-MMmhRTm3PcrZFZCUXBE%2F-MMt3Rej6fQcHONjj2fr%2F-MMtFbUFUjuMZOVJoqfn%2Fimage.png?alt=media&token=d38a5c73-b474-4df3-b767-e6156d0e0426)

‌

## 3.인증서 등록

데모 : http://demo.smartbill.co.kr/xMain/openapi/set_cert_info.aspx‌

운영 : http://www.smartbill.co.kr/xMain/openapi/set_cert_info.aspx‌

※ 로그인 필요

![img](https://gblobscdn.gitbook.com/assets%2F-MMmhRTm3PcrZFZCUXBE%2F-MMt3Rej6fQcHONjj2fr%2F-MMtGODP4ELQ02_6sXst%2Fimage.png?alt=media&token=4b19895d-e9c6-48cd-af77-a04c5e467905)
