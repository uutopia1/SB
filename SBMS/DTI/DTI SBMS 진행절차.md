[TOC]

# 전자세금계산서(DTI) 중계서버(SBMS) 연동이란?

스마트빌서버와 통신을 위한 중계서버(SBMS)와 데이터 연동을 위한 중계DB를 ERP 내부망(혹은 DMZ)에 설치하고 ERP시스템에서 중계DB를 통해서 데이터를 처리할 수 있는 ERP 화면을 개발하여 전자세금계산서의 발행, 관리 국세청전송 등의 기능을 ERP에서 직접 사용할 수 있도록 연동 시스템을 구축합니다.

![image-20201222130331353](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222130331353.png)



# DTI SBMS 연동  진행 절차

![image-20201222130053275](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222130053275.png)

## 1.스마트빌 회원가입

데모 : [http://demo.smartbill.co.kr](http://demo.smartbill.co.kr/)

운영 : [http://www.smartbill.co.kr](http://www.smartbill.co.kr/)

가입한 사업자번호를 스마트빌 담당자에게 전달





## 2.중계서버(SBMS) 준비

스마트빌서버와 연동을 위한 중계서버(SBMS)를 준비

- 공인 IP 설정

#### 하드웨어

- CPU : 2.4Ghz 이상

- HDD : 100GB 이상

- RAM : 8GB 이상

#### 소프트웨어

- OS : NT계열 서버, UNIX계열 서버
- JDK : 1.7 이상
- DBMS : Oracle, MSSQL, MYSQL, tibero, MariaDB

#### 방화벽

![image-20201222114211242](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222114211242.png)





## 3.중계DB 구성

스마트빌에서 전달받은 DB Script를 사용하여 테이블을 구성합니다.

구성이 완료되면 계정을 포함한 접속정보를 스마트빌 담당자에게 전달합니다.

DB와 계정은 다른 시스템에 영향이 없도록 별도로 생성할 것을 권장합니다.





## 4.중계서버(SBMS) 설치

중계서버의 설치는 스마트빌에서 진행합니다.

외부망 접속이 가능한 경우 중계서버에서 설치작업이 가능하도록 원격접속을 지원해주시면 됩니다.

원격접속 URL(https://rsup.net/smartbill)을 호출하여 안내 받은 번호로 연결하시면 됩니다.

![image-20201222130832915](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222130832915.png)





## 5.라우팅 등록

설치완료 후 중계서버의 공인IP를 스마트빌서버에 등록하여 통신이 가능한 환경을 구축합니다.

(해당 작업은 스마트빌 담당자가 진행 합니다.)





## 6.통신 테스트

DB, 스마트빌서버, 인증기관 등과 연결에 문제가 없는지 기본 통신 테스트를 진행합니다.

(해당 작업은 스마트빌 담당자가 진행 합니다.)





## 7.화면 개발 및 기능 테스트

개발가이드 문서를 참고하여 화면을 개발하고 기능을 테스트 합니다.





모든 테스트가 완료되면 운영 이관 후 시스템을 오픈 합니다.







