# StandAlone

SA(StandAlone)이란 중계서버 설치 후 별도의 ERP화면 개발 없이 기본적으로 제공하는 화면을 통해서 데이터를 처리할 수 있도록 제공하는 탬플릿을 말한다.



### 소스 파일

최신버전의 소스파일(standalone-1.2.war)을 %SBMS_HOME%\app 경로에 위치한다.



### 설정 파일

%SBMS_HOME%\conf\legacy-dti-data.properties

```properties

#File
dti.file.path=${HOME}\\UploadFile\\
dti.file.perSize=10
dti.file.totalSize=10

#SBMS_URL
sbms.url = http://localhost:30000

#MS-SQL
dti.jdbc.dbms=mssql
dti.jdbc.driver=net.sourceforge.jtds.jdbc.Driver
dti.jdbc.url=jdbc:jtds:sqlserver://49.50.165.210;databaseName=n_dti
dti.jdbc.user=n_dti
dti.jdbc.password=a1234567

#Oracle
#dti.jdbc.dbms=oracle
#dti.jdbc.driver=oracle.jdbc.OracleDriver
#dti.jdbc.url=jdbc:oracle:thin:@//192.168.100.19:1521/xe
#dti.jdbc.user=legacyTest
#dti.jdbc.password=legacyTest1

#MySQL
#dti.jdbc.dbms=mssql
#dti.jdbc.driver=com.mysql.jdbc.Driver
#dti.jdbc.url=jdbc:mysql://localhost:3306/bizon_dti
#dti.jdbc.user=dti
#dti.jdbc.password=123qwe!1

#Tibero
#dti.jdbc.url=jdbc:tibero:thin:@192.168.100.112:8621:tibero
#dti.jdbc.driver=com.tmax.tibero.jdbc.TbDriver
#dti.jdbc.dbms=tibero
#dti.jdbc.user=sbms
#dti.jdbc.password=a123451

dti.jdbc.initialSize=1
dti.jdbc.maxActive=10

## PDF Viewer URL
#dti.viewer.url=http://192.168.100.223:50000/dti/api/v1/view/

## SBMS Cert URL
#sbms.cert.url=http://192.168.100.223:50000/dti/api/v1/certificate/

## 사용 프로세스
AR_YN=Y
AP_YN=Y
ISSUE_YN=Y
DTT_YN=Y
ATTACH_YN=Y
 
```



소스파일, 설정파일 세팅 후 서비스를 재시작해야 적용된다.



### 관리자 계정

초기 관리자 계정을 세팅해준다.

```sql
INSERT INTO XXSB_DTI_SM_USER ('admin', '', 'admin', 'admin', 'admin', 'Y', 'test@test.com', '', 'test@test.com', '')
```



### 로그인

http://중계서버ip:포트/standalone

![image-20201209150220984](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201209150220984.png)





