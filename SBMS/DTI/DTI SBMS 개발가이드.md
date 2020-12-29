[TOC]

# 1.정매출

거래처(공급받는자)에 세금계산서를 발행하면 수신미승인상태(I)가 되고 수신승인 여부와 관계없이 국세청 전송됩니다. 국세청 전송 전에 발행한 세금계산서를 취소할 수 있고, 거래처(공급받는자)에서는 수신승인, 수신거부의 상태변경을 할 수 있습니다.

![image-20201222164439961](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222164439961.png)



## 1)발행

#### 발행 프로세스

세금계산서 발행 데이터를 DB에 입력 시 중계서버 -> 스마트빌서버를 통해서 세금계산서가 발행되며 일정시간(약 10초)이 지난 후 발행에 대한 처리 결과가 DB에 UPDATE 됩니다.

![image-20201222155802589](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222155802589.png)



#### 발행 화면 구성

세금계산서를 직접 작성하거나 전표를 통해서 발행하는 화면을 구성합니다.

![image-20201222160035724](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222160035724.png)



#### 발행 데이터 입력

발행 시 아래 4개의 테이블에 각각 데이터를 입력합니다. (명세서, 입력샘플 문서 참고)

- XXSB_DTI_**MAIN** //세금계산서 정보
- XXSB_DTI_**ITEM** //세금계산서 품목정보
- XXSB_DTI_**STATUS** //세금계산서 상태정보
- XXSB_DTI_**INTERFACE** //세금계산서 연동정보

MAIN, ITEM, STATUS 테이블은 세금계산서 1건 당 1개의 record가 저장되고, INTERFACE 테이블은 세금계산서의 처리(발행, 상태변경)마다 record가 추가로 저장됩니다.



#### 발행 처리 결과 확인

중계서버에 설치된 스마트빌 모듈에서 INTERFACE 테이블을 주기적으로 POLLING 하다가 발행 데이터가 정상적으로 입력되면 발행 처리 후 처리 결과를 STATUS테이블에 UPDATE합니다.

처리 후 정상 발행 여부는 STATUS테이블의 아래 2개의 필드를 통하여 확인할 수 있습니다.

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







## 2)상태조회

#### 상태조회 프로세스

발행된 세금계산서에 대해서 거래처(공급받는자)에서 수신승인, 수신거부의 상태변경을 할 수 있습니다. 상태변경 시 스마트빌서버->중계서버를 통해서 중계DB에 데이터가 UPDATE됩니다.

![image-20201228092036138](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201228092036138.png)



#### 상태조회 화면구성

매출세금계산서를 조회하여 세금계산서의 상태를 확인할 수 있도록 화면을 구성합니다.

![image-20201223113259430](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201223113259430.png)



#### 상태조회 데이터 확인

거래처(공급자)에서 상태변경 시 STATUS에 데이터가 UPDATE, INTERFACE에 INSERT 됩니다. (명세서, 입력샘플 문서 참고)

- XXSB_DTI_**STATUS** //세금계산서 상태정보
- XXSB_DTI_**INTERFACE** //세금계산서 연동정보

STATUS테이블의 FINAL_STATUS를 조회하여 최종상태를 확인합니다.







## 3)상태변경

#### 상태변경 프로세스

발행 후 국세청전송되거나 거래처에서 수신승인, 수신거부의 상태변경을 하기 전에 발행취소를 할 수 있습니다. 발행취소 데이터를 중계DB에 입력하면 중계서버->스마트빌서버를 통해서 상태변경이 처리되고 처리결과가 중계DB에 다시 UPDATE 됩니다.

![image-20201228084514604](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201228084514604.png)



#### 상태변경 화면 구성

매출세금계산서의 상태를 조회하는 화면에서 발행취소 기능을 구성합니다.

![image-20201223113259430](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201223113259430.png)



#### 상태변경 데이터 입력

상태변경 시 STATUS에 데이터를 UPDATE, INTERFACE에 INSERT 합니다. (명세서, 입력샘플 문서 참고)

- XXSB_DTI_**STATUS**
  - DTI_STATUS='O'
- XXSB_DTI_**INTERFACE**
  - SIGNAL='CANCELALL'



#### 상태변경 처리 결과 확인

중계서버에 설치된 스마트빌 모듈에서 INTERFACE 테이블을 주기적으로 POLLING 하다가 상태변경 데이터가 정상적으로 입력되면 상태변경 처리 후 처리 결과를 STATUS테이블에 UPDATE합니다.

정상 처리 여부는 STATUS테이블의 아래 2개의 필드를 통하여 확인할 수 있습니다.

- TRAN_STATUS=?

  - P, R, T 등 : 전송중 (일정시간 이상 지속될 경우 전송 중 오류 발생)
  - **C : 정상처리**

  - E : 오류

- FINAL_STATUS=?

  - P, R, T 등 : 전송중 (일정시간 이상 지속될 경우 전송 중 오류 발생)
  - **O : 정상처리 (발행취소상태)**

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















# 2.정매입

거래처(공급자)에서 세금계산서를 발행하면 수신승인, 수신거부의 상태변경을 할 수 있고, 거래처(공급자)에서는 발행 후 발행취소 할 수 있습니다.

![image-20201222164439961](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222164439961.png)



## 1)상태조회

#### 상태조회 프로세스

거래처(공급자)에서 발행이나 상태변경 시 세금계산서 데이터가 스마트빌서버->중계서버를 통해서 중계DB에 INSERT, UPDATE 됩니다.

![image-20201228105859585](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201228105859585.png)



#### 상태조회 화면구성

매입세금계산서를 조회하여 세금계산서의 상태를 확인할 수 있도록 화면을 구성합니다.

![image-20201228103312945](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201228103312945.png)



#### 상태조회 데이터 확인

거래처(공급자)에서 발행 시 아래 4개의 테이블에 각각 데이터를 입력됩니다. (명세서, 입력샘플 문서 참고)

- XXSB_DTI_**MAIN** //세금계산서 정보
- XXSB_DTI_**ITEM** //세금계산서 품목정보
- XXSB_DTI_**STATUS** //세금계산서 상태정보
- XXSB_DTI_**INTERFACE** //세금계산서 연동정보

거래처(공급자)에서 상태변경 시 STATUS에 데이터가 UPDATE, INTERFACE에 INSERT 됩니다.

STATUS테이블의 FINAL_STATUS를 조회하여 최종상태를 확인합니다.



## 2)상태변경

#### 상태변경 프로세스

거래처(공급자)에서 발행한 세금계산서에 대해서 수신승인이나 수신거부의 상태변경 데이터를 중계DB에 입력하면 중계서버->스마트빌서버를 통해서 상태변경이 처리되고, 처리결과가 중계DB에 UPDATE 됩니다.

![image-20201228084514604](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201228084514604.png)



#### 상태변경 화면구성

매입세금계산서의 상태를 조회하는 화면에서 수신승인, 수신취소 기능을 구성합니다.

![image-20201223112152714](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201223112152714.png)



#### 상태변경 데이터 입력

상태변경 시 STATUS에 데이터를 UPDATE, INTERFACE에 INSERT 합니다. (명세서, 입력샘플 문서 참고)

- XXSB_DTI_**STATUS**
  - DTI_STATUS='C' //수신승인
  - DTI_STATUS='R' //수신거부
- XXSB_DTI_**INTERFACE**
  - SIGNAL='CANCELALL' //수신승인
  - SIGNAL='REJECT' //수신거부



#### 상태변경 처리 결과 확인

중계서버에 설치된 스마트빌 모듈에서 INTERFACE 테이블을 주기적으로 POLLING 하다가 상태변경 데이터가 정상적으로 입력되면 상태변경 처리 후 처리 결과를 STATUS테이블에 UPDATE합니다.

정상 처리 여부는 STATUS테이블의 아래 2개의 필드를 통하여 확인할 수 있습니다.

- TRAN_STATUS=?

  - P, R, T 등 : 전송중 (일정시간 이상 지속될 경우 전송 중 오류 발생)
  - **C : 정상처리**

  - E : 오류

- FINAL_STATUS=?

  - P, R, T 등 : 전송중 (일정시간 이상 지속될 경우 전송 중 오류 발생)
  - **C : 정상처리 (수신승인상태)**
  - **R : 정상처리 (수신거부상태)**
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















# 3.역매입

공급자에게 세금계산서의 발행을 요청하는 프로세스로 역발행요청 후 역발행요청취소가 가능합니다. 공급자는 역발행요청을 거부하거나 발행할 수 있고 발행 후에는 '2.정매입'과 동일합니다.

![image-20201223112727520](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201223112727520.png)



## 1)역발행요청

#### 역발행요청 프로세스

세금계산서 역발행요청 데이터를 DB에 입력 시 중계서버 -> 스마트빌서버를 통해서 세금계산서 역발행요청이 전송되며 일정시간(약 10초)이 지난 후 역발행요청에 대한 처리 결과가 DB에 UPDATE 됩니다.

![image-20201223113042293](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201223113042293.png)



#### 역발행요청 화면구성

매입세금계산서를 직접 작성하거나 전표를 통해서 역발행요청하는 화면을 구성합니다.

![image-20201223113151878](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201223113151878.png)



#### 역발행요청 데이터 입력

역발행요청 시 아래 4개의 테이블에 데이터를 입력합니다. (명세서, 입력샘플 참고)

- XXSB_DTI_**MAIN** //세금계산서 정보
- XXSB_DTI_**ITEM** //세금계산서 품목정보
- XXSB_DTI_**STATUS** //세금계산서 상태정보
- XXSB_DTI_**INTERFACE** //세금계산서 연동정보

MAIN, ITEM, STATUS 테이블은 세금계산서 1건 당 1개의 record가 저장되고, INTERFACE 테이블은 세금계산서의 처리(역발행요청, 역발행, 상태변경)마다 record가 추가로 저장됩니다.



#### 역발행요청 처리결과 확인

중계서버에 설치된 스마트빌 모듈에서 INTERFACE 테이블을 주기적으로 POLLING 하다가 역발행요청 데이터가 정상적으로 입력되면 역발행요청 처리 후 처리 결과를 STATUS테이블에 UPDATE합니다.

처리 후 정상 역발행요청 여부는 STATUS테이블의 아래 2개의 필드를 통하여 확인할 수 있습니다.

- TRAN_STATUS=?

  - A : 전송전 (저장상태)
  - P, R, T 등 : 전송중 (일정시간 이상 지속될 경우 전송 중 오류 발생)
  - **C : 정상처리**

  - E : 오류

- FINAL_STATUS=?

  - A, P, R, T 등 : 전송중 (일정시간 이상 지속될 경우 전송 중 오류 발생)
  - **V : 정상처리 (역발행요청 상태)**

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



## 2)상태조회

#### 상태조회 프로세스

역발행요청된 세금계산서에 대해서 거래처(공급받는자)에서 역발행요청거부나 발행 시 스마트빌서버->중계서버를 통해서 중계DB에 데이터가 UPDATE됩니다.

![image-20201228092036138](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201228092036138.png)



#### 상태조회 화면구성

매입세금계산서를 조회하여 세금계산서의 상태를 확인할 수 있도록 화면을 구성합니다.

![image-20201228104521528](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201228104521528.png)



#### 상태조회 데이터 확인

상태변경 시 STATUS에 데이터가 UPDATE, INTERFACE에 INSERT 됩니다. (명세서, 입력샘플 문서 참고)

- XXSB_DTI_**STATUS** //세금계산서 상태정보
- XXSB_DTI_**INTERFACE** //세금계산서 연동정보

STATUS테이블의 FINAL_STATUS를 조회하여 최종상태를 확인합니다.







## 3)상태변경

#### 상태변경 프로세스

역발행요청된 세금계산서에 대해서 역발행요청취소 데이터를 중계DB에 입력하면 중계서버->스마트빌서버를 통해서 상태변경이 처리되고, 처리결과가 중계DB에 UPDATE 됩니다.

![image-20201228084514604](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201228084514604.png)



#### 상태변경 화면구성

매입세금계산서의 상태를 조회하는 화면에서 역발행요청취소, 수신승인, 수신거부하는 기능을 구성합니다.

![image-20201223112152714](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201223112152714.png)



#### 상태변경 데이터 입력

상태변경 시 STATUS에 데이터를 UPDATE, INTERFACE에 INSERT 합니다. (명세서, 입력샘플 문서 참고)

- XXSB_DTI_**STATUS**
  - DTI_STATUS='W' //역발행요청취소
  - DTI_STATUS='C' //수신승인
  - DTI_STATUS='R' //수신거부
- XXSB_DTI_**INTERFACE**
  - SIGNAL='CANCELRREQUEST' //수신승인
  - SIGNAL='CANCELALL' //수신승인
  - SIGNAL='REJECT' //수신거부



#### 상태변경 처리 결과 확인

중계서버에 설치된 스마트빌 모듈에서 INTERFACE 테이블을 주기적으로 POLLING 하다가 상태변경 데이터가 정상적으로 입력되면 상태변경 처리 후 처리 결과를 STATUS테이블에 UPDATE합니다.

정상 처리 여부는 STATUS테이블의 아래 2개의 필드를 통하여 확인할 수 있습니다.

- TRAN_STATUS=?

  - P, R, T 등 : 전송중 (일정시간 이상 지속될 경우 전송 중 오류 발생)
  - **C : 정상처리**

  - E : 오류

- FINAL_STATUS=?

  - P, R, T 등 : 전송중 (일정시간 이상 지속될 경우 전송 중 오류 발생)
  - **C : 정상처리 (수신승인상태)**
  - **R : 정상처리 (수신거부상태)**
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



> 거래처(공급자)에서 역발행 후에는 2.정매입 과 동일합니다.



# 4.역매출

## 1)역발행

#### 역발행 프로세스

역발행요청으로 수신된 세금계산서에 대해서 역발행 데이터 입력 시 중계서버 -> 스마트빌서버를 통해서 세금계산서가 발행되며 일정시간(약 10초)이 지난 후 역발행에 대한 처리 결과가 DB에 UPDATE 됩니다.

![image-20201223134625259](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201223134625259.png)



#### 역발행 화면구성

역발행요청으로 수신된 데이터를 통해서 역발행하는 화면을 구성합니다.

![image-20201223134755350](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201223134755350.png)



#### 역발행 데이터 입력

역발행요청되어 중계DB에 저장된 세금계산서 데이터에 대해서 STATUS테이블을 UPDATE하고 INTERFACE테이블에 데이터를 입력 합니다. (명세서, 입력샘플 참고)

- XXSB_DTI_**STATUS**
  - DTI_STATUS='I'
- XXSB_DTI_**INTERFACE**
  - SIGNAL='RARISSUE'



#### 역발행 처리 결과 확인

중계서버에 설치된 스마트빌 모듈에서 INTERFACE 테이블을 주기적으로 POLLING 하다가 역발행 데이터가 정상적으로 입력되면 역발행 처리 후 처리 결과를 STATUS테이블에 UPDATE합니다.

처리 후 정상 역발행 여부는 STATUS테이블의 아래 2개의 필드를 통하여 확인할 수 있습니다.

- TRAN_STATUS=?

  - P, R, T 등 : 전송중 (일정시간 이상 지속될 경우 전송 중 오류 발생)
  - **C : 정상처리**

  - E : 오류

- FINAL_STATUS=?

  - V, P, R, T 등 : 전송중 (일정시간 이상 지속될 경우 전송 중 오류 발생)
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



## 2)상태조회

#### 상태조회 프로세스

역발행된 세금계산서에 대해서 거래처(공급받는자)에서 수신승인, 수신거부의 상태변경을 할 수 있습니다. 상태변경 시 스마트빌서버->중계서버를 통해서 중계DB에 데이터가 UPDATE됩니다.

![image-20201228092036138](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201228092036138.png)



#### 상태조회 화면구성

매출세금계산서를 조회하여 세금계산서의 상태를 확인할 수 있도록 화면을 구성합니다.

![image-20201223113259430](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201223113259430.png)



#### 상태조회 데이터 확인

거래처(공급자)에서 상태변경 시 STATUS에 데이터가 UPDATE, INTERFACE에 INSERT 됩니다. (명세서, 입력샘플 문서 참고)

- XXSB_DTI_**STATUS** //세금계산서 상태정보
- XXSB_DTI_**INTERFACE** //세금계산서 연동정보

STATUS테이블의 FINAL_STATUS를 조회하여 최종상태를 확인합니다.







## 3)상태변경

#### 상태변경 프로세스

역발행 후 국세청전송되거나 거래처에서 수신승인, 수신거부의 상태변경을 하기 전에 발행취소를 할 수 있습니다. 발행취소 데이터를 중계DB에 입력하면 중계서버->스마트빌서버를 통해서 상태변경이 처리되고 처리결과가 중계DB에 다시 UPDATE 됩니다.

![image-20201228084514604](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201228084514604.png)



#### 상태변경 화면 구성

매출세금계산서의 상태를 조회하는 화면에서 발행취소 기능을 구성합니다.

![image-20201223113259430](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201223113259430.png)



#### 상태변경 데이터 입력

상태변경 시 STATUS에 데이터를 UPDATE, INTERFACE에 INSERT 합니다. (명세서, 입력샘플 문서 참고)

- XXSB_DTI_**STATUS**
  - DTI_STATUS='O'
- XXSB_DTI_**INTERFACE**
  - SIGNAL='CANCELALL'



#### 상태변경 처리 결과 확인

중계서버에 설치된 스마트빌 모듈에서 INTERFACE 테이블을 주기적으로 POLLING 하다가 상태변경 데이터가 정상적으로 입력되면 상태변경 처리 후 처리 결과를 STATUS테이블에 UPDATE합니다.

정상 처리 여부는 STATUS테이블의 아래 2개의 필드를 통하여 확인할 수 있습니다.

- TRAN_STATUS=?

  - P, R, T 등 : 전송중 (일정시간 이상 지속될 경우 전송 중 오류 발생)
  - **C : 정상처리**

  - E : 오류

- FINAL_STATUS=?

  - P, R, T 등 : 전송중 (일정시간 이상 지속될 경우 전송 중 오류 발생)
  - **O : 정상처리 (발행취소상태)**

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







# 5.국세청 전송

세금계산서 발행이 정상적으로 처리되면 익일(영업일기준) 스마트빌 서버를 통해서 자동으로 국세청 전송(오후2시-6시 사이)됩니다. 전송 결과는 기본적으로 하루에 두번(오전8시,오후 8시) 중계DB에 UPDATE 됩니다. 

예를 들어서 1월1일(월) 발행 시 익일인 1월2일(화) 국세청 전송되고 1월2일(화) 오후8시에 결과가 UPDATE됩니다.

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

국세청 전송 처리 결과에 대한 상세내용은 STATUS테이블의 아래 2개의 필드를 통하여 확인할 수 있습니다.

- RESULT_REQUEST, ERROR_MSG

  - **SUC001, 성공**

  - {ERROR코드}, {ERROR내용}

국세청 즉시전송이 필요한 경우 발행 시 INTERFACE테이블에 아래와 같이 데이터를 입력 합니다.

- META_STRING = '\<NTS>Y\</NTS>'

국세청 전송결과 조회 주기는 상황에 따라서 조절이 가능합니다. (스마트빌 담당자에게 요청)





