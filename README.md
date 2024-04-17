1. 프로젝트 생성. 
mvn -B archetype:generate -DarchetypeArtifactId=cds-services-archetype -DarchetypeGroupId=com.sap.cds \
  -DarchetypeVersion=RELEASE -DjdkVersion=17 \
  -DgroupId=com.sap.cap -DartifactId=products-service -Dpackage=com.sap.cap.productsservice
2. 생성된 프로젝트 구조
  - srv : Java 애플리케이션
  - db : 데이터베이스 관련 아티팩트
3. cds 서비스 정의 
 - srv > admin-service.cds 생성
 - cds 서비스 샘플
<pre>
<code>
service AdminService{
    entity Products {
        key ID : Integer;
        title : String(111);
        descr : String(1111);
    }
}
</code>
</pre>
4. 컴파일
  - mvn install
  - srv/src/main/resources/edmx 경로 자동 생성 > 해당 경로가  CAP Java 런타임이 모델 정의를 찾는 기본 경로
5. 실행
  - mvn clean spring-boot:run
  - F1 > port:review (가상 환경에서 8080 포트 접근)
  - 
![image](https://github.com/kangseunghyun/cdsgo/assets/21374560/422a37de-f619-4825-9778-4ad5a4585cbc)

6. handeler 설정
  - handeler를 통해 수신된 event를 처리.
  - crud서비스를 설정을 통해 간단하게 생성 가능
  - srv/src/main/java/com/sap/cap/productsservice 내에 handlers 패키지 추가
  - AdminService.java
<pre>
<code>
@Component
@ServiceName("AdminService")
public class AdminService implements EventHandler {

    private Map<Object, Map<String, Object>> products = new HashMap<>();

    @On(event = CqnService.EVENT_CREATE, entity = "AdminService.Products")
    public void onCreate(CdsCreateEventContext context) {
        context.getCqn().entries().forEach(e -> products.put(e.get("ID"), e));
        context.setResult(context.getCqn().entries());
    }

    @On(event = CqnService.EVENT_READ, entity = "AdminService.Products")
    public void onRead(CdsReadEventContext context) {
        context.setResult(products.values());
    }

}
</code>
</pre>
* cqn은 cds에서 사용하는 공통쿼리 서비스임. 참고 https://cap.cloud.sap/docs/cds/cqn
  
7. test
 - requests.http 생성 (vs code plugin http request 필요)
<pre>
<code>
### Create Product

POST http://localhost:8080/odata/v4/AdminService/Products
Content-Type: application/json

{"ID": 42, "title": "My Tutorial Product", "descr": "You are doing an awesome job!"}
</code>
</pre>
8.   
