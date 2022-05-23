# LTO (Link Time Optimization)

LTO란 interprocedural optimization의 형태로 프로그램을 linking 하는 과정에서 최적화하는 방법을 의미한다. 

## Multi-file Optimization
컴파일 단계에서 각각의 소스파일들을 최적화된 Object로 만드는 방법을 의미한다. 

![Multi-file Optimization](https://community.arm.com/resized-image/__size/1040x0/__key/communityserver-blogs-components-weblogfiles/00-00-00-21-12/5367.CL.Compiler-Linker.jpg)

Arm Compiler 5에서는 Multi-file Optimization 방법에서 한 단계 더 나아가, 각각의 Source File을 최적화된 하나의 Source File로 만들어 컴파일을 진행한다.

![Arm Compiler 5](https://community.arm.com/resized-image/__size/1040x0/__key/communityserver-blogs-components-weblogfiles/00-00-00-21-12/4762.Compiler-Preprocessor.Compiler.Linker.jpg)

이 방법의 주요 이점은 각각의 Source File을 컴파일 할 때는 Compiler가 볼 수 없었던 symbol 정보들을, 하나의 소스코드로 집계함으로써 inline 함수로 만들 수 있는 함수를 inline 함수로 만들 수 있다는 이점이 있다. 다만, 컴파일 단계에서 많은 시간과 메모리를 필요로 하다는 것이 단점이다.

## Link Time Optimization
Arm Compiler 6에서는 Link Time Optimization을 제공한다. LTO option을 사용하게 되면 각각의 Source File을 Compile을 했을 때 object file이 아닌 bytecode를 만들어준다. bytecode에는 기존의 object file보다 최적화에 사용할 더 많은 정보(module dependency)를 가지고 있는데 Linker가 이 정보를 토대로 최종적으로 최적화된 image 파일을 만들게 된다. 

![LTO](https://community.arm.com/resized-image/__size/1040x0/__key/communityserver-blogs-components-weblogfiles/00-00-00-21-12/6837.Enchanced.Compiler.EnhancedLinker.jpg)

LTO가 항상 개발자가 원하는 최적화 결과를 가져오는 것은 아니다. Linker가 Project 내의 모든 Source File에 대해 Symbol 정보들을 볼 수 있어서, 프로그램의 출력에 영향을 미치지 않는 code를 식별하는데 매우 효과적일 수는 있다. 이러한 code들을 최적화하여 code size를 줄일 수 있지만, 함수를 inline하여 code size가 증가할 수도 있다. 따라서 LTO는 Project에 사용된 code를 어떻게 구성하냐에 따라 code size가 크게 줄 수도 있고 엄청난 이점이 있지 않을 수도 있다.


## LTO를 적용하기 위해 필요한 작업들

LTO 옵션을 사용하면 Scatter File에 object file을 메모리 내의 어떤 Section에 배치하는 `foo.o (+RO)`와 같은 명령어가 유효하지 않는다. 만약 이를 바꾸지 않고 사용하게 된다면 `L6314W: No section matches pattern <module>(<section>).`와 같은 경고메시지를 볼 수 있다. LTO 옵션을 사용하면서 Scatter File을 사용하려면 다음과 같이 고쳐주어야 한다.

### Makefile 수정

1. Compile 옵션에 --flto 추가한다.
2. Linker 옵션에 lto, lto level 추가한다.
3. lto level은 O1 ~ O3, Omax, Omin 등이 있으나 ARM에서는 성능과 Debugging의 Trade off를 고려하여 O1을 추천하고 있다. 
4. Default Optimization level을 Oz → Omin으로 변경한다. ARM에서 LTO 적용을 하게 되면 Oz 대신 Omin을 사용할 것을 권장하고 있다. Virtual Function Elimination을 고려한다면 더더욱 Omin을 적용해야 한다.
5. 라이브러리를 사용한다면 Archive 제거하고 모든 Object를 직접 Link 한다.

### Source File 수정

1. Background Section Source File에 직접 적용한다.
2. Linker Input으로 여러 .o 파일들을 넣더라도, LTO 옵션을 적용하게 되면 단 하나의 object 파일만 나오게 된다. 따라서 LTO 적용 후에는 Scatter File을 통한 object 파일 Section 배치는 불가능하므로, Scatter 대신에 직접 Section을 변경하는 코드를 프로젝트 내의 모든 Source File에 넣어야 한다. 

### Scatter File 수정

1. object 파일 별 Section 적용 제거한다.
2. Scatter File 내의 object 파일 별 Section은 이제 유효하지 않으므로 제거해야 한다.
