## Apache Tomcat GZIP 압축 적용

### tomcat/conf/server.xml
8080 `<Connector>` Node에 속성 추가
```xml
<Connector port="8080" protocol="HTTP/1.1" redirectPort="8443"
    URIEncoding="UTF-8"
    connectionTimeout="20000" 
    compression="on"
    compressionMinSize="1024"
    noCompressionUserAgents="gozilla, traviata"
    compressableMimeType="text/html,text/xml,text/plain,text/javascript,text/css,application/javascript"/>
```
+ compression : 압축 사용 여부 `Default : "off"`
+ compressionMinSize : 압축하는 최소 파일 크기 `Default : "2048"`
+ noCompressionUserAgents : 압축을 사용하지 않을 브라우저 지정 `Default : ""`
+ compressableMimeType : 압축을 사용 할 파일 타입 `Default : ""`