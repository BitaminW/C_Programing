
### 전처리 지시자
전처리기는 프로그램을 컴파일할 때 컴파일 직전에 실행되는 별도에 프로그램이다. 이는 C의 컴파일 파이프라인이 다른 언어에 비해 최소 한 단계 이상을 더 갖는다.다른 프로그래밍 언어에서는 컴파일러로 소스 코드가 바로 전달되지만 C와 C++에서는 먼저 전처리를 해야합니다.
전처리의 목적은 전처리 지시자를 제거하고 이 지시자를 C 코드에 의해 생성된 동일한 내용으로 바꾸는 것입니다.또한 컴파일러로 보낼 준비가 된 최종 소스 파일을 준비하려는 목적도 있습니다. C 전처리기의 수행은 일련의 지시자를 사용해 통제하고 영향을 줄 수 있습니다. C 지시자는 헤더와 소스 파일 모두에서 # 문자로 시작하는 코드입니다.이 코드는 C 전처리기에만 의미가 있을 뿐 C 컴파일러에는 의미가 없습니다.

### 매크로
매크로는 다음과 같이 다양하게 활용할 수 있습니다.
+ 상수정의하기
+ C 함수를 작성하지 않고 함수로 사용하기
+ 루프 풀기
+ 헤더 가드
+ 코드 생성
+ 조건부 컴파일

#### 매크로 정의하기
매크로는 ***#define*** 지시자를 이용해 정의합니다. 각 매크로는 이름과 사용 가능한 매개변수 리스트를 갖습니다.
또한 값을 가지며, 이 값은 매크로 확장이라는 단계를 통해 전처리 단계에서 매크로의 이름으로 대체될 수 있습니다. 매크로는 ***#undef*** 지시자로 매크로의 정의를 제거할 수 있습니다.

```C
    #define ABC 5

    int main(int argc, char** argv){
        int x = 2;
        int y = ABC;
        int z = x + y;
        return 0;
    }
```
이 코드에서 ABC는 정숫값이나 정수 상수를 담는 변수가 아닌 ABC라는 매크로이며 해당하는 값은 5입니다.
매크로 확장 단계 이후 C 컴파일러로 보낸 코드의 결과물은 다음과 같습니다.

```C
    int main(int argc, char** argv){
    int x = 2;
    int y = 5;
    int z = x + y;
    return 0;
}
```

위 코드는 유요한 C언어 문법이므로 컴파일러에서 코드를 계속 컴파일할 수 있습니다.
또한 유사 함수 매크로도 정의할 수 있습니다.
```C
#define add(a, b) a + b

int main(){
    int x = 1;
    int y = 2;
    int z = add(a, b);
    return 0;
}
```

위 코드에서 add는 함수가 아닙니다. 이는 인수를 받는 *유사 함수 매크로*입니다. 전처리 이후 코드는 아래와 같이  치환됩니다.

```c
int z = x + y; // int z = add(a, b);
```

유사 함수 매크로는 입력 인수를 받을 수 있기 떄문에 함수를 모방할 수 있습니다. 즉, 자주 사용되는 로직을 함수에 넣는 것 대신, 이 로직을 유사 함수 매크로로 정의해서 사용할 수 있습니다.
이처럼 전처리 단계에서 매크로는 자주 사용되는 로직으로 대체되며, 함수를 새로 작성할 필요가 없어집니다.
매크로는 컴파일 단계 이전에만 존재합니다. 즉, 컴파일러가 이론적으로는 매크로에 관해 아무것도 모른다는 의미입니다.
매크로를 함수 대신 사용하기 위해서 꼭 알아야할 사항이 있는데 *컴파일러는 함수의 모든 것을 알고 있습니다. 함수는 C 문법의 일부이며 파스 트리 내에서 함수가 분석되기 때문입니다. 그러나 매크로는 전처리기 자체에 관해서만 알고 있는, C 전처리 지시자에 불과합니다.*

현대의 C컴파일러는 전처리기 지시자를 인지하고 있습니다. 전처리 단계에 관해 컴파일러가 아무것도 모른다고 흔히 알고 있지만 사실 컴파일러는 전처리 단계를 알고 있습니다.현대의 컴파일러는 전처리 단계에 들어가기 이전의 소스 코드를 알고 있습니다.

다음은 루프를 생성하는 매크로 입니다.
```C
#include <stdio.h>

#define PRINT(a) printf("%d\n", a);
#define LOOP(v, s, e) for (int v = s; v <= e; v++){
#define ENDLOOP }

int main(){
    
    LOOP(counter, 1, 10)
        PRINT(counter)
    ENDLOOP
    
    return 0;
}
```
위 코드는 잘못된 C 코드입니다. 하지만 전처리 이후, 올바른 C 코드를 얻게 되며 문제없이 컴파일됩니다.
다음은 전처리 결과입니다.

```C
int main(){
    
    for(int counter = 1; 1 <= 10; counter++){
       printf("%d\n", a);
    }
    return 0;
}
```

최종 전처리된 코드에는 #으로 시작하는 전처리 지시자가 없다는 사실을 눈여겨봐야 합니다.이는 #include 지시자가 자신이 가리키는 파일의 내용으로 대체되었다는 뜻입니다.

#### # 연사자와 ## 연산자
+ #연산자는 매크로를 확장할 때 매개변수를 따옴표로 둘러싼 문자 형태로 반환합니다.
+ ##연산자는 매크로 정의에서 매개변수와 다른 요소를 문자열로 결합해 변수 이름을 만듭니다.

```C
#define CMD(NAME) \
    char NAME ## _cmd[256] = ""; \
    strcpy(NAME ## _cmd, #NAME)

int main(){
    CMD(copy)
    CMD(paste)
    CMD(cut)

    char cmd[256]; 
}
```
위 코드가 전처리 단계를 거치면 아래와 같은 코드가 됩니다.

```C
int main(){
    char copy_cmd[256] = ""; strcpy(copy_cmd, "copy");
    char paste_cmd[256] = ""; strcpy(paste_cmd, "copy");
    char cut_cmd[256] = ""; strcpy(cut_cmd, "copy");
    char cmd[256];
}
```


### 가변 인자 매크로
가변 인자 매크로는 입력 인수에 관한 변수를 받을 수 있습니다. 동일한 가변 인자 매크로가 떄로는 2개 혹은 4개,8개의 가변 인수를 받기도 합니다. 동일한 매크로를 다른 용도로 사용하는 경우, 인수의 개수를 정확히 모른다면 가변 인자 매크로가 매우 유용합니다.

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define VERSION "2.3.4"

#define LOG_EEROR(format, ...) \
    fprintf(stderr, format, __VA_ARGS__)

int main(int argc, char** argv){
    if(argc < 3){
        LOG_ERROR("Invalid numver of arguments for version %s\n", VERSION);
        exit(1);
    }
    if(strcmp(argv[1], "-n") != 0){
        LOG_ERROR("%s is a wrong param at index %d for version %s.", argv[1], 1, VERSION);
        exit(1);
    }
    return 0;
}
```
위 코드가 전처리 단계를 거치면

```C
int main(int argc, char** argv){
    if(argc < 3){
        fprintf(stderr, "Invalid numver of arguments for version %s\n", "2.3.4");
        exit(1);
    }
    if(strcmp(argv[1], "-n") != 0){
        fprintf(stderr, "%s is a wrong param at index %d for version %s.", argv[1], 1, "2.3.4");
        exit(1);
    }
    return 0;
}
```

위 코드에서 __VA_ARGS__ 는 아직 매개변수에 할당되지 않은 나머지 입력 인수로 모두 교체하도록 전처리기로 지시합니다. 앞의 예제에서 두 번쨰로 사용된 LOG_ERROR를 보면, 매크로 정의에 따라 인수 argv[1], 1, VERSION은 어느 매개변수로도 할당되지 않은 입력 인수입니다. 그러므로 이들은 매크로 확장시 __VA_ARGS__로 대신합니다.

위 코드에서 매크로 정의에서 세미콜론을 붙이지 않기 떄문에 사용을 위해선 개발자가 꼭 세미콜론을 붙여줘야 합니다.

### 매크로의 장점과 단점

#### 장점
+ 실행 속도가 빠르다.
+ 자료형에 따라서 별도의 함수를 정의하지 않아도 된다.


#### 단점
+ 매크로를 남발할 경우 코드 가독성이 떨어진다.
+ 문법 오류가 발생한 곳을 찾기 위해 컴파일 오류를 이용하는데 컴파일 전에 전처리가 일어나기에 디버깅 과정이 힘들다.
+ 실행 파일이 커진다.
