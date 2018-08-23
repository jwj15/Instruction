# 주기능 : 리스트 데이타를 그리드 테이블에 맵핑

## jQueryUI의 테마를 사용

## jQueryUI, jQuery, jqGrid 관련 스크립트 로딩후 사용

## HTML CODE - 
	<table id="jqGrid"></table>
	<div id="jqGridPager"></div>
--------------------------------------
pager는 안쓸꺼면 생략 가능 (페이징처리정보나 로딩 row수 정보)

## Script CODE 예제 - 
	$("#comGrid").jqGrid({  
    	url: 'user/selectAdmNotice',  -- 호출 url
    	styleUI : 'Bootstrap',
    	mtype: "GET",              -- POST or GET
    	datatype: "json",          -- datatype설정 local이면 호출 안함
    	postData : {               -- 넘겨줄 parameter 설정
			id : "id",
			title : "title"
		},
    	colNames: ["ID","제목","내용","게시시작일자"],  -- 테이블 헤드 즉 컬럼 명 설정 
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
		loadonce:true, -- 이옵션을 true로 주면 한번 로딩 후 datatype를 local로 바꾼다. 즉 더이상 로딩 되지 않는다. 로딩된 데이타를 정렬할때 사용. 페이징 처리시 false, 새로 로딩 필요시 datatype을 변경후 reload시도 
		multiselect : false, 여러 row 선택가능여부
    	width: 500, -- 너비 : 단위 픽셀
		height: 250, -- 높이 : 단위 픽셀
    	rowNum: 10,  -- 출력될 row갯수 10줄 출력
    	pager:"#jqGridPager",  -- pager 출력될 위치 지정
    	jsonReader:{           -- 리스트가 아닌 객체가 넘어올 경우 출력할 리스트를 설정 및 기타 param값 설정  
    		root:"list",       -- root는 리스트를 선택한다
    		total:"total",     -- total 은 총 페이지수
    		records:"records"  -- recors 는 총 row수 
    	}
	});
	
## colModel Option
label    : string  --> 컬럼명 설정 conNames와 같은기능
name     : string  --> 받은 리스트에서 출력할 값 이름
key      : boolean --> rowid 지정
width    : number  --> 컬럼 너비 지정 (px)
align    : string  --> 정렬 left, center, right
editable : boolean --> 이 컬럼을 추가 및 수정시 사용할지 여부
formatter: mixed   --> checkbox,select 등 여러 옵션 존재, 커스텀 가능
+example code 

	function useFmatter(cellvalue, options, rowObject) {
	switch (cellvalue) {
		case "N":
			return "미사용";
		case "Y":
			return "사용";
	}
	return cellvalue;
	}




