# Alamorefire 공부하기

## Alamorefire란?
`Alamorefire`는 `swift`로 만든 HTTP 네트워킹을 위한 라이브러리다.

## URL Loading System
`Alamorefire`는 Apple의 Foundation 프레임워크에서 제공하는 [URL Loading System](https://developer.apple.com/documentation/foundation/url_loading_system)을 기반으로 구현했다.

`URL Loading System`을 통해 `HTTPS/HTTP`와 같은 표준 프로토콜 또는 사용자가 정의한 프로토콜을 사용하여 URL과 상호 작용하고 서버와 통신할 수 있다.</br>
로딩은 비동기식으로 수행되기 때문에 앱이 응답성을 유지하고 전달 받는 데이터나 오류를 도착과 동시에 처리할 수 있다.</br>

## 웹 사이트의 데이터를 메모리로 가져오는 방법
웹 사이트의 URL을 통해 데이터를 가져오기 위해서는 `URLSession` 인스턴스를 사용하여 작업을 생성해야한다.</br>
그리고 작업이 비교적 간단한 경우, `URLSession`의 `shared` 속성에 접근하여 세션을 만들지 않고 사용할 수도 있다.

- 서버와 작은 규모의 상호 작용의 경우 `URLSessionDataTask` 인스턴스를 생성하여 응답 데이터를 메모리로 수신하여 사용한다.</br>
- `URLSessionDataTask`는 웹 서비스의 EndPoint를 호출하는 등의 용도에 사용하기 적합하다.</br>
- 파일 시스템에 직접 데이터를 저장해야하는 경우, `URLSessionDownloadTask` 인스턴스를 사용한다.</br>


### URLSession
`URLSession`은 네트워킹 즉, 서버와 통신할 수 있도록 제공하는 객체다.

- `URLSession` 인스턴스를 사용하여 하나 이상의 `URLSessionTask` 인스턴스를 생성한다.</br>
- `URLSessionTask` 인스턴스는 데이터를 앱으로 가져오거나 반환하기도 하며, 파일을 다운로드하고 데이터와 파일을 서버에 업로드할 수도 있다.</br>
- `URLSession`을 구성하려면 `URLSessionConfiguration` 객체를 사용하여 캐시와 쿠키 및 셀룰러 네트워크의 연결 허용 여부 등을 제어할 수 있다.</br>
- 하나의 세션을 반복적으로 사용해서 작업을 생성할 수 있다.</br>

### URLSessionTask
`URLSessionTask`는 `URLSession`의 일부이며, 세션을 통해 수행할 작업이다.

- `URLSession` 인스턴스의 `dataTask(with:)` 및 여러 메서드를 호출하여 `URLSessionTask`을 생성한다.</br>
- `Data Task`는 리소스를 요청하여 서버의 응답을 메모리에 있는 하나 이상의 `NSData` 객체로 반환한다.</br>
- 이것들은 `기본(default)`, `임시(ephemeral)`, `공유(shared)` 세션에서는 지원되지만 `백그라운드(Background)` 세션에서는 지원되지 않는다.

## URLSession을 사용한 네트워크 통신 코드

### 1. URL 생성
서버의 URL을 생성한다.
```swift
let url = URL(String:"https://jsonplaceholder.typicode.com/posts")!
```

### 2. URLRequest 생성
URL을 사용하여 요청을 생성한다.
```swift
var urlRequest = URLRequest(url: url)
urlRequest.httpMethod = "GET"
urlRequest.setValue("application/json", forHTTPHeaderField: "Content-Type")
urlRequest.setValue("application/json", forHTTPHeaderField: "Accept")
```

### 3. URLSession 생성
서버와 통신하기 위한 세션을 생성한다.
```swift
let session = URLSession.shared
```

### 4. URLSessionTask 생성
요청에 대한 작업을 생성한다.
```swift
let task = session.dataTask(with: urlRequest) { data, response, error in
    if let error = error {
        print(error)
        return
    }

    guard let httpResponse = response as? HTTPURLResponse,
        (200...299).contains(httpResponse.statusCode) else {
        print(error)
        return
    }

    print(String(decoding: safeData, as: UTF8.self))
}

// Task는 일시정지된 상태로 생성되기 때문에 실행해야한다.
task.resume()
```

