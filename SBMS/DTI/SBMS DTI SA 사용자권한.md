StandAlone에서는 XXSB_DTI_SM_COMPANY테이블에 저장된 회사정보와 XXSB_DTI_SM_USER 테이블에 저장된 사용자 정보를 사용한다. 각 사용자별로 MNGYN, EMAIL을 체크하여 보여지는 데이터가 결정되고 ARAPYN을 체크하여 로그인 시 초기화면이 결정된다. (admin계정은 예외적으로 회사정보화면이 초기화면으로 결정된다.)

초기 세팅을 위해서 최초 admin계정이 필요하다.

```sql
INSERT INTO XXSB_DTI_SM_USER ('admin', '', 'admin', 'admin', 'admin', 'Y', 'test@test.com', '', 'test@test.com', '')
```

admin계정을 통해서 회사정보와 사용자정보를 등록하고 관리한다.

별도의 관리자계정(MNGYN=Y)를 생성하여 관리도 가능하다.

사용자 계정별로 조회되는 데이터는 아래와 같다.

![image-20201211095626694](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201211095626694.png)