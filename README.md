# thymeleaf-form
# v1.6 3/16
# 타임리프 - 스프링 통합과 폼
# 입력 폼 처리
- th:object : 커멘드 객체를 지정
- * {...} : 선택 변수 식, th:object에서 선택한 객체에 접근
- th:field : html 태그의 id,name,value 속성을 자동 처리

**등록 폼** 
- action="item.html" th:action th:object="${item}" method="post : form에서 사용할 객체를 지정, 선택 변수 식에 적용 가능
- th:field="* {itemName}" : ${item.itemName}과 동일, th:object에서 선택한 변수 식 적용 가능
  - th:field 는 id, name, value 속성을 모두 자동으로 생성, 지정한 변수 이름과 동일

# 체크 박스 - 단일 1
- html의 체크 박스를 체크 시 form에서 open=on 이라는 값이 전달, 스프링은 on이라는 문자를 true로 변환
- 체크 박스를 체크하지 않을 시 open 값이 서버로 전송X -> null 값을 전송
- 해결법
  - input type="hidden" name="_ open" value="on" 을 추가
  - 히든 필드를 만들어서 기존 체크박스 이름 앞에 언더스코어( _ ) 를 붙여서 전송 시 체크 해제를 인식
  - 체크 박스를 체크하지 않을 시 open은 전송되지 않고 언더스코어를 붙인 open만 전송되어 체크 헤제를 인식

# 체크 박스 - 단일 2
- type="checkbox" id="open" th:field="* {open}" class="form-checkinput"
- th:field="* {open}" 타임리프를 사용 시 체크 박스의 히든 필드 부분을 해결
- disabled 사용 시 체크 박스 선택하지 않도록 적용

**타임리프의 체크 확인**
- checked="checked" : 체크 박스를 선택 후 저장하면 html에 checked 속성이 추가된 것이 확인 가능
- th:field가 체크 박스 값이 true일 경우 체크로 자동 
