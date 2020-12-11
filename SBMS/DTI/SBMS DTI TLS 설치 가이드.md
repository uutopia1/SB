[TOC]

# SBMS DTI TLS 설치

TLS인증서는 보통 서버인증서, 루트인증서, 체인인증서로 구성되어 있고 서버인증서는 개인키(private.key)를 포함한다. keystore는 위의 모든 인증서를 포함하는 저장소로  파일형식은 .jks, .pfx 등의 형태로 저장된다.

스마트빌 모듈에 적용하기 위한 keystore 요건은 아래와 같다.

- **.jks 형식**
- **서버인증서(PrivateKeyEntry)의 alias를 sbms로 지정**
- **2048bit RSA 키 암호화된 Root 인증서**
- **체인인증서(선택)**

keystore의 세팅은 인증서 포맷이 다양하여 비교적 복잡한 작업이 필요하므로 일반적으로는 업체에 위와 같은 요건으로 요청하여 구성이 완료된 .jks파일을 전달 받아서 세팅을 진행하도록 한다.

전달받은 keystore(.jks)파일에 문제가 있을 경우 부득이하게 아래 keystore세팅을 통해서 기본적인 내용을 확인하고 alias변경이나 루트인증서 추가작업 정도만 지원하도록 한다.



# keystore 세팅

설치된 JAVA_HOME경로의 /bin/keytool.exe 프로그램을 cmd창(관리자권한)으로 실행해서 keystore를 세팅할 수 있다.

cmd를 관리자권한으로 실행 후 keytool.exe 경로로 이동하여 keytool 명령어를 실행할 수 있는 상태로 만든다.

```cmd
cd "c:\Program Files\Java\openjdk-1.7.0-u80-unofficial-windows-amd64-image\bin"
```

![image-20201211110407755](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201211110407755.png)



### 1.keystore 정보 확인

keytool.exe경로에서 keytool -list 명령어를 사용하여 전달받은 keystore파일(.jsk)의 정보를 확인할 수 있다.

```cmd
keytool -list -keystore [keystore파일경로]
ex. keytool -list -keystore c:\keystore\smartbill.test.com.jks
```



#### 정보 확인 결과 : 정상

![image-20201211155753495](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201211155753495.png)

alias명 : root, 등록일자 : 2020. 12. 11, 인증서종류 : trustedCertEntry

alias명 : sbms, 등로일자 : 2020. 12. 11, 인증서종류 : PrivateKeyEntry(개인키가 포함된 서버인증서)

위와같이 정상적으로 세팅이 된 상태라면 2,3,4단계를 건너뛰고 SBMS_HOME설정부터 진행한다.



#### 정보 확인 결과 : 비정상

![image-20201211112102632](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201211112102632.png)

alias명 : tomcat, 인증서종류 : PrivateKEyEntry

위와같이 alias가 sbms로 지정되지 않았거나 루트인증서가 포함되지 않은 경우 2,3단계를 진행한다.



### 2.alias 변경

keytool -list로 확인한 결과 서버인증서(PrivateKeyEntry)의 alias가 sbms가 아닌경우 keytool -changealias 명령어를 사용하여 반드시 alias를 sbms로 변경해준다.

```cmd
keytool -changealias -keystore [keystore파일경로] -alias [기존alias명] -destalias [변경할alias명]
ex. keytool -changealias -keystore c:\keystore\smartbill.test.com.jks -alias tomcat -destalias sbms
```

![image-20201211113350379](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201211113350379.png)

alias가 sbms로 변경된 것을 확인



### 3.keystore 인증서 추가

keytool -list로 확인한 결과 키 저장소목록에 PrivateKeyEntry 한개만 존재하고 루트인증서(trustedCertEntry)가 포함되어 있지 않은 경우 루트인증서파일을 전달 받아서 keytool -importcert 명령어를 사용하여 추가해준다. 필요 시 체인인증서도 동일한 방법으로 추가할 수 있다.

```cmd
keytool -importcert -keystore [keystore파일경로] -storepass [keystore비밀번호] -trustcacerts -alias [추가할인증서alias명] -file [추가할인증서파일경로]
ex. keytool -importcert -keystore c:\keystore\smartbill.test.com.jks -storepass abcd1234 -trustcacerts -alias root -file c:\keystore\GLOBALSIGN_ROOT_CA.crt
```

![image-20201211114453155](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201211114453155.png)

keystore에 root인증서(trustedCertEntry)가 추가된 것을 확인



### 4.keystore 인증서 삭제

keystore파일에 인증서를 잘못 등록한 경우 keytool -delete명령어를 사용하여 삭제할 수 있다.

```cmd
keytool -delete -alias [alias명] -keystore [keystore파일경로]
ex. keytool -delete -alias root -keystore c:\keystore\smartbill.test.com.jks
```



# SBMS_HOME 세팅

SBMS_HOME경로의 \conf\sbms.properties 설정파일에 아래 tls속성을 추가한 후 서비스를 재시작하면 tls가 적용된다.

```properties
#tls 사용여부
sbms.tls=true
#keysotre 파일경로
sbms.tls.keystore.path=c:\keystore\smartbill.test.com.jks
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

