[TOC]

# 인증서 등록 프로세스

인증서 등록 프로세스는 두 단계로 구성됩니다.

1. 인증서 OID 검증
2. 인증서 등록

OID검증 후 정상적인 경우 인증서 등록을 진행합니다.



# 1.인증서 OID 검증

인증서의 유효성 여부를 판단하는 기능으로 스마트빌 API서버와 통신이 가능해야 합니다. (연동등록 필요)

|                        | domain                         | ip              |
| ---------------------- | ------------------------------ | --------------- |
| 스마트빌 API서버(데모) | http://demoapi.smartbill.co.kr | 58.181.27.25    |
| 스마트빌 API서버(운영) | http://api.smartbill.co.kr     | 183.111.188.142 |

중계서버의 특정 URL로 인증서 파일을 포함한 정보를 요청하여 유효성 여부를 응답 받을 수 있습니다.



### REQUEST

#### URL

 http://중계서버IP:PORT/dti/api/vi/certificate/verifyCertOID/사업자번호

**Method :** POST

**Content-type :** multipart/form-data; charset=utf-8



#### Parameter

| Name            | Type | Description                      | Required |
| --------------- | ---- | -------------------------------- | -------- |
| corpRegNo       | text | 사업자번호                       | O        |
| password        | text | 인증서 비밀번호                  | X        |
| signCertPublic  | file | 인증서 공개키 파일(signCert.der) | O        |
| signCertPrivate | file | 인증서 개인키 파일(signPri.key)  | X        |



#### Sample

```html
<meta charset="utf-8">
<form method="post" enctype="multipart/form-data" action="http://49.50.165.2:30000/dti/api/v1/certificate/verifyCertOID/1111111119">
사업자번호 : <input type="text" name="corpRegNo" value="1111111119"><br>
인증서 비밀번호 : <input type="password" name="password" value="signgate1!"><br>
인증서 공개키 파일 : <input type="file" name="signCertPublic"><br>
인증서 개인키 파일 : <input type="file" name="signCertPrivate"><br>
<input type="submit" value="검증">
</form>
```



### RESPONSE

**HTTP** 200 OK

**Contet-Type :** application/json; charset=utf-8



#### Parameter

| Name          | Type(max)    | Description                   |
| ------------- | ------------ | ----------------------------- |
| MessageId     | String(36)   | 요청 한 트랜잭션 식별자       |
| Signal        | Stirng(30)   | 고정값 : ALLOW_OID            |
| ResponseTime  | String(14)   | 응답시간 (YYYYMMDDHH24MISS)   |
| ResultCode    | String(5)    | 처리 결과 코드 (성공 : 30000) |
| ResultMessage | String(2000) | 처리 결과 상세 내역           |
| ResultDataSet | DataSet      | 처리 결과 데이터              |



#### Sample

```
성공
{"MessageId":"ff42c772-9225-4996-9318-1d3257fd11e3",
"Signal":"ALLOW_OID",
"ConversationId":null,
"ResponseTime":"20201217095400",
"ResultCode":"30000",
"ResultMessage":"정상적으로 처리되었습니다.",
"ResultDataSet":{"Table":[{"OID":"1.2.410.200004.5.2.1.1","CA":"한국정보인증","PURPOSE":"법인 범용","EXPIRE_DATE":"","ALLOW":"Y"}]}}

실패(허용하지 않는 인증서)
{"MessageId":"127f5deb-31f8-4e1f-999d-9d33c8768fc2",
"Signal":"ALLOW_OID",
"ConversationId":null,
"ResponseTime":"20201217102010",
"ResultCode":"30000",
"ResultMessage":"정상적으로 처리되었습니다.",
"ResultDataSet":{"Table":[{"OID":"1.2.410.200004.5.2.1.2","CA":"","PURPOSE":"","EXPIRE_DATE":"","ALLOW":"N"}]}}

실패(사업자번호 오류)
{"MessageId":"cc3cb413-9d83-4909-ae6b-fc6c26c80138","Signal":"ALLOW_OID","ConversationId":null,"ResponseTime":"20201217101655","ResultCode":"60101","ResultMessage":"인증토큰(REQUEST_HEADER.AUTH_TICKET) 복호화에 실패했습니다.","ResultDataSet":{}}
```

‌

# 2.인증서 등록

인증서를 DB에 저장하는 기능으로 등록 시 사업자번호, 인증서유효성, 비밀번호, 유효기간 등을 확인합니다. 

중계서버의 특정 URL로 인증서 파일을 포함한 정보를 요청하여 처리결과를 응답받을 수 있습니다. 정상적으로 처리될 경우 중계DB 인증서테이블(SBMS_CERTIFICATE) 인증서가 저장 됩니다.



### REQUEST

#### URL

http://중계서버IP:PORT/dti/api/vi/certificate/사업자번호

**Method :** POST

**Content-type :** multipart/form-data; charset=utf-8



#### Parameter

| Name            | Type | Description                      | Required |
| --------------- | ---- | -------------------------------- | -------- |
| corpRegNo       | text | 사업자번호                       | O        |
| password        | text | 인증서 비밀번호                  | O        |
| signCertPublic  | file | 인증서 공개키 파일(signCert.der) | O        |
| signCertPrivate | file | 인증서 개인키 파일(signPri.key)  | O        |



#### Sample

```html
<meta charset="utf-8">
<form method="post" enctype="multipart/form-data" action="http://49.50.165.2:30000/dti/api/v1/certificate/1111111119">
사업자번호 : <input type="text" name="corpRegNo" value="1111111119"><br>
인증서 비밀번호 : <input type="password" name="password" value="signgate1!"><br>
인증서 공개키 파일 : <input type="file" name="signCertPublic"><br>
인증서 개인키 파일 : <input type="file" name="signCertPrivate"><br>
<input type="submit" value="검증">
</form>
```



### RESPONSE

**HTTP** 200 OK

**Contet-Type :** application/json; charset=utf-8



#### Parameter

| Name            | Type(max) | Description                    |
| --------------- | --------- | ------------------------------ |
| corpRegNo       | String    | 요청한 사업자번호              |
| password        | Stirng    | 요청한 인증서 비밀번호         |
| signCertPublic  | String    | 요청한 인증서 공개키 암호화 값 |
| signCertPrivate | String    | 요청한 인증서 개인키 암호화 값 |
| useYn           | String    | 사용 가능 여부                 |



#### Sample

```
성공
{"certId":null,
"corpRegNo":"1111111119",
"oid":"",
"validDate":1592353490000,
"expirationDate":1625756399000,
"signCertPublic":"MII......uvg=",
"signCertPrivate":"MII...pOw==",
"kmCertPublic":null,
"kmCertPrivate":null,
"password":"**********",
"rvalue":"",
"encryptionMethod":null,
"useYn":true,
"regTimestamp":1608168523049}

실패(인증서 오류)
{"certId":1004,
"corpRegNo":"1111111119",
"oid":"",
"validDate":1592353490000,
"expirationDate":1625756399000,
"signCertPublic":"MII......uvg=",
"signCertPrivate":"MII......pOw==",
"kmCertPublic":null,
"kmCertPrivate":null,
"password":"signgate1!",
"rvalue":"",
"encryptionMethod":null,
"useYn":false,
"regTimestamp":1608168523050}

실패(인증서 오류)
[]
```

‌

### RESPONSE

**HTTP** 500 Server Error

**Contet-Type :** application/json; charset=utf-8



#### Sample

```
실패(유효기간 만료)
cn=signGATE CA4,ou=AccreditedCA,o=KICA,c=KR 인증서의 유효기간이  만료 되었습니다.

실패(인증서 오류)
인증서 신원 확인 불일치

실패(비밀번호 오류)
private key decoding failed. please check password. padding is not corrected!
```

