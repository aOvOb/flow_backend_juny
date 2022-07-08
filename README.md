<br>
<br>

<!-- [리팩터링한 소스 링크](https://github.com/aOvOb/flow_backend_juny_refactoring) -->

<br>

### 수정사항
> MVC패턴 적용[(소스 링크)](https://github.com/aOvOb/flow_backend_juny_refactoring), AWS-RDS 적용, docker와 oracle-cloud instances를 이용한 서버 호스팅  
링크 👉 [http://aovob.r-e.kr:1401/](http://aovob.r-e.kr:1401/) 

<br>
<br>

---

<br>
<br>  

### 안녕하세요 서준희입니다.
###### 
###### 먼저, 좋은 기회를 만들어 주셔서 정말 감사합니다.
###### node.js와 express를 이용하여 이번 과제를 수행하였습니다. (node 14, npm 6.14)
###### dataBase는 mysql@5.7 을 사용했습니다.
  
<br>
<br>
<br>
<br>
   
##### erd 입니다.
######    
###### ![erd](image/erd.png)
###### 
###### fw_user, fw_ext_ban, fw_ext_ban_fixed 총 3개의 테이블을 만들었습니다.  
###### 공통필드는 SYS_ID, SYS_FLAG, SYS_CREATE_DATE, SYS_MODIFY_DATE로 총 4가지 입니다.    
###### SYS_ID는 primary key로 데터 고유 아이디입니다. UUID에서 하이픈을 지운 값을 return해주는 getNewID라는 함수를 만들어 insert될 때마다 고유 아이디를 부여하게 만들었습니다.  
###### SYS_FLAG는 논리삭제를 위한 값 입니다. SYS_FLAG가 1이면 활성, 0이면 삭제 입니다. 물리적인 삭제가 없기때문에 모든 데이터가 기록에 남아, 데이터 CRUD에 대한 로그를 따로 만들지 않기위해 구현하였습니다.
###### SYS_CREATAE_DATE는 데이터가 생성된 날짜입니다. 
###### SYS_MODIFY_DATE는 데이터가 수정된 날짜입니다.
###### FK_USER_ID는, fk_user테이블의 데이터와 관계mapping을 위해 만든 column입니다. user의 SYS_ID값이 저장되며, 추후 로그인 기능이 구현될 때 회원간의 데이터 구분이 필요하다고 생각되어 구현하였습니다.


<br>
<br>
<br>
<br>
<br>
<br>


#### 실행 방법
###### 1. git clone 후 flow_backend_juny/server 경로에서 npm i 실행  
###### DB생성정보 
> host: 'localhost',  
  user: 'root',  
  password: '',  
  database: 'flow',  
  port: 3306
###### 2. clone받은 폴더에 있는 flowDDL의 내용을 DB에서 실행  
###### 3. flow_backend_juny/server 경로에서 npm start실행
###### 4. localhost:9298 접속




<br>
<br>
<br>
<br>
<br>
<br>


#### 요건
<!-- ![고정 확장자 구현 소스]() -->
#### 1-1 고정 확장자 구현
###### 커스텀 확장자 차단 영역에 출력되지 않기 위해 새로운 테이블을 하나 더 만들었습니다.
###### checkbox 클릭시 바로 저장, 업데이트 되는 로직을 만들고 싶었는데, 여러개의 체크박스의 이벤트를 컨트롤 하는 방법을 몰라, 화면 구현에 적지 않은 시간을 썼습니다.  
<!-- ###### checkbox 클릭시 해당 영역의 value가 변경되도록 만들었는데, 생각했던 것과는 다르게 클릭하는 순간 onclick이벤트를 발생시키고, 값을 변경하여 의도한것과는 반대로 작동하였습니다.  -->
<!-- ###### 체크박스별 이벤트 발생시키는 법을 몰라 결국 체크박스 각각 아이디를 만들고 각각 onclick event를 매핑해주는 소위말하는 노가다를 했습니다. -->

<br>

<!-- ![확장자 최대 입력 소스]() -->
#### 2-1 확장자 최대 입력
###### html태그를 이용해 20자로 제한하였고, input받을 때 서버에서 length check를 합니다. 또한 data type을 varchar(20)으로 설정하였습니다.  

<br>

<!-- ![저장로직 + 추가된 테이블 그리는 소스]() -->
#### 2-2 추가버튼 누르면 저장
###### fetch함수를 이용해 데이터를 가져왔고, 가져온 데이터의 길이만큼 forEach문을 실행해 화면 중앙에 구현한 html table을 추가하는 형식으로 구현하였습니다.  
###### 저장할 때 input된 값과 같은 값 (중복)이 있다면 경고창이 뜨게 했습니다. 
###### sql injection을 대비해 영어와 숫자만 입력 가능하게 하였고, 서버에서 특수문자를 공백으로 치환합니다  

<br>

<!-- ![arr length check 소스]() -->
#### 3-1 custom extension 200개 제한
###### return받은 데이터의 길이를 확인 후 200을 초과한다면 에러메세지를 띄우게 하였습니다.  

<br>

#### 3-2 확장자 옆 X 버튼
###### 화면 구현에 실패해서 delete버튼으로 대체하였습니다.  
###### 2-2에서 html table (조회된 데이터 출력)을 생성할 때 html내부에 해당 데이터의 SYS_ID를 삽입하였습니다. delete버튼 클릭시 해당 SYS_ID로 delete함수를 호출해 삭제합니다.  
###### edit버튼도 동일한 방법으로 구현하였습니다. edit버튼은 현재 주석처리 되어있으며,  
###### flow_backend_juny/server/client 경로의 [index.js 183번 라인], [index.html 59번 라인] 주석 해제시 update버튼이 화면에 출력됩니다.

<br>
<br>
<br>
<br>

#### 생각
###### 만들고싶은 기능은 많았는데, 시간분배를 잘못해서 구현하지 못한것이 많아 아쉽습니다. 
###### 처음 계획은 개발 후 소스 리팩터링 하는 것이었는데, 시간분배에 실패해 리팩터링을 하지 못한 것이 아쉽습니다. 혼자 제로베이스부터 개발하는 것은 이번이 처음이라고 할 수 있는데, 개발 초기에 폴더 구조를 잡고 개발을 시작해야한다는 것을 몸소 배웠습니다.  
###### docker를 이용해 이미지를 만들고, 개인 서버(오라클 클라우드)에 올렸습니다. 로컬에선 문제가 없던 소스를 도커로 띄워보니, 에러가 발생하는 것을 확인했습니다.  
###### 이유는 동기/비동기 함수에 대한 error handling을 제대로 하지 못했기 때문이었습니다. 당분간 FLOW에 제출한 이 소스로 공부 할 생각입니다.
###### 더 많은 얘기를 하고싶지만, 만나서 말씀드리고 싶습니다.  
###### 감사합니다 서준희 드림.  

