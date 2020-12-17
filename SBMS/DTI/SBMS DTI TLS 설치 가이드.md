[TOC]

# SBMS DTI TLS 설치

일반적으로 TLS인증서 발급 시 CSR 자동생성의 경우 기본 Base64 PEM 포맷 외 개인키, 서버인증서, 루트인증서, 체인인증서를 모두 포함하는 keystore(.jks, .pfx, .p12 등) 포맷으로 변환하여 발급받을 수 있다. 스마트빌 모듈의 웹서버인 jetty를 포함하여 java기반의 웹서버의 경우 .jks포맷의 keystore를 요구한다.

스마트빌 모듈에 적용하기 위한 keystore 요건은 아래와 같다.

- **.jks 형식**
- **개인키를 포함한 서버인증서(PrivateKeyEntry)의 alias를 sbms로 지정**
- **2048bit RSA 키 암호화된 루트인증서**
- **체인인증서(선택)**

전달받은 파일이 위 요건을 만족하지 않을 경우 openssl이나 keytool을 사용해서 keystore를 직접 생성하거나 포맷을 변경할 수 있다.

작업을 진행하기 전에 keystore를 구성하는 인증서에 대한 기본적인 지식정도는 숙지가 필요하다.

- **keystore란?** 키저장소로 개인키, 서버인증서, 루트인증서, 체인인증서를 모두 포함할 수 있다. 포맷은 .jks, .pfx, .p12, .keystore 등을 사용한다. 인증서 발급 시 개인키 및 모든 인증서를 포함하는 .jks 포맷의 keystore로 발급 받을 수 있다.
- **개인키(private.key)란?** 암복호화를 위한 키 값으로 별도의 파일로 존재할 경우 구분을 위해서 .key 포맷을 주로 사용한다. 인증서 발급 시 서버인증서에 포함되어 발급 받는다.
- **서버(domain)인증서란?** 특정 도메인을 사용하는 웹사이트의 신원을 확인하는 인증서로 인증서 서명 요청(CSR)과 개인키를 통해서 발급한다. 포맷은 .pem, .crt, .cer 등을 사용한다. 발급 시 루트인증서, 체인인증서를 포함한 .jks 포맷으로 변환하여 발급 받을 수 있다.
- **루트(Root)인증서란?** 루트 인증기관에서 관리하는 공개키 인증서로 포맷은 .pem, .crt, .cer 등을 사용한다. 인증서 발급 시 keystore에 포함하여 발급 받는다.
- **체인(Chain)인증서란?** 루트인증서와 서버인증서 사이에 존재하는 인증서로  포맷은 .pem, .crt, .cer 등을 사용한다. 인증서 발급 시 keystore에 포함하여 발급 받는다.



# 1. keystore 생성

전달받은 인증서파일이 keystore 포맷(.jks, .p12, .pfx)이 아닌 개인키(.key)와 인증서파일(.pem, .crt)로 되어있다면 openssl 프로그램을 사용해서 keystore를 직접 생성할 수 있다. (openssl설치는 직접 진행)

> .jks 포맷으로 전달받았다면 이 단계를 건너뛰고 **2. keystore 세팅** 으로 넘어간다.



### pfx 변환

openssl을 사용해서 개인키와 인증서파일을 .pfx포맷의 keystore로 변환한다.

```cmd
openssl pkcs12 -export -name [alias명] -in [인증서파일] -inkey [개인키파일] -out [변환할pfx파일]

ex. openssl pkcs12 -export -name sbms -in sbms.pem -inkey private.key -out sbms.pfx
```

![image-20201214163639743](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201214163639743.png)

> 인증서 파일을 바로 .jks포맷으로 변환할 수 없기 때문에 우선 .pfx포맷으로 변환 후 **2. keystore 세팅**의 **2) keystore 포맷변경**을 참고하여 .jks포맷으로 변경할 수 있다.
>



# 2. keystore 세팅

전달받은 keystore파일의 정보를 확인하고 변경하거나 인증서를 추가할 수 있다.



### 1) keytool

설치된 jdk의 %JAVA_HOME%/bin/keytool.exe 프로그램을 통해서 keystore를 세팅할 수 있다. (jdk설치는 직접 진행)

명령 프롬프트(cmd)를 관리자권한으로 실행 후 설치된 keytool.exe 경로로 이동하여 keytool을 실행할 수 있는 상태로 만든다.

```cmd
cd [jdk경로]\bin

ex. cd "c:\Program Files\Java\openjdk-1.7.0-u80-unofficial-windows-amd64-image\bin"
```

![image-20201211110407755](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201211110407755.png)



### 2) keystore 포맷변경

전달받은 인증서 keystore의 포맷이 .jks가 아닌 .pfx나 .p12인 경우 keytool -importkeystore 명령어를 사용하여 .jks 포맷으로 변환할 수 있다.

> .jks 포맷으로 전달받았다면 이 단계를 건너뛰고 **3) keystore 정보 확인** 으로 넘어간다.

```cmd
keytool -importkeystore -srckeystore [기존p12파일] -srcstoretype pkcs12 -destkeystore [변경할jks파일] -deststoretype jks

ex. keytool -importkeystore -srckeystore c:\keystore\smartbill.test.com.p12 -srcstoretype pkcs12 -destkeystore c:\keystore\smartbill.test.com.jks -deststoretype jks
```



### 3) keystore 정보 확인

keytool -list 명령어를 사용하여 전달받은 keystore(.jsk)의 정보를 확인할 수 있다.

```cmd
keytool -list -keystore [keystore파일]

ex. keytool -list -keystore c:\keystore\smartbill.test.com.jks
```



#### 3-1) 정보 확인 결과 : 정상

![image-20201211155753495](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201211155753495.png)

alias명 : root, 등록일자 : 2020. 12. 11, 인증서종류 : trustedCertEntry

alias명 : sbms, 등로일자 : 2020. 12. 11, 인증서종류 : PrivateKeyEntry(개인키가 포함된 서버인증서)

> 위와같이 privateKeyEntry의 alias명이 sbms이고 root인증서, chain인증서 등이 정상적으로 추가 된 상태라면 **2. keystore 세팅** 단계를 건너뛰고 **3. SBMS_HOME 설정** 으로 넘어간다.
>



#### 3-2) 정보 확인 결과 : 비정상

![image-20201211112102632](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201211112102632.png)

alias명 : tomcat, 인증서종류 : PrivateKEyEntry

위와같이 alias가 sbms로 지정되지 않았거나 루트인증서가 포함되지 않은 경우 변경이나 추가가 필요하다.



### 4) alias 변경

keytool -list로 확인한 결과 서버인증서(PrivateKeyEntry)의 alias가 sbms가 아닌경우 keytool -changealias 명령어를 사용하여 반드시 alias를 sbms로 변경해준다.

```cmd
keytool -changealias -keystore [keystore파일] -alias [기존alias명] -destalias [변경할alias명]

ex. keytool -changealias -keystore c:\keystore\smartbill.test.com.jks -alias tomcat -destalias sbms
```

![image-20201211113350379](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201211113350379.png)

alias가 sbms로 변경된 것을 확인



### 5) keystore 인증서 추가

keytool -list로 확인한 결과 키 저장소목록에 PrivateKeyEntry만 존재하고 루트인증서(trustedCertEntry)가 포함되어 있지 않은 경우 루트인증서파일을 전달 받아서 keytool -importcert 명령어를 사용하여 추가한다. 필요 시 체인인증서도 동일한 방법으로 추가할 수 있다.

```cmd
keytool -importcert -keystore [keystore파일] -storepass [keystore비밀번호] -trustcacerts -alias [추가할인증서alias명] -file [추가할인증서파일]

ex. keytool -importcert -keystore c:\keystore\smartbill.test.com.jks -storepass abcd1234 -trustcacerts -alias root -file c:\keystore\GLOBALSIGN_ROOT_CA.crt
```

![image-20201211114453155](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201211114453155.png)

keystore에 root인증서(trustedCertEntry)가 추가된 것을 확인



### 6) keystore 인증서 삭제

작업 중 keystore파일에 인증서를 잘못 등록한 경우 keytool -delete명령어를 사용하여 삭제할 수 있다.

```cmd
keytool -delete -alias [alias명] -keystore [keystore파일]

ex. keytool -delete -alias root -keystore c:\keystore\smartbill.test.com.jks
```



# 3. SBMS_HOME 설정

%SBMS_HOME%\conf\sbms.properties 설정파일에 아래 tls속성을 추가한 후 서비스를 재시작하면 tls가 적용된다.

설정파일 변경 전에 세팅이 완료된 keystore파일을 %SBMS_HOME%\keystore\ 경로로 이동한다.

```properties
#tls 사용여부
sbms.tls=true
#keysotre 파일경로
sbms.tls.keystore.path=\\keystore\\smartbill.test.com.jks
#keystore 비밀번호
sbms.tls.keystore.password=abcd1234
#인증서 비밀번호
#저장소 비밀번호와 인증서 비밀번호는 일반적으로 동일하게 세팅하지만 다를수도 있다.
sbms.tls.keymanager.password=abcd1234
#ssl 접속 포트
sbms.tls.ssl.port=30443
#특정 tls 암호화방식 사용여부
sbms.tls.cipher.suites=false
#허용할 tls 버전
sbms.tls.provide.versions=TLSv1.1, TLSv1.2
```



적용된 domain을 호출하여 정상적용 여부를 확인할 수 있다.

![image-20201211155353630](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201211155353630.png)

