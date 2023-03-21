
# [투비소프트] Nexacro N 기본
## <넥사크로스튜디오 구조 설명>
1. environment
  - varialbles :  localbrowser에 저장되는데 생성된 변수는 지워주지 않은 이상은 계속 남게됨(보안에 문제가 있을수있음), 평문으로 남음
  - cookies :  세션 변수는 jsessionid에 정의해줌(값은 서버에서 넣음)
  - http header : 추가적인 해더값을 넣고 싶을때 추가하여 사용
  - script

2. typeDefinition 
  - object  
    - modeules : 엔진코어
    - objects : 사용하는 컴포넌트들 (기본은 가장많이 사용되는 컴포넌트들_성능적 이슈로 사용할 컴포넌트들만 추가해서 사용, 나머지들은 추가해서 사용_modules의 comcomp.json에서 +를 눌러 추가해줌)
  - services : 
    - Resource Service : 디자이너, 퍼블리셔가 사용하는 곳 
    - User Service : 일반개발자는 하단 user service /화면, 자바스크립트등의 필요한 디렉토리를 관리하는곳(+눌렀을때 form, js, file 는 디렉토리 관리 나머지는 서비스 url연결)
  - protocalAdaptors :  기본 프로토콜을 설정하지 않으면 http프로토콜을 사용하지만 아닐경우 프로토콜을 등록해서 사용해줌
  - DeviceAdaptors: 기본적으로 데이터 입출력의 키보드나 마우스를 제공 하고 추가적으로 음성인식이나 제스처를 추가로 제공해줌

3. Application infomation
  - datasets: 2차원적으로 변수를 지정해주는것으로만 인지하고있으면됨
  - apllction variavles _ variables : 해당프로젝트의 글로벌 함수정의(environment에서의 varialbles는 localbrowser에 저장되므로 지워주는곳이 있어야하지만/ 메모리에만 저장되어있어서 지워주는 로직은 없어도됨)

application_application_desktop==> 자동으로 생성되었던 소스들
--------
## <넥사크로스튜디오 기본설명>
- 자바스크립트 작성 시 스코프 지정해줘야함(this)

- quick view로 실행
  - necacro emulator을 통해 모바일에 사용되는 사이즈 를 입력해서 실행환경을 확인해볼수있음
  - 현재 내가 깔려있는 5대 브라우저선택할 경우 해당 브라우저로 띄어짐


- trace는 어플리케이션 영역이라 스코프를 nexacro.getApplication()로 잡아줘야함.
```
ex)
 nexacro.getApplication().trace("안녕");
 var objApp = nexacro.getApplication();
	objApp.trace("안녕");
```


- 속성의 값을 바꿀때는 set_ 를 써주는 거임. 값을 꺼낼때는 get_를 쓰지 않고 속성값만 써줌.
```
this.Button00.set_text("TOBESOFT");
alert(this.Button00.text);
```
	
- alert, trace는 예외로 스코프를 따로 안써줘도됨.(써주는것이 정석이긴함)


- 실제 개발업무에서는 개발을 먼저하고 표준으로 바꿔야할때가 있음=> 표준(명명규칙)에 따라 아이디를 만들어야함 ex) 저장(버튼 : btn-) :btnSave
```
property의 id만 바꾸면 오류가 발생한다: this.Button00.set_text("TOBESOFT"); 이런 값이 안바꼈기 때문에..
그러므로 this.Button00.set_text("TOBESOFT") 식이 아닌 this.Button00_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)의 첫번째 파라미터인 값을 이용하여
obj.set_text("TOBESOFT"); 이런식으로 짜야 수정할때 쉬워짐.
```

------------
## <변수선언>
1. var var1 =""; form변수라 스크립트 영역에서만 사용하는 변수임.(다른 폼에서 참조 할수없음 해당스크립트에서만 접근 가능함.)
2. this.var2=""; 어느곳에서 접근할수있는 form의 멤버변수 _ 폼과 폼끼리 사용가능
3. var3  =""; (생략가능한 변수) 글로벌 변수로 form을 닫아도 계속 메모리에 남게 됨. 그러므로 이런건 사용하지 않는것이 좋음._ 마이플렛폼에서는 생략한 변수가 this.var2처럼 되었기때문에 상관 없지만 넥사크로에서는 글로벌로 잡혀버리게된다.


----------------
## <제너레이트 설명>
프로젝트 오른쪽 눌러 open contain folder을 눌러보면 xml로 생성된것을 볼수 있음
.xfdl으로만 되면 xml이므로 실행이 안된다. 
그러나 저장을 할때 output창을 보면 소스가 Generating되어 js파일로 생성해줌 이 js파일로 브라우저에서 볼 수 있었던거임(xfdl와 똑같은 구조로 js로 만들어진것을 볼수 있음)_
nexacorlib도 생성되는데 엔진코어도 같이 제너레이트가 되어 정상적으로 출력이 되는거임.
index.html에서 엔진라이브러리를 로딩하고 만든 js가 실행되므로 정상적으로 출력되는것임.
=> 소스 수정은 js가 아닌 xfdl을 수정하는거임

-----------------------------------
## <전체 제너레이트 방법>
상단 Generate-application 클릭

기존 파일 지우고 다시 제너레이트 하는법
상단 Generate-regenerate-application 클릭

실행 : launch클릭하여 브라우저 선택

최종적으로 만드는 목표 : 왼쪽 form의 employees 부분임.

------------------------------------------------
## <코드 스니핏 사용 code snippe>
- 프로그램 시작은 주석으로 시작(script)
```javascript
/***********************************************************/
/* 프 로 그 램 : Hello_Sample.xfldl
/* 작 성 일 자 : 2020.04.01
/* 작  성   자 : 홍 길 동
/* 설       명 : 
/***********************************************************/
```
=> 이런건 코드 스니펫 기능으로 편히 할수 있음 마우스로 드래그 후 복사하고 오른쪽클릭 후  code snippet에 등록 후 하고 name을 입력하면 자동 생성됨
=> 익스포트해주면 공유가능하다

## <스크립트 설명>
- 주요단축키 설명
  - 블럭이동 : Ctrl+]
  - 블럭선택 : Ctrl+Shift+]
  - 블럭주석 : Ctrl+/
  - 주석해제 : Alt+/
  - 소스정리 : 전체 선택 마우스 오른쪽클릭 - auto indent


-----------------
## <Desingn>
-property
  - 노말체: 엔진에서 기본 제공
  - 볼트체 : 사용자가 변경한것
  - 프라퍼티 하단에 한줄 설명이 나오는데 f1누를시 상세 설명이 나온다(속성에따라 사용할수 있는 환경과 미지원환경을 확인할수 있다)


-------

## <컴포넌트들 설명>

- div컴포넌트는 여러 컴포넌트를 포함할 수 있는데 포함된 컴포넌트는 스코프가 달라진다.   
ex)div00.form.div01.form.button00  

- 이때 포함된 스코프에서 벗어나면 안보이게되는데 해당 상위 컴포넌트를 선택하면된다.

- 하위 컴포턴트를 클릭후 esc를 누르면 상위 컴포넌트가 선택된다

- 하위컴포넌트를 마우스 오른쪽클릭하고 copy id를 선택하면 상위 스코프도 다 복사할수 있다.
  ex)div00.form.div01.form.button00  이런식으로 앞부분 복사됨

- 컴포넌트 복사할때 ctrl+shift+v를 하면 이벤트를 빼고 복사할수 있다
(컴포넌트 복사시 적용된 이벤트들 딸려와서 복사되는것 방지)

- 정렬 : 마우스 드래그 후 상단 align 버튼을 눌러 정렬해줌 (없을경우 오른쪽 누르고 추가해줌)
(tools-obtion- form design - seelct part 스치기만 해도 선택됨)

- visible 끄면 해당버튼의 기능을쓸수 없는데 안보이게 하고 버튼의 기능은 사용하고 싶을때   
  : move로 -좌표를 보이게 하고 -좌표안에다가 버튼을 옮겨준다

- 탭 개발 시 : 마우스오른쪽으로 next tap으로 누르면 다른 탭을 볼수있음


----------------------------
## <nexacoro education화면 보고 바로 개발>
- 에뮬레이터에서 reload하면 바로 수정된 소스가 바로 적용된다.(껏다 킬 필요 없음)
- 해당화면에서 view source누르면 해당화면을 바로 수정할수 있는 화면으로 이동
---------
## <컴포넌트 몇개 설명> (나머지는 샘플소스를 통해 컴포넌트들을 볼수 있음)
- maskEdit
  - type 속성 :들어올값의 형식
  - fomat 속성 : 문자의 형식(아스타처리 {}로 감싸줌)
=> 로그인페이지에 비밀번호 구현시 마스크에디트(길이가 지정되어있음)가 이닌 에디트 컴포넌트 사용하여 개발_password속성값을 true로 바꿔주면 모든 값이 아스타처리됨

- div
  - url 속성에서 링크를 선택해서 개발할수있다(재사용, 성능튜닝면에서 유용하게 개발할수있다)

- tab
  - tab도 동일하게 url 속성에서 링크를 선택해서 개발해야함
	  (=> 로딩될때 모든탭을 로딩후 완료하는 것이아니라 tab1만 로딩된 후 완료 처리하므로 더 빠르다(tab2부터는 사용자가 누를때마다 로딩됨))
  - preload true로 바꾸면 모든탭을 로딩 후 완료됬다고 됨(성능이슈로 권하지 않음)
  
- 콤보박스
  - 콤보박스 같은 값 셋팅 : bind innerdataset을 통해 콤보박스 값 셋팅하고 연결되는 값을 binding 시켜줌

- 라디오 박스
  - 라디오 값 셋팅 :
    - property에서  innerdataset값에 셋팅
    - 기본셋팅이 값이 1개로 되어있어 columncount를 통해 라디어개수 적어줌

==> 코드가 고정일때는 innerdataset값을 통해 셋팅하고 유동적으로 바뀔수 있는 경우 bind innerdataset를 통해 db에서 값을 가져와서 값 셋팅

-------------------------
## <실습연습>
1. 컨포넌트에 디자인 적용하는 연습
- 디자인되어있는곳에서 cssclass의 css명 복사후 원하는 곳에 복사
2. 정렬하는 엽습
- align을 사용해서 정렬
3. 컴포넌트 그리기
- 그리고 스코프확인하여 잘되는 지 확인
------------------------------
## <데이터 넣기>
- 상단 컴포넌트에서 dataset 클릭

- 상단의 colums부분을 적어주고 하단 rows에서  샘픍데이터 추가(복붙가능함 많이 만들기)

- 바인딩하는법
  - 마우스로 끌어다가 넣고 해당 아이디 선택 
  -  properties의 bindable의 value속성에서 추가

- 포맷이 안맞을경우 invalid value가 나옴(mask edite는 type과 format을 맞춰줘야함)

- 자바스크립트 없이 속성 값으로만으로 처리가능한 것들
  - properties의 bindable의 visible속성을 통해 자바스크립트 없이 동적으로 처리가능  
   (체크박스 있을시 날짜컴포넌트 보이고안보이고 여부 처리)
  - background속성으로 값이 있을때만 색깔이 있게 하는것도 가능함

--------------------------------
## <그리드>
더블클릭하여 editor팝업을 띄움
- 속성값 설명
  - header 속성값 text를 통해 타이틀 변경
  - distplaype 사용자가 보는 시점(combo)
  - edittype : 수정된 시점
  - cellMaskEdit를통해 형식 정함
  - textAlign : 정렬

- 콤보컬럼 속성 셋팅 : distplaype :comboText / edittype: combo/ combodataset: 테이블선택/ combocodecol : 코드값선택/ combodatacol : 보이는 값

- 체크박스 속성 셋팅 : distplaype :checkboxcontrol / edittype: checkbox
---------------------
## <grd Expr(기존 bind된 값들을 가지고 연산하여 표현)>
- body 속성값 text의 set expretion
- dataset.getRowCount() + '건' : 모든 건수 
- currow + 1  : row카운트
- dataset.getRowCount()-currow : row건꾸로
- dataset.getSum("SALARY") : 합계
- GENDER =="M" ? "남성" : GENDER =="W" ? "여성" : "기타" : 삼항연상
- 컬럼 + 컬럼 : 컬럼값들 합치기
- comp.parent.함수 : 자바스크립트에서 만든 함수 값

-------------------
## <데이터 통신>

- 넥사에서 고객사쪽에 서버에 x-api 설치해줌?


- 조회 예시 코드


```javascript

//<조회>
this.btn_retrieve_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
{
	this.transaction( "strSelect",//서비스 구분자
	"SvcURL::select_emp.jsp?a=b&c=d",//호출할 서비스 URL 경로(typeDefinition의 User Service 값으로 넣고 그 id 가져옴)
	"",//저장 server ds = client ds
	"ds_emp=out_emp",//조회 client ds = server ds
	"a=b c="+nexacro.wrapQuote("d e"), //전달값  스페이스바 존재시 nexacro.wrapQuote("d e") 사용
	"fnCallback");//서비스끝
	
};

this.fnCallback = function(svcid, ecd, emsg)///첫번째 파라미터 : 서비스 구분자
{
	if(ecd >= 0)
	{
		if(svcid == "strSelect")
		{
			alert(this.ds_emp.getRowCount() + '건');
			return;
		}else if(svcid == "strSave"){
			alert("저장성공")
			return;
		}
	} else {
		alert("오류 :" + ecd + ":" + emsg);
	}
	
}
this.btn_add_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
{
	this.ds_emp.addRow();
	this.ds_emp.setColumn(this.ds_emp.rowposition,"HIRE_DATE","20230321" );
};

this.btn_del_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
{
	this.ds_emp.deleteRow(this.ds_emp.rowposition);
};

this.btn_save_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
{
	this.transaction( "strSave",//서비스 구분자
	"SvcURL::save_emp.jsp",//호출할 서비스 URL 경로(typeDefinition의 User Service 값으로 넣고 그 id 가져옴)
	"in_emp = ds_emp:U",//저장 server ds = client ds => U는 신규 수정된 값만 서버로 넘기겠다는 의미 or A는 모든 값 넘기기
	"",//조회 client ds = server ds
	"a=b c="+nexacro.wrapQuote("d e"), //전달값  스페이스바 존재시 nexacro.wrapQuote("d e") 사용
	"fnCallback");//서비스끝

};
```

-----------
## <sync동기 / async비동기 통신>

- sync (성능에 영향을 줌)
Transaction함수 호출 후 서버에서
응답이 완료되는 시점까지 대기 후
결과를 받으면 다음 스크립트 진행
```
this.transaction("svcSelect"
,"http://127.0.0.1:4098/select_emp.jsp"
,"“
,"ds_emp=out_emp“
,"“
,"fn_callback“, false);

// trace 실행되지 않고 transaction 완료까지 대기
trace(this.ds_emp.getRowCount()); // 서버 건수 확인가능

```

- async(이 방식을 권장)
Transaction함수 호출 후 통신완료와 상관없이
다음 스크립트를 수행하며,
서버에서 결과를 리턴 받게 되면
callback함수가 호출됨
```
this.transaction("svcSelect"
,"http://127.0.0.1:4098/select_emp.xml"
,"“
,"ds_emp=out_emp“
,"“
,"fn_callback“, true);

//바로 trace 실행문 됨
trace(this.ds_emp.getRowCount()); // 서버 건수 확인불가
this.fn_callback = function(svcID, errCode, errMsg)
{
trace(this.ds_emp.getRowCount()); //서버 건수 확인불가
}
```

---------------
## <Transaction 시 전송되는 데이터 형식>
- 넥사크로 수정
var nDataType = 0; // (0: XML타입, 1: 이진 타입, 2: SSV 타입, 3:JSON 타입)
scope.transaction(sRealSvcID, sURL, sInputDsNm, sOutputDsNm, sArgument, sCallbackFunc, bAsync,
nDataType, false);


----
## <cross domain>
화면에서 바라보는 url과 데이터 통신의 url이 다르면 동일출처정책(cross domain)에 위배되어 통신이 되지않는다. 
하지만 nexcoro runtime environment를 사용해서 테스트하거나
run environmentt속성에서 local을 선택하면 테스트시 위배를 피할수 있다.

-------
## <패킷확인>
Fiddler 를 통해 클라이언트에서의 데이터와 서버의 데이터를 객관적으로 볼수 있기때문에 디버깅이 좀더 쉽다.
