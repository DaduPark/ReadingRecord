# [투비소프트] Nexacro N 컴포넌트 활용
## 개발시 유의사항
- 서비스 경로 사용시 full경로가 아닌 prefixid사용 ex) this.transaction("svcSelect","SvcURL::select_emp.jsp",...)
 : 그래야 prefix cache를 사용할수 있고 나중에 유지보수가 쉬워진다.
 
- cache level 
  - none : 캐시 사용 x
  - session : 어플리케이션이 오픈시 빠져나가지 않을때까지 최초 값으로 가져옴
  - dynimic : 캐시값가져오기전에 변경여부를 확인하여 최초값이였던 값이 변경되지 않을때까지는 캐시값을 가져오고 변경되었을때는 새로 가져옴
  - static : 
  
  =>TypeDefinition의 User Service 중 Type이 JSP 즉 Data 통신을 담당하는 Service는 CacheLevel를 “none”으로설정
  
  
- 데이터 가져오는 부분 : 한번 호출로 N개의 dataset를 가져온다(여러개는 공백으로 구분) ex)this.transaction("svcSelect1","SvcURL::select_code.jsp", "","ds_dept=out_dept ds_pos=out_pos",..)

- 서비스 호출시 데이터가 없는 경우에도 반드시 layout이 필요하다 : this.transaction(..)를 통해 데이터를 넘겨줄때 데이터가 없더라도 컬럼값을 넘겨줘야한다.

- 형변환을 못해주는것들은 useclienLayout을 true로 변경해줌 ..?

- dataset 변경시 enalbleevent 속성을 false로 바꿔놓으면 엔진에서 자동으로 변경해주는 부분들을 일시정지하고 수정하면 성능에 무리가 안간다 (끝난후 다시 true로 변경)

- 데이터 매칭은 최대한 바인딩으로하고 안될경우 exp방법으로 하고 그래도 안될때 이벤트
:마스터 그리드를 선택시 상세 그리드가 보여야되는 경우 : dataset 중심적으로 생각하기 (click이벤트가 아닌 rowchange이벤트로 넣어주기_ 컴포넌트에 이벤트를 거는것이 아닌 dataset에 이벤트를 걸어줌)

--------------
## 컴포넌트 활용

### dataset 
  - 데이터를 테이블 형태로 관리하는 오브젝트 (sql문단위로만들어짐)
  - 내부에서 사용하는 데이터로 컴포넌트와 바인딩 하여 사용
  - 서버와 통신하여 데이터 주고받는 형식으로 사용
  - 데이터 수정, 삭제되면 변경 전 내용을 엔진을 통해 Origin Buffer에 저장
  - invisible object에 생성
  - 더블 클릭하여 편집
  - AppVaiables에 공통 dataset 생성하여 글로벌 데이터셋으로 만들수있음

-----
# <실습을통해 데이터셋 메소드 교육>
  - 데이터셋의 컬럼(Column) 개수와 레코드(Row) 개수, 컬럼명(Column ID)은 그리드의 개수가 아닌 실제 데이터의 dataset에서 가져옴(그리드에서는 컬럼을 없앤 경우가 있기때문)
  - Dataset.addColumn를 통해 컬럼 추가시 생략가능한 인자값이 있다.(size값 지정해주지 않으면 256 기본값이들어간다)
  - findRow와 findRowExpr은 찾은 값들중 가장 첫번째 값의 행번호만 찾음(lookup을 사용하면 행번호가 아닌 값을 가져옴)/extractRows은 여러값의 행번호를 배열 형태로 가져옴
	(findRow를 스타트 인덱스를 변경하면 extractRows처럼 사용할수있음_마이플랫폼에서는 extractRows가 없어서 이런 방법으로 씀)
  - 평균구한는 getCaseAvg는 옵션을 통해 평균에 포함시킬지 말지를 정할 수 있음
  - 정렬은 dataset의 keystring이라는 속성값에 정해주면된다.
  - 필터를 진행 후에도 getRowCountNF를 통해 필터되기전의 개수를 가져올수있다(NF:not filter)
  
  - <수정삭제>
  - getOrgColumn을 통해 변경되기 전 origin값을 가져올 수 있다(맨 처음 값만 가져올수있음_두번째변경되었던 값은 못가져옴)
  - inserRow는 이전 행에 추가되고 addRow는 마지막 행에 추가된다.
  - deleteMultiRows를 통해 여러 행을 삭제하는데 순차적으로 삭제되는것이아니라 거꾸로 삭제된다(첫번째 삭제한 값을 가져오면 마지막값을 가져온다.)
  - deleteMultiRows이 없던 마이플랫폼은 deleteRow로 거꾸로 지우는 로직을 만들어줬었다.
  - 데이터 변경여부를 체크하는법(변경됬는데 화면을 닫을때 저장하지 않고 닫을수 있기때문에 )
    - objDs.getDeletedRowCount() , row를  for 로 돌려 objDs.getRowType(i);값으로 확인
	- onbeforclose이벤트를 태워준다-> 내부엔진단에서 브라우저 마다의 confirm 처리를 해줌
	
  - <복사>
  - data set 복사할때 copyData하면 데이터만복사됨. assign로 복사하면 데이터타입까지 복사됨
  - 특정row만 복사할때 copyRow에 옵션파라미터를 지정해두면 원하는 컬럼만 복사가 가능하다.
  
  - <이벤트 발생순서>
  - 변경전 :can(cancolumnchange) / 변경후:on(oncolumnchanged) => 중복체크시에는 can으로 적용해줌
  - for문으로 데이터 여러건을 변경할경우 한 row당 can, on 이벤트가 모두 발생하므로 성능에 문제가 생길수 있음 
    => for돌기전 this.Dataset6.set_enableevent(false);을 사용하여 꺼주고 마지막 로직이 끝난 후 this.Dataset6.set_enableevent(true);로 변경해준다.
	
------
# <실습을통해 그리드 교육>
  - merge cell은 하나의 셀정보만 나타나고 merge cell(having cell)은 머지되고 두개의 정보가 보임
  - 머지할경우 컬럼의 인덱스와 셀의 인덱스는 매치가되지 않는다.
  - 그리드 내용 가져오기  this.Grid1.getCellProperty("body", i, "text") => (형식 =>  bind:텍스트내용)
  - 헤더값클릭시 해당 해더 기준으로 정렬(sort)를 하려면 헤더에 이벤트를 걸어준다.
  - 헤더값은 값이 바인딩 되지않기때문에 체크박스같이 값이 들어가야되는 부분에서는 넣어줘야함 ex) this.Grid2.setCellProperty("Head", 0, "text", nValue);
  
  - <그리드 속성변경_사이즈, 컬럼이동등 그리드 기능>
  - cellmovingtype(컬럼이동), cellsizingtype(사이즈 변경)의 속성들을 추가하여 기능들을 추가할 수 있다.
  - 더블클릭하여 editor모드로 가서 band 속성값을 바꿔주면 스크롤시 fix모드를 설정할 수 있다.
  - 엑셀출력 기능이 있을시 별도의 그리드 값을 추가하여 만들어주는것이 좋다
  - 설정저장의 기능으로 사용자마다의 바꾼 그리드 컬럼순서등이 저장될 수 있다.(대신 로컬스토리지에 저장되기때문에 브라우저가 바뀌면 적용이안된다_ 테이블에 저장시 가능하다.)
  
  - <트리형태>
  -데이터셋에 level컬럼과 sort컬럼이 있어야함다.
  - 그리드 속성에 displaytype : treeitemcontrol/ treeLevel에는 기준이되는 컬럼을 선택한다.
  - this.Grid4.getTreeStatus(nGridRow) 상태값으로 이벤트를 추가할수있다(눌렀을때 펼쳐지고 닫치고등등.)
  
  - <그룹핑>
  - keystring에 G로 시작하면됨 set_keystring에(G:+컬럼명)
  - .appendContentsRow("summary") 를 통해 합계를 추가(summarytype속성으로 위치 조정가능 : 맨아래, 맨위)
  
  - <너비조절, 스크롤등 속성값 수정>
  - suppress에 1을 주면 같은 값의 row가 합쳐져서 보임(셀 병합)
  - autofittype 속성으로 너비 자동조절 가능하다
  - selecttype 속성으로 셀선택 모드를 변경할수 있다
  - 데이터가 많을경우 스크롤 속성인 fastvscrolltype를 설정하여 보기쉽게 변경
  - addEventHandler을 추가하면 변경된 데이터가 바로 적용이된다(this.editInput.addEventHandler("oninput", this.fn_oninput, this);	)=>oninput은 데이터 변경해주는 함수
  
  - <편집모드>
  - default는 두번눌러야 편집모드 
  - autoenter속성으로 select로 설정하면 한번 눌렀을때 편집모드
  - 그리드에디터에서 포맷을 여러개 만들어서 그리드 속성중 formatid로 그리드 포맷을 변경할수 있다.
---
# <실습을통해 PopupDiv 교육>
  - <PopupDiv>
  - this.PopupDiv1.closePopup()를통해 닫는 메소드 사용할수 있다
  - 서브페이지(URL로 건 페이지)로 만들 수있는데 popup에서 데이터 를 넘길때 this.parent.parent.PopupDiv2.closePopup(arrRtn); 이런식으로 값에 접근을 해야한다.arrRtn는 array값
  - 기간 달력(시작종료달력)은 기본div가 아니므로 pop달력으로 하는것이 좋다.
    - 기본 popuptype을 none으로 바꾸고 ondropdown이벤트를 걸어줌
	- oncloseup 이벤트에 날짜 validation 걸어주면됨.
---
# <실습을통해 Form 교육>
  - this.all;을 사용하면 화면의 모든 데이터를 가져올 수 있다.(inner에 들어있는 하위컨포넌트들의 정보는 못 가져온다.)
  - 하위컨포넌트정보는 다시 그 부분을 .all 로 가져와야한다.
  ```javascript
	  this.fn_compList = function(pObj)
	{
		var arrObj = pObj.all; 
	//	var arrObj = pObj.components; 

		for(var i=0; i<arrObj.length; i++)
		{
			trace(pObj.parent + " : " + pObj.valueOf() + " : " + arrObj[i] + " : " + arrObj[i].name);
		
			var sType = arrObj[i].valueOf();
			if(sType == "[object Div]"){
				this.fn_compList(arrObj[i].form);
			}		
			else if(sType == "[object Tab]"){
				for(var j=0; j<arrObj[i].tabpages.length; j++){
					//trace( arrObj[i].valueOf() + " : " + arrObj[i].tabpages[j].name);
					this.fn_compList(arrObj[i].tabpages[j].form);
				}
			}
		}
	}
  ```
  
  - String을 object형식으로 바꾸는것 this.components(값)을 쓰면됨.(eval은 어떤코드라도 무조건 실행하므로 보안에 취약하다.)
  - 타이머 : setTimer / 타이머 종료 : killTimer
  - 팝업(모달_부모창에서 못 벗어남, 모달리스_부모창을 벗어날 수 있음) => 모달은 parent의 엔진을 공유해서 사용하므로 더 빠름/ 모달리스은 넥스크로 초기 엔진을 다시 올리기때문에 훨씬 느리다.
---
# <실습을통해 Common 교육>
  - 컴포넌트의 사이즈 얻고싶을때 : getOffsetWidth() (getPixelWidth()는 내가 지정한 width라 유동width 값은 얻을수 없다.)
  - new Button()등으로 컴포넌트를 소스로 생성할 수 있다.
  ```javascript
  var objBtn = new Button();
	objBtn.init("btn_Comp", 320, 380, 150, 100);
	this.addChild("btn_Comp", objBtn);
	//컴포넌트 안에 생성하려면 스코프를 지정해준다.
	//this.Div2.form.addChild("btn_divComp", objDivBtn); 
	objBtn.set_text("Created Button");
	objBtn.set_background("skyblue");
	objBtn.show();	
  ```
  - 컴포넌트 삭제 
  ```javascript
  var objBtn = this.removeChild("btn_Comp"); 
	objBtn.destroy(); 
	objBtn = null;
  ```
  - 이벤트 추가 : this.btn_Comp.addEventHandler("onclick", this.fn_temp, this);	
  - 동적 바인드 처리
  ```javascript
    var objBindItem = new BindItem();
	objBindItem.init("item00", "edt_bind", "value", "Dataset2", "COL_NAME");
	this.addChild("item00", objBindItem);
	objBindItem.bind();
   ```
-------
  
- 소스 폴더를 복사해오면 typeDefinition의 User Service에 폴더 위치 추가하고  
해당 소스를 제너레이트해준다(해당 폴더 오른쪽클릭하여 제너레이트)
  
-----
   - https://www.playnexacro.com/#list:demo:1에서 데모사이트들을 보면서 개발에 참고할수있다.(넥사크로에서 제공해주는 기본 기능이 아니라 구현한 결과물이라 고객사에 설명을할때 이 사이트를 참고하여 기능을 구현할 수 있다고 말하게되면 낭패를 볼 수 있다.)
   - 유트브에 투비소프트를 쳐서 넥사크로를 찾아보면 강의를 찾아볼 수 있다.
   - 투비소프트사이트의 QnA 를 통해 질문을 할 수 있다.
----
http://support.tobesoft.co.kr/Support/?menu=home
  
 
