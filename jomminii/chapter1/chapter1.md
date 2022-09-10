## [part 1] JVM 이란
[블로그 링크](https://velog.io/@jomminii/whiteship-java-01-what-is-jvm)

>[백기준님 라이브 스터디 따라하기](https://github.com/whiteship/live-study)
JVM은 무엇이며 자바 코드는 어떻게 실행하는 것인가
자바 소스 파일(.java)을 JVM으로 실행하는 과정 이해하기.
- JVM 이란 무엇인가

<br>

# JVM(Java Virtual Machine)

JVM은 자바로 작성된 프로그램을 실행시켜주기 위한 가상 컴퓨터라고 보면 됩니다.

일반적으로 프로그램은 OS에 종속적으로 개발이 되었지만 JVM 은 OS 와 프로그램 사이에 위치해 OS 와 상관 없이 프로그램을 실행할 수 있게 해줍니다. 이게 가능한 이유는 각 OS에 맞게 제공된 JVM이 자바를 실행할 수 있게 도와주기 때문입니다.
>참고로 JVM 은 자바 프로그래밍 언어에 대한건 아무것도 모르고 특정 바이너리 형식인 클래스 파일 형식만 알고 있습니다.

이로 인해 자바의 주요한 장점 중 하나인 "Write once, run anywhere.(한 번 작성하면 어디서든 실행된다.)" 가 가능해집니다.

![](https://miro.medium.com/max/643/0*GMXQBZCEpGQMBjy-)(이미지 출처 : [platform-engineer](https://medium.com/platform-engineer/understanding-jvm-architecture-22c0ddf09722))

<br>

## 세 부분으로 이루어진 JVM
### JVM specification
JVM 은 [소프트웨어 사양 명세](https://docs.oracle.com/javase/specs/jvms/se7/html/index.html)라고 볼 수 있는데 구현할 때 최대한의 창의성을 허용하기 위해 명세에서는 세부사항까지는 정의하고 있지 않음을 강조하고 있습니다.
>JVM 을 올바르게 구현하려면, 그저 class file 을 읽고 지정된 작업을 올바르게 수행할 수 있게 하기만 하면 됩니다.

따라서 JVM 을 구현할 때는 그저 Java 를 올바르게 실행하는 것 뿐이라고 말하고 있습니다. 이 외에는 각자의 구현에 맡기는 거죠.

### JVM implementation
JVM 사양을 구현해서 만든 실제 소프트웨어 프로그램으로 OpenJDK의 HotSpot JVM이 가장 일반적으로 사용되는 JVM 이라고 합니다.

### JVM instance
구현된 JVM이 소프트웨어 제품으로써 배포가 되어 그걸 다운 받으면 이걸 JVM 인스턴스라고 합니다.

<br>

## JVM 의 주요 기능
자바를 어느 장치나 어느 OS 에서도 실행 가능하게 해주고, 프로그램 메모리를 최적화하고 관리해줍니다. 자바가 나온 1995년에는 모든 컴퓨터 프로그램은 특정 os에 맞게 개발되었고, 메모리 또한 개발자들에 의해 관리되었는데 JVM이 이걸 해결해줬습니다.

![](https://velog.velcdn.com/images/jomminii/post/e7cd2e4f-7fcc-4d92-b89a-57cc788b21f5/image.png)(이미지출처 : [오라클](https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/index.html))

<br>

### 클래스 로더(Class Loader)
자바 프로그램을 실행하기 위해서 JVM은 바이트 코드로 컴파일 된 `.class` 파일을 읽어내야합니다.

클래스 로더는 컴파일된 코드를 읽어 클래스를 운영체제로 부터 할당 받은 메모리 영역인 `Runtime Data Area`에 적재해 실행할 수 있도록 하는데, lazy-loading 과 캐싱 같은 기술을 이용해 클래스 로딩을 효율적으로 만들어줍니다.

JVM은 RAM에 상주하며 실행하는 동안 클래스 파일을 RAM으로 가져옵니다. 이걸 동적 클래스 로딩이라고 부르며, 컴파일 때가 아닌 런타임에 처음 클래스를 참조할 때 `.class` 파일을 load, link, initialize 합니다.

#### Loading
클래스 로딩 단계에서 클래스 로더는 주어진 바이트 코드를 JVM의 메소드 영역에 로드 및 저장합니다. 로더는 3가지 종류가 있습니다.
- `BootstrapClassLoader` : 다른 모든 클래스 로더의 부모가 되는 로더로 BootStrapClassPath(jdk/jre/lib/ 의 rt.jar) 에서 자바 라이브러리 클래스를 로드하는 역할을 합니다. 다른 로더와 다르게 탑재되는 운영체제에 맞게 네이티브 코드로 쓰여졌습니다.
- `ExtensionClassLoader` : 부트스트랩 클래스 로더에서 실패하면 ExtensionClassPath(jdk/jre/lib/ext) 에서 사용자 정의 클래스 또는 자바 라이브러리 클래스를 로드하는 역할을 합니다.
- `ApplicationClassLoader/SystemClassLoader`: ApplicationClassPath 에서 사용자 정의 클래스를 로드하는 역할을 합니다. 개발자들이 짠 클래스 파일들을 JVM 에 탑재하는 역할을 합니다.

위의 클래스 로더를 모두 거쳤는데도 클래스 파일을 찾지 못하면 `ClassNotFoundException` 예외를 던집니다.

#### Linking
로드된 클래스 파일을 검증하고 사용할 수 있게 준비하는 과정 입니다.

- `verification`  : 클래스 파일이 유효한지 확인합니다. 클래스 로더가 바이트 코드를 자바 언어 명세에 따라 잘 작성했는지, JVM 규격에 따라 검증된 컴파일러에서 생성됐는지 등을 확인하여 정확성을 보장합니다. 클래스 로드 중 가장 오래걸리는 프로세스이지만, 바이트 코드를 실행할 때 여러번 검사를 수행할 필요가 없으므로 효율적 입니다.
> 공격자가 클래스 파일을 수정해 공격을 시도해도 바이트코드 검증기를 통해 정상적인 파일인지 확인하고, 검증에 실패하면 java.lang.VerifyError를 발생시킴
- `preparation` : 클래스 및 인터페이스에 필요한 static field 메모리를 할당하고, 기본 값으로 초기화합니다. 코드에서 작성한 초기 값은 초기화 단계에서 할당되므로 아직 초기화 블록이나 초기화 코드는 실행되지 않습니다.(ex) int 는 0으로, boolean 은 false로)
- `resolution` : Symbolic Reference(심볼릭 참조) 값을 JVM의 메모리 구성 요소인 Method Area 의 런타임 상수 풀을 통해 Direct Reference(직접 참조) 라는 메모리 주소 값으로 바꿔줍니다. 추상적인 기호를 구체적인 값으로 동적으로 결정하는 과정입니다. 이 단계에 영향을 받는 요소는 new, instanceof 가 있습니다. 이 요소들은 런타임 상수 풀에 있는 심볼릭 참조를 사용하고, 이를 실행하려면 먼저 심볼릭 참조를 해석해야합니다.

#### Initialization
초기화 단계에서 static 블록이 실행되고, 정적 변수가 초기값으로 초기화됩니다.

<br>

### Runtime Data Area
런타임 데이터 영역은 JVM 프로그램이 OS 에서 실행될 때 할당되는 메모리 영역 입니다.

클래스 로더는 바이너리 데이터를 생성하고 각 클래스에 대해 다음 정보를 메서드 영역에 저장합니다.
- 로드된 클래스 및 해당 직계 상위 클래스의 정규화된 이름
- .class 파일이 Class/Interface/Enum과 관련이 있는지 여부
- 수정자, 정적 변수 및 메서드 정보 등

그 다음 로드된 모든 `.class`에 대해 `java.lang` 패키지에 정의된 대로 힙 메모리에 파일을 나타내기 위해 정확히 하나의 `Class` 객체를 생성합니다.

<br>

#### Method Area(쓰레드 간 공유)
메서드 영역은 JVM  당 하나만 존재하는 공유 리소스 입니다. 모든 JVM 쓰레드는 같은 메서드 영역을 공유하기 때문에 모든 메서드 데이터에 대한 접근과 dynamic linking 프로세스는 thread safe 해야합니다.

메서드 영역은 정적 변수와 같은 클래스 레벨의 데이터를 저장합니다.
- 클래스 로더 참조(ClassLoader reference)
- 런타임 상수풀(Run time constant pool) -  숫자 상수, 필드 참조, 메서드 참조, 속성 값. 각 클래스와 인터페이스의 상수 뿐만 아니라 메서드와 필드의 모든 참조값을 포함하고 있습니다. 메서드와 필드가 참조되면 JVM은 런타임 상수풀에서 메서드와 필드의 실제 주소를 검색합니다.
- 필드 데이터(Field data) - 각 필드의 이름, 타입, 수정자, 속성
- 메서드 데이터(Method data) - 각 메서드의 이름, 반환타입, 파라미터 타입, 수정자, 속성
- 메서드 코드(Method code) - 각 메서드의 바이트코드, 피연산자 스택 크기, 로컬 변수 크기, 로컬 변수 테이블, 예외 테이블, 예외 테이블 등

<br>

#### Heap Area(쓰레드 간 공유)
힙 영역도 JVM 당 하나만 존재하는 공유 리소스 입니다. 모든 객체의 정보와 해당 인스턴스 변수 및 배열은 힙 영역에 저장됩니다. 메서드와 힙 영역은 멀티 쓰레드에서 메모리를 공유하기 때문에 이 영역들에 저장된 데이터는 thread safe 하지 않습니다. 힙 영역은 GC의 좋은 타겟 입니다.

<br>

#### Stack Area(쓰레드 당 생성)
스택 영역은 공유 리소스가 아닙니다. 쓰레드가 시작되면 쓰레드마다 메서드 호출을 저장하기 위해 각자의 런타임 스택이 생성됩니다. 모든 메서드 호출에 대해 하나의 항목이 생성이 되어 런타임 스택의 맨 위에 추가(push)되며 이러한 항목을 스택 프레임이라고 합니다.

각각의 스택 프레임은 실행된 메서드가 속한 클래스의 로컬 변수 배열, 피연산자 스택, 런타임 상수 풀에 대한 참조를 가지고 있습니다. 로컬 변수 배열, 피연산자 스택의 사이즈는 컴파일 될 때 결정되며, 이로 인해 메서드에 따라 스택 프레임의 사이즈가 결정됩니다.

스택 프레임은 정상적으로 메서드가 return 을 하거나 메서드 호출 중 예기지 못한 예외가 발생하게 되면 제거(popped) 됩니다. 또한 예외가 발생하면 stack trace의 각 라인은 하나의 스택 프레임을 나타냅니다.(`printStackTrace()` 와 같은 메서드로 표시됨) 그리고 스택 영역은 공유 리소스가 아니기 때문에 thread safe 합니다.

![](https://velog.velcdn.com/images/jomminii/post/90f000c7-23a7-408a-88d5-02cdde9d51d9/image.png)(이미지 출처 : [platform-engineer](https://medium.com/platform-engineer/understanding-jvm-architecture-22c0ddf09722))

스택 프레임은 세개의 하위 항목으로 나뉩니다.
>[JVM stack과 frame - 기계인간 John Grib](https://johngrib.github.io/wiki/jvm-stack/) 에서 예시 참조

- `Local Variable Array` : 지역 변수 배열은 0부터 시작하는 인덱스를 가지고 있고, 특정 메서드에 대한 관련된 지역 변수와 해당 값들이 이곳에 저장됩니다. 0은 메서드가 속한 클래스의 인스턴스 참조이며, 1부터 메서드에 전달된 파라미터들이 저장되며, 메서드 파라미터 다음에 지역 변수들이 저장됩니다.
- `Operand Stack` : 메서드 내 계산을 위한 작업 공간 입니다.
- `Constant Pool Reference` : 런타임 상수풀에 대한 참조를 갖고 있습니다.

<br>


## 메모리 관리 기능
## JVM 실행 엔진
클래스 로딩이 끝나면 JVM의 실행엔진(execution engine)은 각각의 클래스를 실행하기 시작합니다. 코드를 실행한다는 것에는 시스템 리소스 접근을 관리하는 것과도 관련이 있습니다. 실행 엔진은 파일, 네트워크, 메모리 리소스 등을 요구하는 프로그램과 이 자원들을 제공하는 os 시스템 사이에 위치하고 있습니다.

### 실행 엔진은 어떻게 시스템 리소스를 관리하는가
시스템 리스소는 넓게 나눠보면 메모리와 그 밖의 것으로 들 수 있습니다. JVM 이 사용되지 않는 메모리를 처리한다고 했는데 가비지 컬렉션이 이 처리를 담당합니다. JVM 실행 중에 자바 프로그램에서 사용되지 않는 메모리를 식별하고 제거합니다.

그리고 JVM은 참조구조를 할당하고 관리하는 일도 하는데, 예를 들면 자바의 `new` 키워드 같은 걸 메모리 할당을 위한 OS별 요청에 맞게 처리하는 역할을 합니다.


# 참고
자바의정석 chapter 1.4 JVM

[What is the JVM? Introducing the Java Virtual Machine - InfoWorld](https://www.infoworld.com/article/3272244/what-is-the-jvm-introducing-the-java-virtual-machine.html)

[[Java] 자바 JVM 내부 구조와 메모리 구조에 대하여 - 코딩팩토리](https://coding-factory.tistory.com/828)

[How the new JVM works and its Architecture - knoldus](https://blog.knoldus.com/how-the-new-jvm-works-and-its-architecture/)

[JVM에 관하여 - Tecoble](https://tecoble.techcourse.co.kr/post/2021-07-15-jvm-classloader/)

[JVM. 클래스 로더 서브시스템 - hexabrain](https://blog.hexabrain.net/397)

[Understanding JVM Architecture - Thilina Ashen Gamage](https://medium.com/platform-engineer/understanding-jvm-architecture-22c0ddf09722)

[JVM stack과 frame - 기계인간 John Grib](https://johngrib.github.io/wiki/jvm-stack/)

## [part 2] 컴파일 및 실행 방법, 바이트코드 외
[블로그 링크](https://velog.io/@jomminii/whiteship-java-01-what-is-compile-etc)

>[백기준님 라이브 스터디 따라하기](https://github.com/whiteship/live-study)
1주차 JVM은 무엇이며 자바 코드는 어떻게 실행하는 것인가
자바 소스 파일(.java)을 JVM으로 실행하는 과정 이해하기.

<br>

# 컴파일 및 실행 하는 방법
자바 프로그램은 JVM 이 읽을 수 있는 `.class` 파일로 컴파일을 해줘야합니다. 자바 언어로 된 코드를 바이트 코드로 변환해주는 과정인데요, 과정은 어렵지 않습니다.

```bash
javac [options] [sourcefiles]
```

예를 들어 test.java 라는 자바 파일이 있고, 이걸 컴파일 하고자 한다면
```java
public class test {

    public static void main(String[] args) {
        System.out.println("hi");
    }
}

```

아래처럼 `javac` 명령어로 해당 자바 파일을 컴파일해주면 됩니다.
```bash
javac test.java

```
`javac` 명령어 뒤에 옵션을 넣을 수도 있는데 `-encoding` 등의 옵션을 넣어 인코딩을 설정할 수도 있습니다. 보다 자세한 옵션들은 [문서](https://docs.oracle.com/en/java/javase/11/tools/javac.html#GUID-AEEC9F07-CB49-4E96-8BC7-BCC2C7F725C9)에서 확인해봅시다.

<br>

컴파일 후 확인해보면 동일한 이름의 `.class` 파일이 생성되어 있습니다. 컴파일이 성공한거죠!

```bash
➜  java ls
test.class test.java

```

<br>

컴파일된 파일이 정상적으로 실행되는지 확인하려면 `java` 명령어를 사용하면 됩니다. 참고로 `javac` 와는 다르게 `java` 뒤의 `source file name` 에는 `.class`를 붙이면 실행하지 못합니다.

[java command 문서](https://docs.oracle.com/en/java/javase/11/tools/java.html#GUID-3B1CE181-CD30-4178-9602-230B800D4FAE)

```bash
➜  java java test 
hi
➜  java java test.class
오류: 기본 클래스 test.class을(를) 찾거나 로드할 수 없습니다.
원인: java.lang.ClassNotFoundException: test.class

```

`java` command 는 자바 프로그램을 실행하고, JRE(Java Runtime Environment 를 통해 클래스를 로딩하면서 클래스의 `main()` 메서드를 호출합니다. `main()` 메서드는 반드시 public, static 으로 선언되어야하고 반환 값은 없어야합니다.

### 참고

[oracle -  javac](https://docs.oracle.com/en/java/javase/11/tools/javac.html)

[자바 프로그래밍 - TCP School](http://www.tcpschool.com/java/java_intro_programming)


<br>



# 바이트코드(Bytecode)란
바이트코드는 고급 언어로 작성된 코드를 JVM 같은 가상머신이 이해할 수 있는 중간 코드로 컴파일한 것을 말합니다. 그리고 가상머신은 바이트코드를 각각의 OS 아키텍처에 맞는 기계어로 다시 컴파일 합니다.

처음에는 바이트라고 하길래 바이트..? 01로 이루어진 코드..? 인가 라고 생각했는데 아니더라고요. 사실 0과 1로 이루어진 코드는 바이너리코드이니 아예 착각을 했던거였죠. 컴파일러에 의해 변환되는 코드의 명령어 크기가 1바이트라서 바이트 코드라고 부른다고 합니다.

바이트코드는 중간에 JVM 같은 가상머신이 있으면 대체로 함께 존재한다고 보면 되는데 JVM 이 효율적으로 읽어올 수 있는 형식이 필요하기 때문에 사용된다고 합니다.

바이트코드라는 명칭은 Java, JVM 에서 대표적으로 사용되기는 하는데 다른 곳에서도 바이트코드라 지칭하는 경우가 있으므로 혼동을 피하기 위해 `Java 바이트코드` 라고 명시하는 경우가 많다고 합니다.

그리고 컴파일된 바이트코드는 인터프리터 방식으로 해석되는 인터프리터 언어라고 볼 수 있습니다. 바이트코드는 JIT를 통해 바이너리코드로 변환되어 컴퓨터가 읽을 수 있게 되는데요, 이번에는 JIT 컴파일러가 뭔지 알아보겠습니다.


### 참고

[바이트코드 - 나무위키](https://namu.wiki/w/%EB%B0%94%EC%9D%B4%ED%8A%B8%EC%BD%94%EB%93%9C)

<br>

# JIT 컴파일러란
JIT 컴파일(Just-in-time- compilation) 은 컴파일된 바이트코드를 기계어로 컴파일 해주는 친구인데요, JVM 의 실행엔진에 속해있으면서 런타임 시 실시간으로 바이트코드를 기계어로 변환해주는 역할을 합니다.

인터프러터 방식의 특성상 실시간으로 컴파일을 해야하기 때문에 정적 컴파일 방식보다 느릴 수 있는데 JIT 컴파일러는 반복되는 컴파일은 캐시에 저장해놓고 재사용 시 불러와 사용함으로써 실행 속도를 개선했습니다.

복잡한 최적화 과정은 이미 `.class` 로 컴파일 될 때 바이트코드 컴파일러가 대신 해줬고, 바이트코드 자체가 빠른 기계어 번역을 위해 설계되어 기존 인터프리터 언어들보다 빠르게 실행될 수 있습니다.

![](https://velog.velcdn.com/images/jomminii/post/4c76b866-5055-4bdc-b87f-746c88cd4f96/image.png)[이미지 출처 : [understanding jit compiler - aboullaite](https://aboullaite.me/understanding-jit-compiler-just-in-time-compiler/)]


### 참고
[JIT 컴파일 - 위키백과](https://ko.wikipedia.org/wiki/JIT_%EC%BB%B4%ED%8C%8C%EC%9D%BC)

[understanding jit compiler - aboullaite](https://aboullaite.me/understanding-jit-compiler-just-in-time-compiler/)

<br>

# JDK와 JRE의 차이

## JRE(Java Runtime Environment)
JRE는 자바의 런타임 환경에서 필요한 소프트웨어 묶음 입니다. 우리가 구현하지는 않았지만 사용할 수 있는 `List.class` 같은 것들을 사용할 수 있게 해줍니다. 자바 라이브러리들과 클래스로더, JVM 을 세트로 묶어서 다운받을 수 있습니다. 어떤 컴퓨터든 JRE 만 깔리면 자바 프로그램을 실행할 수 있습니다.

- Java libraries(prewritten code)
- Java Class Loader(load into JVM)
- Java Virtual Machine(JVM)

## JDK(Java Development Kit)
JDK에는 JRE 를 포함하고 자바 개발을 위해 필요한 프로그램들도 포함하고 있습니다. `javac`, `jar` 등을 제공하고 있습니다. [jdk-wiki](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94_%EA%B0%9C%EB%B0%9C_%ED%82%A4%ED%8A%B8) 에 더 많은 내용이 있습니다. 참고해주세요!
- JRE included
- Java Compiler

### 참고
[jdk-wiki](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94_%EA%B0%9C%EB%B0%9C_%ED%82%A4%ED%8A%B8)

[JRE vs JDK - IBM technology](https://www.youtube.com/watch?v=fDXMo3lX-Ug)

[JVM, JRE, JDK가 뭔가요 - 얄팍한 코딩사전](https://www.youtube.com/watch?v=VvVruEDCSSY)