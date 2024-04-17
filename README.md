1. 프로젝트 생성. 
mvn -B archetype:generate -DarchetypeArtifactId=cds-services-archetype -DarchetypeGroupId=com.sap.cds \
  -DarchetypeVersion=RELEASE -DjdkVersion=17 \
  -DgroupId=com.sap.cap -DartifactId=products-service -Dpackage=com.sap.cap.productsservice
2. 생성된 프로젝트 구조
  - srv : Java 애플리케이션
  - db : 데이터베이스 관련 아티팩트
3. cds 서비스 정의 
 - srv > admin-service.cds 생성 
 '''
service AdminService{
    entity Products {
        key ID : Integer;
        title : String(111);
        descr : String(1111);
    }
}
'''
