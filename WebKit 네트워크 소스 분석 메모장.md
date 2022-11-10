# WebKit 네트워크 소스 분석 메모장 
1. WebCore: WebKit 의 가장 거대한 컴포넌트. DOM, Render Tree 
2. WebKit: (일명 WebKit2) 해당 레이어에서는 WebKit의 멀티-프로세스 구조를 구현. macOS & iOS 의 WKWebView 를 구현. 구성은 아래와 같음:
  - UI 프로세스
  - WebContent 프로세스
  - Networking 프로세스

/ WebKit / Source / WebCore / platform / network / curl /
## WebCore CURL
namespace WebCore 내부에 정의 

### CurlContext.h
- CurlGlobal: libcurl 전역적인 초기화를 위한 인터페이스
- CurlShareHandle: curl 공유 핸들
- CurlContext: CurlGlobal 상속, CurlRequestScheduler, CurlStreamScheduler 소유
  프록시, SSL, HTTP/2, Timeout 담당
- CurlMultiHandle: curl 멀티 핸들
- CurlSList: curl_slist 리스트?
- CurlHandle: curl 핸들 래퍼

### CurlStream.h
- class Client: 내부 클래스 - 
- CurlStreamScheduler 참조 변수
- CurlHandle 유니크 포인터
- 성공 / 데이터 송수신 등 메서드 제공

CurlStreamID uint16_t 정의됨 1부터 시작이며 
invalidCurlStreamID(유효하지 않은 값) 고정값은 0으로 설정

### CurlStreamScheduler.h
- 내부적으로 스레드를 갖고 있음
- 시작/끝 메서드 제공
- 기본 형식 (void f(void)) 함수에 대한 벡터 보유
- key: CurlStreamID, value: CurlStream::Client* 형식 해시 맵 보유
- key: CurlStreamID, value: std::unique_ptr<CurlStream> 형식 해시 맵 보유

### CurlRequest.h
- ThreadSafeRefCounted<CurlRequest>, 
  CurlRequestSchedulerClient, CurlMultipartHandleClient 상속
- NetworkLoadMetrics 클래스 등장(?)
- 시작/끝, 취소됐는지, 셋업, 헤더/바디 수신 시 콜백 호출 등 메서드 제공

### CurlRequestClient.h
- virtual 메서드만 존재
- 대부분 CurlRequest 참조를 인자로 받는 메서드 
- 사용처는 CurlRequest.cpp로 예상됨

### CurlRequestScheduler.h
- 내부적으로 스레드를 갖고 있음
- CurlRequestSchedulerClient 에 대한 해시 셋과 해시 맵 갖고 있음

### CurlRequestSchedulerClient.h
- virtual 메서드만 존재
- CURL 핸들 포인터 메서드
- 데이터 송수신 완료/취소 메서드 제공

### CurlResourceHandleDelegate.h
- CurlRequestClient 상속(구현체)
- CurlRequest 의 메서드 구현
- ResourceHandle, ResourceHandleClient, ResourceHandleInternal 클래스 등장(?)

### CurlResponse.h
- isolatedCopy() 라는 독특한 메서드 제공
- CertificateInfo 클래스 등장(?)
- URL, statusCode, httpConnectCode, headers 등 HTTP 관련 변수 보유

### CurlMultipartHandle.h
- 멀티파트 데이터 송수신 핸들
- processContent(), matchedLength(), parseHeadersIfPossible() 등 메서드 제공
- CurlMultipartHandleClient 참조 변수 보유

### CurlMultipartHandleClient.h
- virtual void 메서드만 있음
- didReceiveHeaderFromMultipart(), didReceiveBodyFromMultipart() 

---------------------------------------- CurlSSL

### OpenSSLHelper.h
- 네임스페이스 OpenSSL 안에 메서드 정의

### CurlSSLHandle.h
- 추가 바람

### CurlSSLVerifier.h
- 추가 바람

---------------------------------------- Resource
추가 예정

---------------------------------------- Curl Utils

### CurlCacheEntry.h
- 

### CurlCacheManager.h
- 

## WebKit Networking Process
