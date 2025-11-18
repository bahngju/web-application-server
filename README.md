# 실습을 위한 개발 환경 세팅
* https://github.com/slipp/web-application-server 프로젝트를 자신의 계정으로 Fork한다. Github 우측 상단의 Fork 버튼을 클릭하면 자신의 계정으로 Fork된다.
* Fork한 프로젝트를 eclipse 또는 터미널에서 clone 한다.
* Fork한 프로젝트를 eclipse로 import한 후에 Maven 빌드 도구를 활용해 eclipse 프로젝트로 변환한다.(mvn eclipse:clean eclipse:eclipse)
* 빌드가 성공하면 반드시 refresh(fn + f5)를 실행해야 한다.

# 웹 서버 시작 및 테스트
* webserver.WebServer 는 사용자의 요청을 받아 RequestHandler에 작업을 위임하는 클래스이다.
* 사용자 요청에 대한 모든 처리는 RequestHandler 클래스의 run() 메서드가 담당한다.
* WebServer를 실행한 후 브라우저에서 http://localhost:8080으로 접속해 "Hello World" 메시지가 출력되는지 확인한다.

# 각 요구사항별 학습 내용 정리
* 구현 단계에서는 각 요구사항을 구현하는데 집중한다. 
* 구현을 완료한 후 구현 과정에서 새롭게 알게된 내용, 궁금한 내용을 기록한다.
* 각 요구사항을 구현하는 것이 중요한 것이 아니라 구현 과정을 통해 학습한 내용을 인식하는 것이 배움에 중요하다. 

### 요구사항 1 - http://localhost:8080/index.html로 접속시 응답
# Java I/O Stream 이해하기: `BufferedReader`의 역할

Java에서 입출력(I/O) 작업을 수행할 때 스트림(Stream)의 개념은 매우 중요합니다. 특히 콘솔 입력과 같이 바이트(Byte) 단위로 들어오는 데이터를 우리가 사용하는 문자(Character) 단위로 변환하고 효율적으로 처리하기 위해 `InputStream`, `InputStreamReader`, `BufferedReader`가 함께 사용됩니다.

## 1. 기본 개념 정리

*   **`InputStream` (바이트 처리 스트림)**
    *   **역할:** 이진(binary) 데이터, 즉 **바이트(byte) 단위**의 데이터를 처리하는 스트림의 최상위 추상 클래스입니다.
    *   **예시:** `System.in` (콘솔 입력), `FileInputStream` (파일로부터 바이트 읽기)
    *   **특징:** 이미지, 오디오, 동영상 파일 등 모든 종류의 데이터를 바이트 단위로 읽을 때 사용됩니다.

*   **`Reader` (문자 처리 스트림)**
    *   **역할:** 문자(Character) 단위의 데이터를 처리하는 스트림의 최상위 추상 클래스입니다.
    *   **예시:** `FileReader` (파일로부터 문자 읽기)
    *   **특징:** 텍스트 파일과 같이 문자열 데이터를 다룰 때 사용됩니다.

## 2. 스트림의 연결과 변환

우리가 키보드로 입력하는 문자는 실제로는 `System.in`을 통해 바이트 형태로 들어오게 됩니다. 이 바이트들을 사람이 읽을 수 있는 문자로 변환하고, 더 효율적으로 사용하기 위해 다음과 같은 과정을 거칩니다.

### `InputStreamReader` (바이트 스트림 -> 문자 스트림 변환)

*   **역할:** `InputStream` 객체를 생성자의 매개변수로 전달받아, 해당 **바이트 스트림을 문자 스트림으로 변환**해주는 "다리" 역할을 합니다.
*   **원리:** 내부적으로 지정된 문자 인코딩(예: UTF-8, EUC-KR)에 따라 바이트 시퀀스를 문자로 디코딩합니다.
*   **예시:** `new InputStreamReader(System.in)`
    *   `System.in`으로 입력된 **바이트** 데이터를 `InputStreamReader`가 **문자(char)** 형태로 변환합니다.

### `BufferedReader` (문자 스트림 버퍼링 및 편리한 읽기)

*   **역할:** `Reader` 객체(예: `InputStreamReader`, `FileReader`)를 감싸서 **문자 스트림의 읽기 성능을 향상**시키고, 한 줄(`String`) 단위로 데이터를 읽는 등 편리한 기능을 제공합니다.
*   **원리:** 대량의 문자를 한 번에 읽어 내부 버퍼에 저장해두고, 프로그램이 요청할 때 버퍼에서 데이터를 제공함으로써 실제 I/O 작업을 줄여 성능을 높입니다.
*   **예시:** `new BufferedReader(reader)`
    *   `InputStreamReader`가 변환한 **문자(char)** 데이터를 `BufferedReader`가 **String** 형태로 입력받을 수 있게 해줍니다. 특히 `readLine()` 메서드는 개행 문자(`\n`)를 기준으로 한 줄 전체를 `String`으로 반환하여 매우 유용합니다.

## 3. 종합적인 흐름 예시

콘솔에서 사용자 입력을 `String`으로 받기 위한 일반적인 패턴은 다음과 같습니다.

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;

public class ConsoleInputExample {
    public static void main(String[] args) {
        // 1. System.in (바이트 스트림)을 InputStreamReader를 통해 문자 스트림으로 변환
        InputStreamReader reader = new InputStreamReader(System.in);

        // 2. 문자 스트림(InputStreamReader)을 BufferedReader로 감싸서 버퍼링 및 편리한 읽기 기능 추가
        BufferedReader br = new BufferedReader(reader);

        System.out.println("문자열을 입력하세요:");
        try {
            // BufferedReader의 readLine()을 사용하여 한 줄을 String으로 읽음
            String inputLine = br.readLine();
            System.out.println("입력된 문자열: " + inputLine);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 스트림 닫기 (자원 해제)
            try {
                if (br != null) br.close();
                if (reader != null) reader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

이 코드는 다음과 같은 과정을 거칩니다:

1.  `System.in`으로 입력 받은 **바이트(Byte) 형태**의 데이터를
2.  `InputStreamReader`에 전달하여 **문자(Char) 형태**로 변환하고
3.  **문자(Char) 형태**로 변환된 값을 `BufferedReader`가 **String 형태**로 입력받아 처리하는 형태입니다.

이러한 단계별 스트림 연결을 통해 Java는 다양한 I/O 소스로부터 들어오는 데이터를 유연하고 효율적으로 처리할 수 있게 됩니다.

### 요구사항 2 - get 방식으로 회원가입
* 

### 요구사항 3 - post 방식으로 회원가입
* 

### 요구사항 4 - redirect 방식으로 이동
* 

### 요구사항 5 - cookie
* 

### 요구사항 6 - stylesheet 적용
* 

### heroku 서버에 배포 후
* 
