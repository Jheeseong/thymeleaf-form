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

# v1.7 3/17
# @ModelAttribute 특별한 사용법
- 모든 폼에서 객체를 체크 박스에 반복해서 보여줘야할 떄 사용
- 기존의 경우 각각의 컨트롤러에서 model.addAttribute(...)를 사용해서 체크 박스를 구성하는 데이터를 넣어줘야함
- @ModelAttribute를 사용하여 별도에 메서드에 적용 시 해당 컨트롤러를 요청할 때 반환 값이 자동으로 모델에 담기게 됨

    @ModelAttribute("regions")
    public Map<String, String> regions() {
     Map<String, String> regions = new LinkedHashMap<>();
     regions.put("SEOUL", "서울");
     regions.put("BUSAN", "부산");
     regions.put("JEJU", "제주");
     return regions;
    }
    
# 체크 박스 - 멀티
- input type="checkbox" th:field="* {regions}" th:value="${region.key}
  - th:field에 속성을 넣으면 th:value에 들어가는 region.key 값과 비교해서 값이 포함되어있을 경우 checked가 추가. 즉, 자동으로 value와 비교해서 checked 여부를 설정
-  th:for="${#ids.prev('regions')}
  - 멀티 체크박스는 여러 체크박스가 생성되는데, name 속성은 모두 같아도 되지만 id는 유일해야 함.
  - 타임리프는 ids 프로퍼티를 제공하고, ids는 반복하는 요소의 인덱스로 1,2,3 의 숫자를 붙여줌
- id 가 checkbox에서 동적으로 생성된 region1, region2, region3에 맞춰 순서대로 입력됨

# 라디오 버튼
- 여러 선택지 중에 하나를 선택할 떄(ENUM) 사용 가능
- 체크 박스는 수정 시 체크를 헤제하면 false 값이 넘어가지만, 라디오 버튼 경우 수정 시에도 필수적으로 하나를 선택하도록 되어있음
- type="radio" th:field="* {itemType}" th:value="${type.name()}" class="form-check-input"

# 셀렉트 박스
- type="radio" th:field="* {itemType}" th:value="${type.name()}" class="form-check-input"
- th:each="deliveryCode : ${deliveryCodes}" th:value="${deliveryCode.code}" th:text="${deliveryCode.displayName}"

# v1.8 3/18
# 메시지
- html 뷰에 보이는 특정 단어를 보다 쉽게 변경 가능
- 기존에는 HTML 파일에 메시지가 하드코딩 되어 있음, label에 있는 단어를 변경 시 다른 화면들을 찾아가면서 모두 변경해야함
- 다양한 메시지를 한 곳에서 관리하는 기능을 메시지 기능이라 정의
- messages.properties 라는 관리용 파일을 생성

      item=상품
      item.id=상품 ID
      item.itemName=상품명
      item.price=가격
      item.quantity=수량
      
- 각 HTML들은 다음의 해당 데이터를 key 값으로 불러서 사용
  - label for="itemName" th:text="#{item.itemName}"

# 국제화
- 메시지에서 설정한 메시지 파일(message.properties)을 각 나라 별로 관리하여 서비스 국제화 가능
- ex) **messages_en.propertis**

      item=Item
      item.id=Item IDitem.itemName=Item Name
      item.price=price
      item.quantity=quantity
      
- ex) **messages_ko.propertis**

      item=상품
      item.id=상품 ID
      item.itemName=상품명
      item.price=가격
      item.quantity=수량
      
- HTTP accept-language 헤더 값을 이용하여 한국에서의 접근인지 영어에서의 접근인지 파악하여 선택

# v1.9 3/19
**MessageSource 인터페이스**

    public interface MessageSource {
      
      String getMessage(String code, @Nullable Object[] args, @Nullable String defaultMessage, Locale locale);
      String getMessage(String code, @Nullable Object[] args, Locale locale) throws NoSuchMessageException;
      
- 코드를 포함한 일부 파라미터로 메시지를 읽어오는 기능을 제공

**MessageSource TestCode**

    @Autowired MessageSource ms;
    
    @Test
    void helloMessage() {
        String result = ms.getMessage("hello", null, null);
        System.out.println("result = " + result);
    }
    
- message locale 정보가 없으면 basename에서 설정한 기본 메시지 파일을 조회(messages.properties 파일에서 데이터 조회) 

**MessageSource TestCode(메시지가 없는 경우, 기본 메시지)**

    @Test
    void notFoundMessageCode() {
        assertThatThrownBy(() -> ms.getMessage("no_code", null, null))
                .isInstanceOf(NoSuchMessageException.class);
    }

    @Test
    void notFoundMessageCodeDefaultMessage() {
        String result = ms.getMessage("no_code", null, "기본 메시지", null);
        System.out.println("result = " + result);
    }
    
- 지정한 메시지가 없는 경우 NoSuchMessageException이 발생
- 메시지가 없어도 기본 베시지(defaultMessage)를 사용시 기본 메시지가 

**MessageSource TestCode(매개변수 사용)**

    @Test
    void argumentMessage() {
        String result = ms.getMessage("hello.name", new Object[]{"Spring"}, null);
        assertThat(result).isEqualTo("안녕 Spring");
    }
    
- messages.properties에서 설정한 hello.name=안녕 {0} 에서 {0}부분에 매개변수를 전달하여 치환이 가능
- 이 코드에선 Spring 단어를 매개변수로 전달
- 결과 : 안녕 Spring
