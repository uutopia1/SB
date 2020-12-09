### 테스트환경

OS : CentOS-7.3-64



## 1.jdk 설치

jdk는 고객사에서 license를 보유한 정식 버전으로 1.7 이상 설치를 권장한다. (리눅스에서 배포되는 openJDK로는 오류 발생)

테스트는 https://github.com/alexkasko/openjdk-unofficial-builds 에서 다운받은 openjdk-1.7.0-u80-unofficial-linux-amd64-image.zip로 진행



### 설치 경로 설정

jdk image.zip 파일을 압축해제 후 로컬경로로 이동한다.

```cmd
mv openjdk_1.7.0-u80/ /usr/local/lib/openjdk_1.7.0-u80
```



### 환경변수 설정

linux 설정파일에 환경변수 추가

```cmd
vi /etc/profile
```

```cmd
......
export JAVA_HOME=/usr/local/lib/openjdk_1.7.0-u80
export PATH=$PATH:$JAVA_HOME/bin
......
```

변경사항 적용

```cmd
source /etc/profile
```

확인

```cmd
which java
```



### 심볼릭링크(명령어) 생성

java, javac, javaws 명령어 등록

```cmd
alternatives --install /usr/bin/java java /usr/local/lib/openjdk-1.7.0-u80/bin/java 1
alternatives --install /usr/bin/java javac /usr/local/lib/openjdk-1.7.0-u80/bin/javac 1
alternatives --install /usr/bin/java javaws /usr/local/lib/openjdk-1.7.0-u80/bin/javaws 1
alternatives --set java /usr/local/lib/openjdk-1.7.0-u80/bin/java
alternatives --set javac /usr/local/lib/openjdk-1.7.0-u80/bin/javac
alternatives --set javaws /usr/local/lib/openjdk-1.7.0-u80/bin/javaw
```

등록된 java 버전 확인

```cmd
Java -version
```



## 2.SBMS 설치

%SBMS_HOME%(SBMS_DTI) 설치는 windows와 동일하며 윈도우 서비스 대신 sh파일을 실행해서 프로세스를 등록한다.



### 쉘 스크립트 생성

run.sh

```shell
nohup java -cp .:./app/lib -Dfile.encoding=UTF-8 -Djava.library.path=./lib -DSBMS_HOME=/root/smartbill/SBMS_DTI -XX:MaxPermSize=256m -Dsbms.application.serviceport=30000 -Dsbms.application.stopport=30001 -Xms256m -Xmx2048m -Dlogback.configurationFile=./conf/logback.xml -jar ./app/lib/ejetty.jar start > /dev/null &
```



run.sh (오류 확인)

```shell
nohup java -cp .:./app/lib -Dfile.encoding=UTF-8 -Djava.library.path=./lib -DSBMS_HOME=/root/smartbill/SBMS_DTI -XX:MaxPermSize=256m -Dsbms.application.serviceport=30000 -Dsbms.application.stopport=30001 -Xms256m -Xmx2048m -Dlogback.configurationFile=./conf/logback.xml -jar ./app/lib/ejetty.jar start > ./logs/stdout.txt 2> ./logs/stderr.txt &
```

쉘 실행 시 오류가 발생할 경우 위와 같이 수정하여 생성 된 stdout.txt, stderr.txt 파일을 분석하여 오류를 해결한다.

nohup 제거 시 sh실행에 대한 로그도 확인할 수 있지만 세션이 끊겨도 유지되기 위해서는 필요한 옵션이다.



stop.sh

```shell
java -cp .:./app/lib -Djava.library.path=./lib -DSBMS_HOME=/root/smartbill/SBMS_DTI -Dsbms.application.serviceport=30000 -Dsbms.application.stopport=30001 -Xms256m -Xmx1024m -jar ./app/lib/ejetty.jar stop
```



### 실행/종료

```cmd
sh run.sh
sh stop.sh
```

