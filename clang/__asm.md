# __asm

- __asm 키워드는 inline assembler를 호출한다.
- c / c++ code 어디에서든 `__asm ( assembly-instruction-codes )` 를 작성하면 inline assmbler를 호출하여 assembly instruction을 실행하게 된다.
- `__asm { assembly-instruction-codes }`의 형태는 intel style inline assembly이다.
- clang / llvm 컴파일러에서는 intel style로 작성하면 에러가 발생하기 때문에 중괄호 대신 소괄호로 작성하는 것이 **표준**이다.
- gcc에서는 함수 내부에 inline assembler를 호출하는 code가 있다면 `__asm [return type] [function name] [parameter] {function code...}` 형태로 작성하여 컴파일러에게 명시해줄 수 있었다.
- 하지만 clang / llvm 컴파일러에서는 `__asm [return type] [function name] [parameter] {function code...}` 형태로 작성을 하게 되면 컴파일러에서는 에러가 발생한다.
- clang / llvm 컴파일러에서는 `__attribute__((naked))`를 함수 선언부에 사용하여 해당 함수 내부에 inline assembler를 호출하는 code가 있음을 컴파일러에게 명시해줄 수 있다.
  
    ```c
    __attribute__((naked)) void foo (int i);
    ```