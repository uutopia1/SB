[TOC]

### 테스트 환경

OS : Windows Server 2016 Standard



# 1.JDK 설치

jdk는 고객사에서 license를 보유한 정식 버전으로 1.7 이상 설치를 권장한다.

테스트는 https://github.com/alexkasko/openjdk-unofficial-builds 에서 다운받은 openjdk-1.7.0-u80-unofficial-windows-amd64-image로 진행



### 환경변수 설정

제어판 > 시스템 > 시스템속성 > 고급탭 > 환경변수 > 시스템변수 추가

변수이름 : JAVA_HOME

변수 값 : jdk설치경로 (ex. D:\openjdk-1.7.0-u80)

![image-20201209140002911](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201209140002911.png)



# 2.SBMS 설치

SBMS_DTI 폴더를 설치경로에 이동

ex. D:\smartbill\SBMS_DTI



### 소스 파일

최신버전의 소스 파일(dti-1.16.14.war, manager-1.4.33.war)을 %SBMS_HOME%\app\ 경로로 이동



### JDBC

설치할 DB버전에 맞는 JDBC 라이브러리 파일을 %SBMS_HOME%\lib\ 경로로 이동

![image-20201209142940708](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201209142940708.png)



### 설정 파일

%SBMS_HOME%\conf\sbms.properties

```properties
sbms.system.name=SBMS
sbms.bill.endpoint=http://demoneobill.smartbill.co.kr/Request.svc?wsdl
#sbms.bill.endpoint=http://neobill.smartbill.co.kr/Request.svc?wsdl
jms.url=tcp://0.0.0.0:61617
sbms.target=DTI
sbms.oapi.endpoint=http://demoapi.smartbill.co.kr/sb-api/request
#sbms.oapi.endpoint=http://api.smartbill.co.kr/sb-api/request
sbms.system.application.serviceport=30000
sbms.system.jdbc.dbms=mssql
#sbms.system.jdbc.dbms=oracle
#sbms.system.jdbc.dbms=mysql
#sbms.system.jdbc.dbms=tibero
sbms.system.jdbc.user=new_dti
sbms.erp.system=NONSAP
sbms.system.jdbc.url=jdbc:jtds:sqlserver://49.50.165.211:1433;databaseName=dti
#sbms.system.jdbc.url=jdbc:oracle:thin:@//localhost:1521/xe
#sbms.system.jdbc.url=jdbc:mysql://localhost/n_dti
#sbms.system.jdbc.url=jdbc:tibero:thin:@192.168.100.11:8629:tibero
sbms.system.jdbc.driver=net.sourceforge.jtds.jdbc.Driver
#sbms.system.jdbc.driver=oracle.jdbc.driver.OracleDriver
#sbms.system.jdbc.driver=com.mysql.jdbc.Driver
#sbms.system.jdbc.driver=com.tmax.tibero.jdbc.TbDriver
sbms.key=sbms_default_key
sbms.system.application.stopport=30001
sbms.system.jdbc.password=a1234567
# 데모서버일때 인증서 등록시 vid 체크 bypass
sbms.taxinvoice.test.cert.bypass.verify.vid=true
# 네트워크 속도 저하등의 이슈로 큐 적체 현상에 따라 스마트빌과 시간차가 누적되어 발행처리되는 경우에만 사용 (서명지연), 단위 : Milli Second
# [주의 - 필요 판단 시 개발팀과 반드시 상의 후 사용]
# sbms.taxinvoice.option.delaysigntime.millisec=3000
# 자동업데이트 접속 정보
sbms.update.server.ip=58.181.27.25
#sbms.update.server.ip=183.111.188.142
sbms.update.server.port=22
sbms.update.server.user.id=A5KqfNDkAXARgAWFcV6Nkg==
sbms.update.server.user.pwd=1Crm8LBRgjMCRmFbvv7YwA==
# sbms.tls=true
# sbms.tls.keystore.path=D:\\SBMS_DTI\\ssl_cert\\keystore.dat
# sbms.tls.keystore.password=a1234567
# sbms.tls.keymanager.password=a1234567
# sbms.tls.ssl.port=30443
# sbms.tls.cipher.suites=false
# sbms.tls.provide.versions=TLSv1.1, TLSv1.2

```



%SBMS_HOME%\conf\adapter\dti.datasource.default.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
							http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
							http://www.springframework.org/schema/beans
							http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
						     ">

    <!-- Data Source ( SB_DTI )-->
    <bean id="dataSourceDTI-default" class="org.apache.commons.dbcp2.BasicDataSource">
        <property name="driverClassName" value="net.sourceforge.jtds.jdbc.Driver"/>
        <property name="url" value="jdbc:jtds:sqlserver://49.50.165.211:1433;databaseName=dti"/>
        <property name="username" value="new_dti"/>
        <property name="password" value="a1234567"/>
        <property value="3" name="initialSize"/>
        <property value="3" name="maxIdle"/>
        <property value="1" name="defaultTransactionIsolation"/>
        <property value="true" name="defaultAutoCommit"/>
        <property value="select 1" name="validationQuery"/>
    </bean>

    <!-- Transaction Manager ( Datasource : SB_DTI ) -->
    <bean id="transactionManagerDTI" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSourceDTI-default"/>
    </bean>

    <!-- SQL Session Factory ( Datasource : SB_DTI ) -->
    <bean id="sqlSessionFactoryDTI-default" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="environment" value="default"/>
        <property name="dataSource" ref="dataSourceDTI-default"/>
        <property name="configLocation" value="WEB-INF/mybatis-config.xml"/>
        <property name="typeAliasesPackage" value="com.bizon.sbms.server.dti.common.dto"/>
        <property name="mapperLocations" value="classpath:com/bizon/sqlmap/persistence/mssql/*.xml"/>
        <property name="transactionFactory">
            <bean class="org.mybatis.spring.transaction.SpringManagedTransactionFactory"/>
        </property>
    </bean>

    <!-- SQL Session ( Datasource : SB_DTI ) -->
    <bean id="sqlSessionDTI" class="org.mybatis.spring.SqlSessionTemplate" destroy-method="clearCache">
        <constructor-arg index="0" ref="sqlSessionFactoryDTI-default"/>
    </bean>

    <!-- SQL Session Persistence mapper Autowired ( Datasource : SB_DTI )
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.bizon.sbms.server.dti.common.persistence" />
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactoryDTI"/>
    </bean>
    -->
    
</beans>

```



# 3.서비스 등록

%SBMS_HOME%\bin\installService.bat 파일을 수정 및 실행하여 SmartBillService를 윈도우 서비스에 등록한다.

```bat
set SERVICE_NAME=SmartBillService
set SBMS_HOME=D:\smartbill\SBMS_DTI
set SERVICE_PORT=30000
set STOP_PORT=30001
set IS_NAME=SmartBillService
set DEPLOY_HOME=%SBMS_HOME%\deploy
set PR_INSTALL=%SBMS_HOME%\bin\SmartBillService.exe
 
REM Service log configuration
set PR_LOGPREFIX=%SERVICE_NAME%
set PR_LOGPATH=%SBMS_HOME%\logs
REM set PR_STDOUTPUT=%SBMS_HOME%\logs\stdout.txt
set PR_STDERROR=%SBMS_HOME%\logs\ServiceErr.txt
set PR_LOGLEVEL=Error

REM Path to java installation
set PR_JVM=D:\smartbill\openjdk-1.7.0-u80\jre\bin\server\jvm.dll
set PR_CLASSPATH=%SBMS_HOME%\app\ejetty.jar;%SBMS_HOME%\app\lib\*
 
REM Startup configuration
set PR_STARTUP=auto
set PR_STARTMODE=jvm
set PR_STARTCLASS=com.bizon.sbms.server.SBMSServer
set PR_STARTMETHOD=start

REM Shutdown configuration
set PR_STOPMODE=jvm
set PR_STOPCLASS=com.bizon.sbms.server.SBMSServer
set PR_STOPMETHOD=stop

REM JVM configuration
set PR_JVMMS=256
set PR_JVMMX=512
set PR_JVMOPTIONS=-DSBMS_HOME=%SBMS_HOME%;-Djava.io.tmpdir=%DEPLOY_HOME%;-Dsbms.application.serviceport=%SERVICE_PORT%;-Dsbms.application.stopport=%STOP_PORT%;-Djava.library.path=%SBMS_HOME%\lib;-Xmx1024m;-Xms1024m;-XX:MaxPermSize=256m;-Dfile.encoding=utf-8;-Dlogback.configurationFile=%SBMS_HOME%\conf\logback.xml
 
REM Service Description
set PR_DISPLAYNAME=%IS_NAME%
set PR_DESCRIPTION=Smartbill SBMS Service

REM Install service
%PR_INSTALL% //IS//%IS_NAME%
exit

```



%SBMS_HOME%\bin\SmartBillServicew.exe 을 실행하여 서비스를 Start하거나 윈도우 서비스에서 SmartBillService를 찾아서 실행한다.

![image-20201209143158090](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201209143158090.png)

![image-20201209143251298](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201209143251298.png)



# 4.중계서버 제공 페이지

서비스 실행 후 보통 1,2분 내로 서비스가 정상적으로 올라온다.



### 기본 페이지

중계서버 기본페이지를 호출하여 스마트빌서버와 정상통신 여부 확인 및 DB 정상접속 여부를 확인한다.

http://중계서버IP:포트/dti

![image-20201209144007049](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201209144007049.png)



### 관리 페이지

중계서버 관리페이지에 로그인 후 DB 초기설정을 진행한다.

http://중계서버ip:포트/manager (관리자 : sbms / sbms000)

![image-20201209144202726](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201209144202726.png)



#### 라우팅 등록

라우팅 정보 관리 메뉴에서 라우팅 정보를 등록한다.

시스템 ID : default

시스템명 : default

ADAPTER : NONSAP

TARGET KEY : default

![image-20201209144512012](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201209144512012.png)



#### 승인번호 업체코드 등록

쿼리전송 메뉴를 통해서 승인번호의 업체코드 3자리를 등록한다.

ex. insert into xxsb_dti_approve_id values ('41000008', 'kkk', 1)

![image-20201209144724713](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201209144724713.png)