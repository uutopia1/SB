[TOC]

# SBMS DTI TLS 설치

TLS인증서는 보통 서버인증서(domain.crt), 루트인증서(.crt), 체인인증서(.crt)로 구성되어 있고 서버인증서는 개인키(private.key)를 포함한다. keystore는 위의 모든 인증서를 포함하는 저장소로  파일형식은 .jks, .pfx 등의 형태로 저장된다.

스마트빌 모듈에 적용하기 위한 keystore 요건은 아래와 같다.

- **.jks 형식**
- **서버인증서(PrivateKeyEntry)의 alias를 sbms로 지정**
- **2048bit RSA 키 암호화된 Root 인증서**
- **체인인증서(선택)**

keystore의 세팅은 인증서 포맷이 다양하여 비교적 복잡한 작업이 필요하므로 일반적으로는 업체에 요청하여 구성이 완료된 .jks파일을 전달 받아서 세팅을 진행하도록 하고 직접 keystore를 세팅해야 하는 경우 아래 기본 내용 정도만 확인한다.



# 1. keystore 세팅

설치된 jdk의 /bin/keytool.exe을 cmd창(관리자권한)에서 실행해서 keystore를 세팅할 수 있다.

```cmd
cd d:\openjdk-1.7.0-u80\bin\
```



### keystore 정보 확인

설치된 jdk의 /bin/keytool.exe파일을 통해서 keystore 정보를 확인할 수 있다.

```cmd
keytool -list -keystore [keystore파일경로]
```

![image-20201210130315571](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201210130315571.png)



### alias 변경

-list로 확인한 결과 서버인증서(PrivateKeyEntry)의 alias가 sbms가 아닌경우 반드시 sbms로 변경해준다.

```cmd
keytool -changealias -keystore [keystore파일경로] -alias [기존alias명] -destalias [변경할alias명]
```

![image-20201210131636491](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201210131636491.png)



### keystore 인증서 추가

필요 시 keystore파일에 루트인증서, 체인인증서 등을 추가할 수 있다.

```cmd
keytool -importcert -keystore [keystore파일경로] -storepass [keystore비밀번호] -trustcacerts -alias [추가할인증서alias명] -file [추가할인증서경로]
```

![image-20201210132848039](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201210132848039.png)



### keystore 인증서 삭제

keystore파일에 인증서를 잘못 등록한 경우 삭제할 수 있다.

```cmd
keytool -delete -alias [alias명] -keystore [keystore파일경로]
```

![image-20201210133235228](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201210133235228.png)





# 2. SBMS_HOME 세팅

%SBMS_HOME%\conf\sbms.properties 파일에 아래 tls설정을 추가한 후 서비스를 재시작하면 tls가 적용된다.

```properties
sbms.tls=true
sbms.tls.keystore.path=[keystore파일경로]
sbms.tls.keystore.password=[keystore비밀번호]
#저장소비밀번호와 인증서비밀번호는 일반적으로 동일하게 세팅하지만 차이가 있을 수 있다.
sbms.tls.keymanager.password=[key비밀번호]
sbms.tls.ssl.port=[tls포트]
#특정 tls암호화방식 사용여부
sbms.tls.cipher.suites=false
#허용할 tls버전
sbms.tls.provide.versions=TLSv1.1, TLSv1.2
```
