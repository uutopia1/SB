[TOC]

# 정매출 발행

공급자가 공급받는자에게 세금계산서를 발행하는 프로세스로 발행 후 승인, 거부, 취소 등의 상태 변경이 가능합니다.

![image-20201222164439961](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222164439961.png)



### 1.구성도

세금계산서 발행 데이터를 DB에 입력 시 중계서버 -> 스마트빌서버를 통해서 세금계산서가 발행되며 일정시간(약 10초)이 지난 후 발행에 대한 처리 결과가 DB에 UPDATE 됩니다.

![image-20201222155802589](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222155802589.png)



### 2.화면구성

발행 화면은 아래와 비슷한 UI로 구성하면 됩니다.

![image-20201222160035724](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222160035724.png)



### 3.발행 데이터 입력

발행 시 아래 4개의 테이블에 명세서를 참고하여 데이터를 입력합니다.

- XXSB_DTI_MAIN //세금계산서 정보
- XXSB_DTI_ITEM //세금계산서 품목정보
- XXSB_DTI_STATUS //세금계산서 상태정보
- XXSB_DTI_INTERFACE //세금계산서 연동정보

MAIN, ITEM, STATUS 테이블은 세금계산서 1건 당 1개의 record가 저장되고, INTERFACE 테이블은 세금계산서의 처리(발행, 상태변경)마다 record가 추가로 저장됩니다.



### 4.처리결과 확인

중계서버에 설치된 스마트빌 모듈에서 INTERFACE 테이블을 바라보다가 발행 데이터가 정상적으로 입력되면 POLLING하여 발행 처리 후 처리 결과를 STATUS테이블에 UPDATE합니다.

정상 발행 여부는 STATUS테이블의 아래 2개의 필드를 통하여 확인할 수 있습니다.

- TRAN_STATUS=?

  - S : 전송전 (저장상태)
  - P, R, T 등 : 전송중 (일정시간 이상 지속될 경우 전송 중 오류 발생)
  - **C : 정상처리**

  - E : 오류

- FINAL_STATUS=?

  - S, P, R, T 등 : 전송중 (일정시간 이상 지속될 경우 전송 중 오류 발생)
  - **I : 정상처리 (수신미승인상태)**

  - E : 오류

추가로 아래 2개의 필드를 통하여 오류코드, 오류메시지를 확인할 수 있습니다.

- RETURN_CODE=?

  - NULL : 처리전

  - **30000 : 정상처리**
  - {30000 외 값} : 오류

- RETURN_DESCRIPTION=?

  - NULL : 처리전
  - **empty : 정상처리**
  - {오루내용} : 오류



### 5.국세청 전송

세금계산서 발행이 정상적으로 처리되면 익일(영업일기준) 스마트빌 서버를 통해서 자동으로 국세청 전송(오후2시-6시 사이)됩니다. 전송 결과는 스마트빌서버 -> 중계서버를 통해서 매일 오전,오후 8시에 전송되어 중계DB에 UPDATE 됩니다. 

예를 들어서 1월1일(월) 발행 시 1월2일(화) 국세청 전송되고 1월2일(화) 오후8시에 결과가 UPDATE됩니다.

국세청 전송이 정상적으로 처리되면 MAIN테이블의 아래 2개의 필드가 UPDATE 됩니다.

- DTI_IDATE : 발행일자
- DTI_SDATE : 국세청전송일자

국세청 전송 결과에 대한 상세내용은 STATUS테이블의 아래 2개의 필드를 통하여 확인할 수 있습니다.

- SEND_REQUEST, SEND_REQUEST_DESC
  - 9, 미전송
  - 0, 전송중
  - 1, 처리중
  - **7, 전송완료**
  - 2, 검증실패
  - 3, 전송실패

국세청 처리 결과에 대한 상세내용은 STATUS테이블의 아래 2개의 필드를 통하여 확인할 수 있습니다.

- RESULT_REQUEST, ERROR_MSG

  - **SUC001, 성공**

  - {ERROR코드}, {ERROR내용}











