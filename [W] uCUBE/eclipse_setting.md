## eclipse_setting
### Eclipse Project
1. Glue Maven Project 생성
2. Java 1.7 변경 (pom.xml & Java Build Path)
3. Resource - Text file encoding `UTF-8`

### Resource
Text file encoding `UTF-8`

### Deployment Assembly
/src/main/java : WEB-INF/classes
/src/main/resources : WEB-INF/classes
/src/main/webapp : /
/target/m2e-wtp/web-resources : /
Maven Dependencies : WEB-INF/lib

### Project Facets
Dynamic Web Module 3.0
Java 1.7
JavaScript 1.0
JavaServer Faces 2.0
JAX-RS (REST Web Services) 1.1

### server.xml
URIEncoding="UTF-8"

### config_path
-DCONFIG_PATH="D:\Develop\apache-tomcat-7.0.47_second"


O1. 초기화면 Monitoring > Queue Monitor
O2. Auditor > History Event View에서 Detail Event View로 이동 할때 파라미터에 트랜잭션 코드도 추가
O3. Auditor > Detail Event View 백업 데이터 항목삭제
O4. Excel Import, Export
 - 수정기능이 있는 메뉴
 - DRM해지 문의
O5. Row Count 선택기능 추가 (10, 20, 30, 50)
O6. Composer > SMS Manager 등록
 - SMS수신자 검색을 EP메일처럼 SELECT식으로 변경
O0. Menu 한글처리




O01. VDI 개발 환경구성 
02. 상세처리현황 데이터 파싱
03. SMS수신자 팝업X