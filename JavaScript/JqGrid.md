## 주기능 : 리스트 데이타를 그리드 테이블에 맵핑

jQueryUI의 테마를 사용

jQueryUI, jQuery, jqGrid 관련 스크립트 로딩후 사용

### HTML CODE
	<table id="jqGrid"></table>
	<div id="jqGridPager"></div>
pager는 안쓸꺼면 생략 가능 (페이징처리정보나 로딩 row수 정보)

--------------------------------------

### Script CODE 예제 - 
	$("#comGrid").jqGrid({  
    	url: 'user/selectAdmNotice',  -- 호출 url
    	styleUI : 'Bootstrap',
    	mtype: "GET",              -- POST or GET
    	datatype: "json",          -- datatype설정 local이면 호출 안함
    	postData : {               -- 넘겨줄 parameter 설정
			id : "id",
			title : "title"
		},
    	colNames: ["ID","제목","내용","게시시작일자"],  -- 테이블 헤드 즉 컬럼 명 설정 여기서 헤드 설정시 colModel의 label생략
    	colModel: [                             -- 각 컬럼을 정의한다 
    		{ label: 'ID', name: 'id', key: true},
    		{ label: '제목', name: 'title',  width: 400 },
    		{ label: '내용', name: 'ctnts'},
    		{ label: '게시시작일자', name: 'stDt'},
    	],
    	caption:'관리자 공지사항',
    	rownumbers: true, -- row번호 출력여부
		viewrecords: true,
		cellEdit : false,
		loadonce:true, -- 이옵션을 true로 주면 한번 로딩 후 datatype를 local로 바꾼다. 즉 더이상 로딩 되지 않는다.
				로딩된 데이타를 정렬할때 사용. 페이징 처리시 false, 새로 로딩 필요시 datatype을 변경후 reload시도 
		multiselect : false, 여러 row 선택가능여부
    	width: 500, -- 너비 : 단위 픽셀
		height: 250, -- 높이 : 단위 픽셀
    	rowNum: 10,  -- 출력될 row갯수 10줄 출력
    	pager:"#jqGridPager",  -- pager 출력될 위치 지정
    	jsonReader:{           -- 리스트가 아닌 객체가 넘어올 경우 출력할 리스트를 설정 및 기타 param값 설정  
    		root:"list",       -- root는 리스트를 선택한다
    		total:"total",     -- total 은 총 페이지수
    		records:"records"  -- recors 는 총 row수 
    	},
    	loadComplete	: function(data) {  -- 로딩 완료후 수행되는 함수
    		console.log(data);  
    	},
    	grouping:true,   -- 그룹핑 여부
		groupingView : {
			groupField : ['all','region'],   -- 그룹으로 묶을 name
			groupText : ['{0}', '{0}'],      -- 그룹 이름 {0}은 그 값자체 의미
			groupColumnShow : [false,false], -- 해당 컬럼을 그리드에 나타낼건지 여부
			groupCollapse : true,            -- 로딩후 접혀있는지 여부
		}
	});
	
### colModel Option
label    : string  --> 컬럼명 설정 conNames와 같은기능  
name     : string  --> 받은 리스트에서 출력할 값 이름  
key      : boolean --> rowid 지정  
width    : number  --> 컬럼 너비 지정 (px)  
align    : string  --> 정렬 left, center, right  
hidden   : boolean --> 해당 컬럼 숨김 여부
editable : boolean --> 이 컬럼을 추가 및 수정시 사용할지 여부  
formatter: mixed   --> checkbox,select 등 여러 옵션 존재, 커스텀 가능  
>example code 

	function useFmatter(cellvalue, options, rowObject) {
	switch (cellvalue) {
		case "N":
			return "미사용";
		case "Y":
			return "사용";
	}
	return cellvalue;
	}
edittype : string  --> textarea, select, number 등  
editrules: array   -->  
>example code
	
	editrules:{edithidden:true, -- row가 hidden이면 에디팅이 안되지만 이옵션을 주면 가능
		required:true,  -- 필수입력값 설정
		number:true,email:true,date:true,time:true, -- 값 검증 옵션
		custom:true,  -- 커스텀 함수 사용여부
		custom_func: cfunc,  -- 커스텀 함수 정의하고 해당 이름 지정
		minValue:1,  -- 최소값(넘버)
		maxValue:100 -- 최대값(넘버)
	}
	-- 커스텀 함수 정의 예시 --
	function cfunc(value, colname) {
		if (value < 0 || value >20) 
			return [false,"Please enter value between 0 and 20"];
		else 
			return [true,""];
	}
	
editoptions: array -->  
>example code

	editoptions: {value:"Yes:NO",  -- checkbox경우
		value:"1:One;2:Two",  -- select인 경우
		defaultValue: "222",  -- 기본값 지정, js 함수호출 가능  ex: getdate()
		dataInit:function(e){$(e).datepicker();} -- 데이터 입력 함수 지정
	}
----------------------------------
### 자주 사용하는 함수 모음
	
그리드 리사이징

	$(window).on('resize.jqGrid', function () {
		jQuery("#jqGrid").jqGrid( 'setGridWidth', 400 );
	});

추가
	
	$("#jqGrid").jqGrid(
		'editGridRow'
		,"new"
		,{	 width:600
			,dataheight:300
			,url:"/admin/addEth"
			,closeAfterAdd:true
			,reloadAfterSubmit:true
			,beforeInitData:function() { -- 로딩전 값 설정
				$("#jqGrid").setGridParam({datatype : "json"});
				$("#jqGrid").jqGrid('setColProp', 'ethCd')
			}
			,afterSubmit: function() {  -- 전송후 수행되는 함수
				return [true,""]; -- 리턴값 배열
			}
		});

수정

	$("#jqGrid").jqGrid(
		'editGridRow'
		, 1      -- id
		,{	url:"/admin/updateEth"
			,width:600
			,dataheight:300
			,reloadAfterSubmit:true
			,closeAfterEdit:true
			,beforeInitData:function() {
				$("#jqGrid").setGridParam({datatype : "json"});
				$("#jqGrid").jqGrid('setColProp', 'ethCd'});
			}
		});
삭제
	
	$("#jqGrid").jqGrid(
				'delGridRow'
				,userId  -- id값으로 넘어감
				,{	
					url:"/admin/deleteUser"
					,beforeShowForm: function() {  --로딩전 값 설정
						$("#jqGrid").setGridParam({datatype : "json"});
					}
					,delData : {  -- 넘겨주는 데이터
						"ethCd" : ethCd
					}
					,reloadAfterSubmit:true
					,width:500
					,msg: "삭제하시겠습니까?"  -- 확인 메세지
				});
그리드 클리어

	$("#jqGrid").jqGrid("clearGridData");
선택해제
	
	$("#jqGrid").jqGrid("resetSelection");
선택된 로우 데이터
	
	var selectedRowId = $("#jqGrid").getGridParam('selrow'); -- 선택된 row id return	
	var row = $("#jqGrid").getRowData(selectedRowId);  -- 선택된 row data가져오기
	row.컬럼네임   -- 선택된 row 특정 컬럼데이터
	$('#jqGrid').getCell(selectedRow, '컬럼네임');  -- 위와같음
특정 셀값 가져오기
	
	var list = $("#jqGrid").jqGrid("getCol", "특정셀이름"); -- 배열 리턴
그리드 설정 값 변경
	
	$("#jqGrid").setGridParam({datatype : "json"}).trigger("reloadGrid");
	-- 마지막에 reloadGrid는 리로딩 트리거 주로 값 변경해주고 재로딩 할때 사용
	-- setGridParam안에 들어가는설정값은 처음 그리드 설정할때 값과 같다
그리드에 row 추가
	
	$("#jqGrid").jqGrid('addRowData'
	,"total" -- rowId설정(고유한값)
	,{"traitCd":"총계","korCtnts":"","yesAns":sumYes,"noAns":sumNo,"sumAns":sumTotal}
	    -- 추가될 데이터 기본 추가된 컬럼수에 맞춰 입력
	,"last");  --추가될 위치 (+ first)
그룹 헤더 설정
	
	$("#jqGrid").jqGrid('setGroupHeaders', {
	    	useColSpanStyle: true,
	    	groupHeaders : [
	        	{startColumnName: 'yesAns', numberOfColumns: 2, titleText: '남성'},
	        	{startColumnName: 'yesAns_W', numberOfColumns: 2, titleText: '여성'}
	        ]
	    })
